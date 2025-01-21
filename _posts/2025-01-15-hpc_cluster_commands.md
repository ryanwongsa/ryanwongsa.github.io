---

title: "Useful Commands for HPC Cluster (HTCondor and Slurm)"
subtitle: "General commands for HTCondor and Slurm"
summary: ""
authors: [admin]
tags: []
categories: []
permalink: /post/hpc-commands/
date: 2025-01-15T07:45:08+01:00
lastmod: 2025-01-15T07:45:08+01:00
featured: false
draft: false

projects: []
---

# HPC Cluster

These are cluster commands that I found useful when running jobs on the HPC cluster at University of Surrey. It is only a list of 'custom' commands and does not include a list of the common general commands.

Commands for HTCondor and Slurm.

## HTCondor Commands

### Check the availability of GPUs, CPUs and Memory for each machine
```
condor_status -compact -af Machine Gpus Cpus Memory CUDADeviceName | awk 'BEGIN{OFS=FS=" "} {gsub(/ /, "_", $5); $4=$4/1000; if ($2 > 0) print}' | column -t
```

### Good overview of just CPU related information
```
condor_status --compact
```

### Submit file template

Example of how to submit from git repo.
```
condor_submit submit.sh
```

**Launch file (launch.sh)**

```bash
#!/bin/bash
git clone $1 Repository && cd Repository && git checkout $2
export PYTHONPATH=.
exec $3 ${@:4}
```

**Submit file (submit.sh)**
```bash
# ----------------------------------------------------------------------------------------------------------------------
executable = /bin/bash
# ----------------------------------------------------------------------------------------------------------------------
universe = docker
docker_image = <DOCKER_PATH>
should_transfer_files = YES
# ----------------------------------------------------------------------------------------------------------------------
log    = <LOG_DIR>/$(cluster).$(process).log
output = <LOG_DIR>/$(cluster).$(process).out
error  =  <LOG_DIR>/$(cluster).$(process).error
# ----------------------------------------------------------------------------------------------------------------------
+CanCheckpoint = True
+JobRunTime = 72
# ----------------------------------------------------------------------------------------------------------------------
on_exit_hold = ExitCode != 0
# ----------------------------------------------------------------------------------------------------------------------
request_GPUs     = 1
+GPUMem          = 15000
request_CPUs     = 1
request_memory   = 64G

+CanCheckpoint = True
+JobRunTime = 72
# ----------------------------------------------------------------------------------------------------------------------

location = <CODE_DIR>
launcher = $(location)/launch.sh
repository = <REPO_PATH>
arguments = $(launcher) $(repository) $(branch) python $(script) $(args)
script = main.py
args = --config=$(experiment)
branch = <COMMIT_HASH>
experiment=<EXP_NAME>
queue
```

## Slurm Commands

### View everyones jobs in the last few days
```bash
sacct --starttime=now-3days --format=JobID,user,Node,ReqTRES%50,Partition,JobName,Start,End,State,Timelimit,Priority,Submit --allocations --allusers
```

### Check priority of jobs
```bash
sprio -l
```


### Check what resources are available
NOTE: `slurm_gpustat` does not work correctly on UoS slurm cluster. Therefore I have created my own script:

```bash
python slurm_checker.py
```

```python
<TODO ADD THE SCRIPT HERE AROUND APRIL 2025>
```

### Interactive job
```bash
srun --mem=20G --nodes 1 --ntasks-per-node=1  --time=04:00:00 --partition=<p_name> --pty apptainer exec docker://<DOCKER_DIR> bash -i
```

