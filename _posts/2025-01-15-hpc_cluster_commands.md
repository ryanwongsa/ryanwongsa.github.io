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
# file: slurm_checker.py
import subprocess

scontrol_output = subprocess.check_output(["scontrol", "-o", "show", "node"])


selection_names = [
    "NodeName",
    # "CPUAlloc",
    # "CPUTot",
    "Gres",
    # "FreeMem",
    "State",
    "CfgTRES",
    "AllocTRES",
]
node_data = []
for line in scontrol_output.decode().splitlines():
    fields = line.split()
    node_info = {}
    for field in fields:
        split_field = field.split("=")
        if len(split_field) > 1 and split_field[0] in selection_names:
            if (
                len(split_field) == 2
                and "CfgTRES" not in split_field[0]
                and "AllocTRES" not in split_field[0]
            ):
                node_info[split_field[0]] = split_field[1]
            else:
                if "CfgTRES" in split_field[0]:
                    node_info["TotalCPUs"] = split_field[2].split(",")[0]
                    node_info["TotalMem"] = split_field[3].split(",")[0]
                    node_info["TotalGPUs"] = split_field[5]
                elif "AllocTRES" in split_field[0] and len(split_field) > 2:
                    node_info["UsedCPUs"] = split_field[2].split(",")[0]
                    node_info["UsedMem"] = split_field[3].split(",")[0]
                    if len(split_field) >= 5:
                        node_info["UsedGPUs"] = split_field[4]
    if "UsedCPUs" not in node_info:
        node_info["UsedCPUs"] = 0
    if "UsedMem" not in node_info:
        node_info["UsedMem"] = 0
    if "UsedGPUs" not in node_info:
        node_info["UsedGPUs"] = 0

    node_info = {
        "NodeName": node_info["NodeName"],
        "State": node_info["State"],
        "Gres": node_info["Gres"],
        "UsedCPUs": node_info["UsedCPUs"],
        "TotalCPUs": node_info["TotalCPUs"],
        "UsedMem": node_info["UsedMem"],
        "TotalMem": node_info["TotalMem"],
        "UsedGPUs": node_info["UsedGPUs"],
        "TotalGPUs": node_info["TotalGPUs"],
    }

    node_data.append(node_info)

column_widths = {
    header: max(max(len(str(node[header])) for node in node_data), len(header))
    for header in node_data[0]
}

header_format = (
    "| {:<"
    + "} | {:<".join(str(column_widths[header]) for header in node_data[0])
    + "} |"
)
print(header_format.format(*node_data[0].keys()))
print("-" * (sum(column_widths.values()) + len(column_widths) * 3 + 1))

row_format = (
    "| {:<"
    + "} | {:<".join(str(column_widths[header]) for header in node_data[0])
    + "} |"
)
for node in node_data:
    print(row_format.format(*node.values()))
```

### Interactive job
```bash
srun --mem=20G --nodes 1 --ntasks-per-node=1  --time=04:00:00 --partition=<p_name> --pty apptainer exec docker://<DOCKER_DIR> bash -i
```

