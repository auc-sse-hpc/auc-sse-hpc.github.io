# Software Modules & Containers

The AUC SSE HPC cluster provides scientific software via **Environment Modules**, user-managed **Conda environments**, and **Apptainer (Singularity) containers**.

---

## Environment Modules

The Environment Modules system enables users to dynamically modify their shell environment (e.g., `PATH`, `LD_LIBRARY_PATH`) to load specific compilers, libraries, and scientific toolchains without version conflicts.

### Common Module Commands

| Command | Action |
| :--- | :--- |
| `module avail` | List all available software packages and versions |
| `module load <package>/<version>` | Load a specific software module into your current shell |
| `module list` | Display all currently loaded modules in your session |
| `module unload <package>` | Unload a specific package |
| `module purge` | Remove all currently loaded modules |
| `module show <package>` | Inspect the environment variables modified by a module |

### Using Modules in Slurm Batch Scripts

When submitting jobs via `sbatch`, always explicitly load the required modules inside your job script:

```bash
#!/bin/bash
#SBATCH --job-name=module_job
#SBATCH --partition=cpu
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4
#SBATCH --mem=16G
#SBATCH --time=02:00:00

# Purge any inherited shell modules for reproducibility
module purge

# Load specific compiler or toolchain
module load gcc/11.2
module load openmpi/4.1

# Execute your application
mpirun -np 4 ./my_mpi_program
```

---

## Python & Conda Environments

For Python-based workflows, we recommend creating isolated Conda environments to manage packages without requiring administrative (`root`) privileges.

### Setting Up a Conda Environment

1. **Create an environment with a specific Python version:**
   ```bash
   conda create --name my_project python=3.10 -y
   ```

2. **Activate the environment:**
   ```bash
   conda activate my_project
   ```

3. **Install dependencies:**
   ```bash
   pip install numpy scipy pandas scikit-learn
   # Or install via conda:
   conda install -c conda-forge matplotlib
   ```

4. **Deactivate when finished:**
   ```bash
   conda deactivate
   ```

### Running Conda in Slurm Jobs

In your batch script, initialize Conda before activating your environment:

```bash
#!/bin/bash
#SBATCH --job-name=conda_workflow
#SBATCH --partition=cpu
#SBATCH --cpus-per-task=4
#SBATCH --mem=16G
#SBATCH --time=01:00:00

# Initialize conda for non-interactive shell sessions
eval "$(conda shell.bash hook)"

# Activate target environment
conda activate my_project

# Run workflow
python3 main_pipeline.py
```

---

## Containerization with Apptainer (Singularity)

**Apptainer** (formerly Singularity) is installed cluster-wide. It enables you to run complex software packages or Docker images inside a rootless, reproducible container environment.

### Why Apptainer?
* **Security:** Runs entirely in user space without requiring root privileges or Docker daemon access.
* **Compatibility:** Native integration with Slurm, MPI, and NVIDIA GPUs.
* **Direct Docker Hub Support:** Pulls and converts Docker images automatically.

### Common Apptainer Workflows

#### 1. Download and Convert a Container
Download an image from Docker Hub and convert it to a Singularity Image File (`.sif`):
```bash
# Pull an official PyTorch container
apptainer pull docker://pytorch/pytorch:2.2.0-cuda12.1-cudnn8-runtime
```

#### 2. Execute a Command Inside the Container
Run a command inside the container using `apptainer exec`:
```bash
# For CPU jobs:
apptainer exec pytorch_latest.sif python3 my_script.py

# For GPU jobs (add --nv flag for NVIDIA GPU acceleration):
apptainer exec --nv pytorch_latest.sif python3 -c "import torch; print(torch.cuda.is_available())"
```

#### 3. Mount Custom Host Directories
By default, Apptainer binds your current working directory and `/home`. To access external data paths (such as `/data` or `/scratch`), pass the `--bind` flag:
```bash
apptainer exec --bind /data:/data pytorch_latest.sif python3 /data/train.py
```

#### 4. Apptainer Inside a Slurm GPU Job Script
```bash
#!/bin/bash
#SBATCH --job-name=apptainer_gpu
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1
#SBATCH --cpus-per-task=8
#SBATCH --mem=32G
#SBATCH --time=04:00:00
#SBATCH --output=%x_%j.out

# Run container with GPU passthrough
apptainer exec --nv --bind /data:/data pytorch_2.2.0-cuda12.1-cudnn8-runtime.sif python3 /data/train.py
```

---

## Related Guides

* Return to the [User Guide Overview](index.md)
* Learn more about [Slurm Job Submission](slurm_jobs.md)
* Review [Storage & Quotas](storage.md)

