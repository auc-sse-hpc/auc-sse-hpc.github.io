# Slurm Job Submission Guide

The AUC SSE HPC cluster uses the **Slurm Workload Manager** to allocate compute resources, prioritize queues, and execute jobs across the cluster nodes.

---

## Slurm Partitions

Jobs must be submitted to an appropriate partition based on runtime and resource requirements:

| Partition | Default Walltime | Max Walltime | Default Memory / Core | Target Workload |
| :--- | :--- | :--- | :--- | :--- |
| **`debug`** *(Default)* | 30 minutes | 2 hours | 8 GB | Script testing, rapid prototyping, and interactive debugging. |
| **`cpu`** | 1 hour | 48 hours | 8 GB | Standard multi-threaded (OpenMP) and distributed (MPI) batch jobs. |
| **`gpu`** | 1 hour | 24 hours | 4 GB | Accelerated AI/ML, PyTorch, CUDA, and GPU-accelerated modeling. |
| **`long`** | 4 hours | 7 days | 8 GB | Extended simulations (limited concurrent running jobs per user). |

---

## Submitting Batch Jobs (`sbatch`)

Batch jobs are submitted via shell scripts containing `#SBATCH` directives that instruct Slurm how many resources to allocate.

### Common `#SBATCH` Directives

| Directive | Description | Example |
| :--- | :--- | :--- |
| `--job-name=<name>` | Descriptive name for the job | `#SBATCH --job-name=sim_run1` |
| `--partition=<part>` | Target partition (`debug`, `cpu`, `gpu`, `long`) | `#SBATCH --partition=cpu` |
| `--nodes=<N>` | Number of physical nodes to allocate | `#SBATCH --nodes=1` |
| `--ntasks=<n>` | Total number of tasks (MPI processes) | `#SBATCH --ntasks=1` |
| `--cpus-per-task=<c>` | CPU cores per task (for multi-threading) | `#SBATCH --cpus-per-task=8` |
| `--mem=<size>` | Total memory required per node | `#SBATCH --mem=32G` |
| `--time=<d-hh:mm:ss>` | Maximum walltime limit | `#SBATCH --time=04:00:00` |
| `--gres=gpu:<n>` | Generic resources (for GPU jobs) | `#SBATCH --gres=gpu:1` |
| `--output=<file>` | File to capture standard output (`%j` is Job ID) | `#SBATCH --output=%x_%j.out` |
| `--error=<file>` | File to capture standard error | `#SBATCH --error=%x_%j.err` |

---

### Example 1: Multi-Threaded CPU Job

Save the following script as `run_cpu.sh`:

```bash
#!/bin/bash
#SBATCH --job-name=cpu_multithread
#SBATCH --partition=cpu
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=32G
#SBATCH --time=04:00:00
#SBATCH --output=%x_%j.out
#SBATCH --error=%x_%j.err

# Set OpenMP thread count to match allocated CPUs
export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK

echo "Job started on $(hostname) at $(date)"
echo "Allocated CPU cores: $SLURM_CPUS_PER_TASK"

# Run your computational workflow
./my_simulation_program

echo "Job finished at $(date)"
```

Submit the job to the queue:
```bash
sbatch run_cpu.sh
```

---

### Example 2: GPU Accelerated Job (PyTorch / CUDA)

Save the following script as `run_gpu.sh`:

```bash
#!/bin/bash
#SBATCH --job-name=gpu_training
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=32G
#SBATCH --time=06:00:00
#SBATCH --output=%x_%j.out
#SBATCH --error=%x_%j.err

echo "Running on node: $SLURMD_NODENAME"
echo "Allocated GPU device: $CUDA_VISIBLE_DEVICES"

# Verify GPU availability
nvidia-smi

# Execute Python / PyTorch training script
python3 train_model.py

echo "GPU job completed at $(date)"
```

Submit the job:
```bash
sbatch run_gpu.sh
```

---

### Example 3: Job Arrays (Parametric Sweeps)

When running hundreds of similar tasks with different inputs or parameters, use **Slurm Job Arrays** instead of submitting individual scripts in a loop:

```bash
#!/bin/bash
#SBATCH --job-name=param_sweep
#SBATCH --partition=cpu
#SBATCH --cpus-per-task=2
#SBATCH --mem=8G
#SBATCH --time=01:00:00
#SBATCH --array=1-50%10       # 50 total tasks, max 10 running simultaneously
#SBATCH --output=logs/task_%A_%a.out

echo "Processing task ID $SLURM_ARRAY_TASK_ID on $(hostname)"
python3 process_sample.py --input-file data/sample_${SLURM_ARRAY_TASK_ID}.dat
```

---

## Interactive Sessions (`srun`)

For interactive development, debugging, or code compilation requiring compute node resources, launch an interactive shell:

```bash
srun --partition=debug --cpus-per-task=4 --mem=16G --time=01:00:00 --pty bash
```

This immediately allocates resources and opens a bash prompt on an available compute node. When you are done, type `exit` to release the resources.

---

## Monitoring and Managing Jobs

### Check Queue Status
```bash
# View all jobs currently in the queue
squeue

# View only your submitted jobs
squeue -u $USER
```

### Common Job States:
* `PD` (Pending): Waiting for requested resources or higher-priority jobs.
* `R` (Running): Currently executing on compute nodes.
* `CG` (Completing): Finishing execution and flushing I/O buffers.
* `CD` (Completed): Job exited successfully with return code 0.
* `F` (Failed): Job exited with a non-zero status or crashed.
* `OOM` (Out Of Memory): Job exceeded its requested `--mem` and was terminated by the cgroup OOM killer.

### Cancel a Job
```bash
scancel <job_id>
```

### Review Historical Job Resource Usage
Query the Slurm accounting database for completed jobs:
```bash
sacct -j <job_id> --format=JobID,JobName,Partition,AllocCPUS,Elapsed,MaxRSS,State,ExitCode
```
> **Tip:** Inspecting `MaxRSS` helps you accurately tune future `--mem` requests so you do not over-request or run out of memory.
