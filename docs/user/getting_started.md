# Getting Started

This guide explains how to connect to the AUC SSE HPC cluster, set up your SSH keys, and navigate your first session.

---

## Network Prerequisites

Access to the HPC cluster is restricted to authorized university networks. You must connect from:

* **On-Campus Network:** Connected directly to the AUC campus wired or wireless network.
* **Off-Campus Network:** Connected via the official **AUC FortiClient VPN** before establishing an SSH connection.

---

## SSH Access

Access to the cluster is provided via Secure Shell (SSH).

### Connecting to the Login Node

Open a terminal (Linux/macOS) or PowerShell / Windows Terminal (Windows) and run:

```bash
ssh <username>@<login_host>
```

> Replace `<username>` with your assigned university username and `<login_host>` with the cluster login hostname or IP address provided by the HPC administrators.

### Setting Up SSH Key Authentication (Recommended)

SSH keys eliminate the need to type your password repeatedly and enhance account security:

1. **Generate an SSH key pair** on your local computer (if you don't already have one):
   ```bash
   ssh-keygen -t ed25519 -C "your_email@aucegypt.edu"
   ```
   *(Press Enter to accept default location and optionally set a passphrase).*

2. **Copy your public key to the cluster**:
   ```bash
   ssh-copy-id <username>@<login_host>
   ```

3. **Verify passwordless login**:
   ```bash
   ssh <username>@<login_host>
   ```

---

## Login Node Etiquette

The node you land on after SSH login is a **shared login/gateway node**.

!!! warning "Do Not Run Computational Jobs on Login Nodes"
    The login node is shared by all active researchers and students. Intensive computations, long-running Python/Jupyter scripts, and heavy data processing executed directly on the login node will degrade system responsiveness for all users and may be automatically terminated by system administrators.

| Permitted on Login Nodes | Must be Run on Compute Nodes (via Slurm) |
| :--- | :--- |
| Editing code and scripts (`nano`, `vim`, `code-server`) | Running Python / PyTorch / TensorFlow scripts |
| Managing files, directories, and Git repos | Molecular dynamics, docking, DFT simulations |
| Light, single-threaded software compilation | Heavy multi-threaded builds (`make -j`) |
| Submitting and monitoring Slurm jobs (`sbatch`, `squeue`) | Long-running interactive data analysis |

---

## First Steps & Verification

Once logged in, verify your environment and check cluster availability:

### View Cluster Partitions & Nodes
```bash
sinfo
```
This displays available partitions (`debug`, `cpu`, `gpu`, `long`) and the status of compute nodes (`idle`, `alloc`, `mix`, `down`).

### View the Active Queue
```bash
squeue
```
To view only your jobs:
```bash
squeue -u $USER
```

---

## Next Steps

Now that you are connected, proceed to the [Slurm Job Submission Guide](slurm_jobs.md) to learn how to write batch scripts and submit your computational workloads.
