# AUC SSE HPC Documentation Portal

Official documentation repository for the **American University in Cairo (AUC) School of Sciences and Engineering (SSE) High-Performance Computing (HPC) Cluster**.

🌐 **Live Documentation:** [https://auc-sse-hpc.github.io/](https://auc-sse-hpc.github.io/)

---

## User Guide Contents

* **[Getting Started](https://auc-sse-hpc.github.io/user/getting_started/)**: Network requirements (campus & FortiClient VPN), SSH login, ED25519 key authentication, and login node rules.
* **[Slurm Job Submission](https://auc-sse-hpc.github.io/user/slurm_jobs/)**: Partition overview, `#SBATCH` batch templates (CPU multithreading, GPU PyTorch, and Job Arrays), interactive shells (`srun`), and job management (`squeue`, `scancel`, `sacct`).
* **[Storage & Quotas](https://auc-sse-hpc.github.io/user/storage/)**: Home directory vs high-performance scratch/data storage, quota monitoring, data compression, and remote transfers (`scp`, `rsync`).
* **[Software Modules & Containers](https://auc-sse-hpc.github.io/user/software/)**: Loading Environment Modules (`module load`), managing isolated Conda environments, and running rootless Apptainer (Singularity) containers with GPU acceleration.

---

## Local Development & Build

To test and build the documentation locally:

```bash
# Install mkdocs and mkdocs-material
pip install mkdocs mkdocs-material

# Run local preview server
mkdocs serve

# Build production site
mkdocs build
```
