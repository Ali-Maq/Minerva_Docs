# Minerva HPC System Architecture and Command Reference

## System Architecture Overview

### Minerva HPC Cluster Architecture

```
MINERVA HPC CLUSTER STRUCTURE
============================

Internet → Campus Network → VPN (if off-campus) → Login Nodes → Compute Nodes
    ↓              ↓              ↓                    ↓              ↓
[External]    [On-campus]    [Off-campus]        [Interactive]   [Production]
             [Direct Access]  [F5 Big-IP VPN]     [Shell Access]  [Job Execution]

LOGIN NODES (Entry Points):
┌─────────────────────────────────────────────────────────────┐
│ minerva.hpc.mssm.edu (round-robin DNS)                     │
│ ├── minerva12.hpc.mssm.edu                                 │
│ ├── minerva13.hpc.mssm.edu                                 │
│ └── minerva14.hpc.mssm.edu                                 │
│                                                             │
│ Purpose: Job submission, file management, code development │
│ Resources: Limited CPU, no GPU, shared among all users     │
│ Storage Access: Home, Work, Projects, Scratch              │
└─────────────────────────────────────────────────────────────┘
                    ↓ (Job Scheduler: LSF)
COMPUTE NODES (Production Workhorses):
┌─────────────────────────────────────────────────────────────┐
│ CPU NODES                     │ GPU NODES                   │
│ ├── Standard compute          │ ├── V100 nodes (legacy)     │
│ ├── High memory (himem)       │ ├── A100 nodes (current)    │
│ └── Interactive nodes         │ ├── H100 nodes (newest)     │
│                               │ └── L40S nodes (specialized)│
│                                                             │
│ Purpose: Heavy computation, model training/inference       │
│ Access: Only through job scheduler (LSF)                   │
│ Network: Limited internet (proxy required)                 │
└─────────────────────────────────────────────────────────────┘
```

### Data Flow Architecture

```
USER WORKFLOW DATA FLOW
======================

1. AUTHENTICATION & ACCESS:
   User → VPN (if needed) → Login Node → Authentication (Password + VIP)
   
2. ENVIRONMENT SETUP:
   Login Node → Module System → Conda Environment → Package Installation
   
3. RESOURCE ALLOCATION:
   Login Node → LSF Scheduler → Queue → Node Assignment → Resource Allocation
   
4. COMPUTATION:
   Allocated Node → Model Loading → Data Processing → Result Generation
   
5. DATA STORAGE:
   Results → Work Directory → Backup/Archive → Final Submission

STORAGE HIERARCHY:
┌─────────────────────────────────────────────────────────────┐
│ /hpc/users/$USER (Home - 30GB, Backed up, Slow NFS)        │
│ ├── Configuration files (.bashrc, .ssh, etc.)              │
│ └── Small scripts and personal files                       │
│                                                             │
│ /sc/arion/work/$USER (Work - 100GB, Fast GPFS, Not backed) │
│ ├── Conda environments                                     │
│ ├── Model cache                                            │
│ ├── Data processing scripts                                │
│ └── Intermediate results                                   │
│                                                             │
│ /sc/arion/scratch/$USER (Scratch - Shared 100TB, 14-day)   │
│ ├── Temporary large files                                  │
│ ├── Job intermediate outputs                               │
│ └── Auto-purged after 14 days                              │
│                                                             │
│ /sc/arion/projects/$PROJECT (Project - Paid, Not backed)   │
│ ├── Shared research data                                   │
│ ├── Collaborative workspaces                               │
│ └── Long-term research storage                             │
└─────────────────────────────────────────────────────────────┘
```

## Complete HPC Command Reference

### 1. Authentication and Login Commands

```bash
# PRIMARY LOGIN COMMAND
ssh username@minerva.hpc.mssm.edu
# Why: SSH (Secure Shell) provides encrypted connection to HPC
# What happens: 
# - Establishes secure tunnel through campus network
# - Redirects to one of login nodes (minerva12/13/14)
# - Prompts for password + 6-digit VIP token
# - Lands you on shared login node

# WITH X11 FORWARDING (for GUI applications)
ssh -X username@minerva.hpc.mssm.edu
# Why: -X enables X11 forwarding for graphical applications
# Use case: Running RStudio, plotting, GUI tools
# Requirement: X11 server on local machine (XQuartz for Mac)

# SPECIFIC LOGIN NODE (if needed)
ssh username@minerva12.hpc.mssm.edu
# Why: Sometimes need specific node (e.g., reconnect to screen session)
# When: Login node crashed, need to access specific running process
```

### 2. Module System Commands

```bash
# LOAD MODULE SYSTEM
ml anaconda3/2024.06
# Full form: module load anaconda3/2024.06
# Why: Minerva uses Lmod for software management
# What it does:
# - Adds conda to PATH
# - Sets up environment variables
# - Provides access to pre-installed software stack
# - Avoids conflicts between different software versions

# LIST AVAILABLE MODULES
ml avail
# Shows all available software modules
# Organized by category (compilers, libraries, applications)

# SEARCH FOR SPECIFIC SOFTWARE
ml spider python
# Why: Finds all versions of python available
# Better than 'avail' for specific software lookup

# LIST CURRENTLY LOADED MODULES
ml list
# Shows what's currently in your environment
# Helps debug path/version conflicts

# UNLOAD MODULES
ml purge
# Removes all loaded modules
# Why: Clean slate for troubleshooting
# Use before loading conflicting software

# LOAD CUDA (for GPU work)
ml cuda/12.4.0
# Why: Provides CUDA runtime for GPU computing
# Must match GPU driver version on compute nodes
# Required for PyTorch GPU operations
```

### 3. Storage and File Management

```bash
# CHECK STORAGE QUOTAS
quota -s
# Shows home directory usage and limits
# Why: Home has 30GB hard limit, need to monitor

# CHECK GPFS QUOTAS (work/scratch/projects)
showquota -h
# Shows usage for all GPFS filesystems
# More detailed than quota command

# GPFS-SPECIFIC QUOTA CHECK
showquota -u $USER
# User-specific quota information
# Shows usage across all GPFS storage types

# CREATE WORK DIRECTORY STRUCTURE
mkdir -p /sc/arion/work/$USER/{scripts,data,models,results}
# Why: Organize work in fast GPFS storage
# Structure helps with data management

# CHECK FILESYSTEM TYPE AND PERFORMANCE
df -h /sc/arion/work/$USER
# Shows filesystem info and available space
# GPFS vs NFS performance characteristics

# SET FILE PERMISSIONS (for collaboration)
chmod 755 /sc/arion/work/$USER
setfacl -m u:colleague:rx /sc/arion/work/$USER
# Why: Control access to shared files
# setfacl: Access Control Lists for fine-grained permissions
```

### 4. Job Scheduler (LSF) Commands

```bash
# CHECK AVAILABLE QUEUES
bqueues
# Lists all job queues with limits and policies
# Shows max walltime, resource limits per queue

# DETAILED QUEUE INFORMATION
bqueues -l gpu
# Detailed info for specific queue
# Shows resource limits, user limits, policies

# CHECK YOUR ACCOUNT BALANCE
mybalance
# Shows project allocations you have access to
# Required for job submission (-P flag)

# SUBMIT INTERACTIVE GPU JOB
bsub -P acc_pareks02a -q gpu -n 8 -R h100nvl -gpu num=1 -W 12:00 -R span[hosts=1] -R rusage[mem=16000] -Is /bin/bash

# Let's break this down completely:
# bsub: LSF job submission command
# -P acc_pareks02a: Project allocation account (billing/access control)
# -q gpu: Submit to GPU queue (vs cpu queues)
# -n 8: Request 8 CPU cores
# -R h100nvl: Request H100 NVL GPU type specifically
# -gpu num=1: Request 1 GPU device
# -W 12:00: Walltime limit (12 hours in HH:MM format)
# -R span[hosts=1]: All resources on single physical node
# -R rusage[mem=16000]: 16GB RAM per CPU core (128GB total)
# -Is /bin/bash: Interactive session with bash shell

# WHY THESE SPECIFIC OPTIONS:
# span[hosts=1]: GPU memory not shared across nodes
# rusage[mem=16000]: Large models need substantial RAM
# h100nvl: Newest/fastest GPU type available
# Interactive: Needed for development/debugging

# CHECK JOB STATUS
bjobs
# Shows your running/pending jobs
# Status codes: RUN, PEND, EXIT, DONE

# DETAILED JOB INFORMATION
bjobs -l <jobid>
# Full job details including resource usage
# Pending reasons, execution host, etc.

# JOB HISTORY (completed jobs)
bhist -a | head -10
# Shows recently completed jobs
# Useful for checking past resource usage

# CANCEL JOB
bkill <jobid>
# Terminates running or pending job
# Frees up allocated resources

# MODIFY PENDING JOB
bmod -W 24:00 <jobid>
# Change walltime of pending job
# Only works before job starts running

# CHECK PENDING REASONS
bjobs -p
# Shows why jobs are pending
# Common: resource unavailability, queue limits

# MONITOR JOB OUTPUT (running jobs)
bpeek <jobid>
# Shows stdout/stderr of running job
# Useful for monitoring progress
```

### 5. GPU and Resource Monitoring

```bash
# GPU STATUS AND USAGE
nvidia-smi
# Shows all GPUs, their usage, running processes
# Memory usage, temperature, power consumption

# SPECIFIC GPU MONITORING
nvidia-smi -i 0
# Monitor only GPU 0
# Useful when you're assigned specific GPU

# CONTINUOUS MONITORING
watch -n 5 nvidia-smi
# Updates every 5 seconds
# Ctrl+C to exit
# Why: Track usage over time, spot memory leaks

# GPU PROCESS INFORMATION
nvidia-smi -q -d PIDS
# Detailed process information for GPU usage
# Shows which processes using which GPUs

# SYSTEM RESOURCE MONITORING
htop
# Interactive process viewer
# CPU usage, memory, process tree

# NETWORK CONNECTIVITY (GPU nodes)
curl -I https://huggingface.co
# Test internet connectivity
# GPU nodes require proxy configuration
```

### 6. Environment and Package Management

```bash
# CONDA ENVIRONMENT CREATION
conda create --prefix /sc/arion/work/$USER/.conda/envs/medgemma python=3.11 -y
# Why --prefix: Explicit path avoids home directory
# Why python=3.11: Optimal for current ML packages
# Why work directory: More space than home

# ACTIVATE ENVIRONMENT
conda activate /sc/arion/work/$USER/.conda/envs/medgemma
# Must use full path for custom location
# Changes prompt to show active environment

# CONDA INITIALIZATION
conda init
source ~/.bashrc
# One-time setup for conda commands
# Modifies shell configuration

# PACKAGE INSTALLATION
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
# Why specific index: Gets CUDA-compatible PyTorch
# Why cu121: Matches Minerva's CUDA version

# ENVIRONMENT VARIABLES (GPU nodes)
export http_proxy=http://172.28.7.1:3128
export https_proxy=http://172.28.7.1:3128
export all_proxy=http://172.28.7.1:3128
export no_proxy=localhost,*.hpc.mssm.edu,*.chimera.hpc.mssm.edu,172.28.0.0/16

# Why needed: GPU compute nodes don't have direct internet
# Proxy server: 172.28.7.1:3128 routes external requests
# no_proxy: Excludes internal Minerva traffic from proxy
```

### 7. File Transfer and Data Management

```bash
# SECURE COPY (from local to Minerva)
scp localfile.txt username@minerva.hpc.mssm.edu:/sc/arion/work/$USER/
# Copies files over SSH connection
# Preserves permissions and timestamps

# SECURE COPY (from Minerva to local)
scp username@minerva.hpc.mssm.edu:/sc/arion/work/$USER/results.zip ./
# Downloads files to current local directory

# RSYNC (more efficient for large files)
rsync -avz localdir/ username@minerva.hpc.mssm.edu:/sc/arion/work/$USER/
# -a: archive mode (preserves permissions)
# -v: verbose output
# -z: compression during transfer

# GLOBUS (for very large datasets)
# Web interface: https://app.globus.org/
# High-performance transfer for TB-scale data
# Resumes interrupted transfers
```

## System Architecture Deep Dive

### Login Nodes vs Compute Nodes

**LOGIN NODES (minerva12/13/14):**
```
Purpose: Entry point and job management
Resources: 
- Shared among ALL users simultaneously
- Limited CPU (don't run heavy computations)
- No GPU access
- Full internet connectivity
- NFS storage access

What you do here:
- File editing and management
- Job submission (bsub commands)
- Code development and testing (small scale)
- Data transfer and organization
- Environment setup

What you DON'T do here:
- Heavy computations (gets you banned)
- GPU work (no GPUs available)
- Long-running processes (use compute nodes)
```

**COMPUTE NODES (lg*, lc*, etc.):**
```
Purpose: Heavy computation and production work
Resources:
- Exclusive allocation through job scheduler
- High-performance CPUs
- GPUs (on GPU nodes)
- Limited internet (proxy required)
- GPFS high-performance storage

Access method: Only through LSF job submission
Node types:
- CPU nodes: lc* (large core count, no GPU)
- GPU nodes: lg* (GPUs + CPUs)
- Interactive nodes: Special subset for interactive work
- High memory nodes: Extra RAM for memory-intensive tasks
```

### Resource Allocation Flow

```
DETAILED RESOURCE ALLOCATION PROCESS
===================================

1. Job Submission (from login node):
   User runs: bsub -P project -q gpu -n 8 -gpu num=1 ...
   
2. LSF Scheduler Analysis:
   ├── Check user permissions for project account
   ├── Validate resource request against queue limits
   ├── Check available resources across cluster
   └── Queue job based on priority and availability
   
3. Resource Matching:
   ├── Find nodes with requested GPU type
   ├── Ensure sufficient CPU cores available
   ├── Check memory availability
   └── Verify no resource conflicts
   
4. Job Dispatch:
   ├── Allocate exclusive access to resources
   ├── Set up execution environment
   ├── Start user shell on compute node
   └── Begin resource monitoring
   
5. Execution Environment:
   ├── CUDA_VISIBLE_DEVICES set to allocated GPUs
   ├── CPU affinity set to allocated cores
   ├── Memory limits enforced
   └── Network isolation (proxy required)
```

### GPU Node Architecture Specifics

```bash
# When you get assigned lg02e03, here's what happens:

# 1. EXCLUSIVE GPU ACCESS
echo $CUDA_VISIBLE_DEVICES
# Shows: "0" (you get GPU 0 exclusively)

# 2. PHYSICAL RESOURCES
nvidia-smi
# Shows your GPU: NVIDIA H100 80GB HBM3
# 80GB GPU memory available
# Connected via NVLink for fast memory access

# 3. COMPUTE ENVIRONMENT
lscpu
# Shows: 8 CPU cores allocated exclusively
# These cores dedicated to your job only

# 4. MEMORY ALLOCATION
free -h
# Shows: 128GB RAM available (16GB per core × 8 cores)
# Exclusive allocation, not shared

# 5. STORAGE ACCESS
df -h
# Shows mounted filesystems:
# - /hpc/users/$USER (home directory)
# - /sc/arion/work/$USER (work directory)
# - /sc/arion/scratch/$USER (scratch space)
# - Any project directories you have access to
```

### Network Architecture

```
NETWORK FLOW DIAGRAM
===================

Internet ←→ Campus Network ←→ VPN Gateway ←→ Login Nodes ←→ Compute Nodes
    ↑              ↑              ↑            ↑            ↑
[External]    [On-campus]    [F5 Big-IP]   [minerva*]   [lg*, lc*]
[Websites]    [Direct]       [Off-campus]  [Full inet]  [Proxy only]

LOGIN NODES:
- Full internet access
- Can download directly from HuggingFace, PyPI, etc.
- No proxy configuration needed

COMPUTE NODES (including GPU nodes):
- Limited internet access for security
- Must use proxy: http://172.28.7.1:3128
- Internal Minerva communication direct
- This is why we needed proxy setup for model downloads
```

This comprehensive breakdown shows how every command we used fits into the overall HPC ecosystem, why specific approaches worked while others failed, and how the system architecture influences our deployment strategies.
