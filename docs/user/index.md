# User Guide

Welcome to the **AUC SSE HPC Cluster User Guide**. This guide provides researchers, faculty, and students with essential instructions for accessing cluster resources, running computational jobs, managing storage, and configuring software environments.

---

## Guide Sections

Explore the documentation across the following sections:

| Section | Description |
| :--- | :--- |
| **[Getting Started](getting_started.md)** | Network prerequisites (campus network & FortiClient VPN), SSH login, ED25519 key setup, and login node rules. |
| **[Slurm Job Submission](slurm_jobs.md)** | Cluster partitions, `#SBATCH` directives, batch script templates (CPU multithread, GPU PyTorch, Job Arrays), interactive sessions, and queue monitoring. |
| **[Storage & Quotas](storage.md)** | Home directory vs fast scratch/data storage, checking disk usage (`df`, `du`), data archiving (`tar`), cache cleanup, and file transfers (`scp`, `rsync`). |
| **[Software & Containers](software.md)** | Loading Environment Modules (`module load`), managing isolated Conda environments, and running rootless Apptainer (Singularity) containers with GPU acceleration. |

---

## Need Support?

If you encounter issues or have questions regarding cluster usage, please refer to [Getting Help & Support](../index.md#getting-help-support) or open an issue on the [GitHub repository](https://github.com/auc-sse-hpc/auc-sse-hpc.github.io/issues).
