# Storage & Quotas

This guide describes the storage layout on the AUC SSE HPC cluster, quota guidelines, and best practices for managing your data.

---

## Storage Layout Overview

The cluster provides different tiers of storage suited for specific workloads:

| Storage Location | Path | Intended Purpose | Backup & Retention |
| :--- | :--- | :--- | :--- |
| **Home Directory** | `/home/<username>` | Source code, configuration files (`.bashrc`, `.ssh`), scripts, and small Conda envs. | Permanent storage; quota-managed. |
| **Fast / Scratch Storage** | `/data` or `/scratch` | Active computation inputs, large raw datasets, model weights, and simulation output files. | High-performance scratch; no automatic long-term backup. |

!!! tip "Use Fast Scratch for Compute I/O"
    Directing heavy read/write operations (such as checkpoint saving, temporary simulation files, or unpacked datasets) to `/data` or `/scratch` significantly accelerates your job performance and prevents your home directory from hitting quota limits.

---

## Checking Your Disk Usage

Always monitor your storage consumption to avoid unexpected job failures due to disk exhaustion:

### Check Overall Filesystem Usage
```bash
df -h ~
```

### Identify Largest Directories in Your Workspace
To find which directories are consuming the most space:
```bash
du -sh ~/* 2>/dev/null | sort -h
```

To inspect subdirectories within your current working directory:
```bash
du -h --max-depth=1 . | sort -h
```

---

## Data Management & Hygiene Best Practices

1. **Do not store uncompressed archives in Home:**
   Always compress old datasets and finished simulation logs using `tar`:
   ```bash
   # Compress a completed project directory
   tar -czvf project_backup_$(date +%F).tar.gz ./project_dir/

   # Extract an archive
   tar -xzvf project_backup.tar.gz
   ```

2. **Clean up Conda and Pip caches:**
   Package managers maintain large package cache tarballs by default. Periodically purge them:
   ```bash
   # Clean conda package caches
   conda clean --all -y

   # Clean pip cache
   pip cache purge
   ```

3. **Delete unnecessary simulation checkpoints:**
   Many scientific packages (e.g., VASP `WAVECAR`/`CHGCAR`, molecular dynamics trajectory frames, deep learning checkpoints) produce multi-gigabyte outputs. Delete intermediate iterations once convergence is confirmed.

---

## Transferring Files

You can transfer files between your personal machine and the HPC cluster using standard secure file transfer utilities:

### Using `scp` (Secure Copy)

* **Upload from local machine to HPC:**
  ```bash
  scp -r ./my_local_data <username>@<login_host>:~/
  ```

* **Download results from HPC to local machine:**
  ```bash
  scp <username>@<login_host>:~/run_cpu_1234.out ./
  ```

### Using `rsync` (Recommended for Large Transfers)

`rsync` provides resumable, bandwidth-efficient file transfers:

```bash
# Sync local directory to cluster
rsync -avzP ./datasets/ <username>@<login_host>:~/datasets/

# Sync results back to local machine
rsync -avzP <username>@<login_host>:~/results/ ./local_results/
```
> Options: `-a` (archive permissions), `-v` (verbose), `-z` (compress in transit), `-P` (show progress and resume interrupted transfers).

---

## Next Steps

Explore how to load Environment Modules and execute rootless Apptainer containers in the [Software Modules & Containers Guide](software.md).

