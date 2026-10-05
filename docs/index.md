# Welcome to the AUC SSE HPC Cluster

Welcome to the **American University in Cairo (AUC) School of Sciences and Engineering (SSE) High-Performance Computing (HPC) Cluster** user guide.

The HPC cluster provides high-throughput computational resources, large memory nodes, and GPU acceleration to support scientific research and academic projects across the AUC community.

---

## User Guide Overview

This portal contains essential guides and best practices for navigating and running workloads on the cluster:

* **[User Guide Overview](user/index.md):** High-level summary of all available documentation sections.
* **[Getting Started](user/getting_started.md):** Information on network access, SSH connections, key setup, and login node etiquette.
* **[Slurm Job Submission](user/slurm_jobs.md):** How to run batch jobs, request CPU and GPU resources, and start interactive sessions using Slurm.
* **[Storage & Quotas](user/storage.md):** Overview of user home directories, high-speed scratch space, and data hygiene best practices.
* **[Software & Containers](user/software.md):** Using environment modules, managing Conda environments, and running Apptainer containers.

---

## Important Cluster Etiquette

To ensure fair and reliable resource access for all researchers, please adhere to these core rules:

1. **Do not run computation on the login node:** Login nodes are reserved solely for compiling code, editing files, managing data, and submitting Slurm jobs. All computational workloads must be submitted via Slurm (`sbatch` or `srun`).
2. **Specify realistic resource requests:** Request only the CPU cores, memory, and walltime your job actually requires. Over-requesting delays your job and blocks resources for other users.
3. **Practice good storage hygiene:** Do not store massive intermediate or raw temporary datasets in your home directory. Use high-speed scratch/data directories and archive old outputs promptly.

---

## Getting Help & Support

If you need an HPC account, experience technical issues, or require assistance installing specialized software libraries, please contact the SSE HPC administration team through departmental channels or submit an issue via the [AUC SSE HPC GitHub Issues](https://github.com/auc-sse-hpc/auc-sse-hpc.github.io/issues).
