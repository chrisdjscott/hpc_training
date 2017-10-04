---
layout: post
title: HPC3 Slurm job scheduler
---


SLURM is a software used on the NeSI supercomputers for scheduling and managing job submission requests. A job is a script that tells the scheduler how much resources you will require to perform a given computational work. SLURM will then try to accommodate your request while maximising overall user experience. 

SLURM was an acronym for Simple Linux Utility for Resource Management

Additional information can be found at: https://support.nesi.org.nz/hc/en-gb/articles/115000194910-Submitting-Slurm-Jobs-on-Pan

Adapted from Jordi Blasco's [Introduction to SLURM documentation](https://wiki.auckland.ac.nz/download/attachments/63145549/introduction-slurm.pdf?api=v2)


## Features of SLURM

- Full control over CPU and memory usage
- Job array support
- Integration with MPI
- Supports interactive sessions
- Debugger friendly
- Environment privacy
- Job profiling

## Resource management

### Nodes and Job States

Nodes

- state (up/down/idle/allocated/mix/drained)

Jobs

- queued/pending and running
- suspended/preempted
- cancelled/completed/failed

## Queues

A queuing system assigns priority to jobs based on:

- Project type (decided upon at time of project application)
  - merit
  - institution
  - proposal development
  - postgraduate

- Wall time (specified at job runtime)
  - high (under 6 hours)
  - medium (over 6 hours but less than 24 hours)
  - low (longer than 24 hours)

Other considerations made by the queue are the number of jobs previously run by the user, and how long the job as spent in the queue

## Running a job

### SLURM Commands


- *sbatch* - submits a script job
- *scancel* - cancels a running or pending job
- *srun* - runs a command across nodes
- sbcast Transfer file to a compute nodes allocated job
- interactive - opens an interactive job session
- sattach - connect stdin/out/err for an existing job or job step
- squeue - displays the job queue

### Commonly used SLURM variables

- $SLURM_JOBID (job id)
- $SLURM_JOB_NODELIST (nodes allocated for job)
- $SLURM_NNODES (number of nodes)
- $SLURM_SUBMIT_DIR (directory job was submitted from)
- $SLURM_ARRAY_JOB_ID (job id for the array)
- $SLURM_ARRAY_TASK_ID (job array index value)

### Standard job script directives

```
#!/bin/bash
#SBATCH -J JobName
#SBATCH -A nesi99999		# Project Account
#SBATCH --time=08:00:00		# Walltime
#SBATCH --mem-per-cpu=4096	# memory/cpu (in MB)
#SBATCH --ntasks=2		# 2 tasks
#SBATCH --cpus-per-task=4	# number of cores per task
#SBATCH --nodes=1		# number of nodes
#SBATCH --exclusive             # node should not be shared with other jobs
```



### Examples of submitting jobs

Let's submit a first job via SLURM. We will do it using the `sbatch` command to interact with SLURM. Remember to replace [Project Account] with the actual project ID that you want to use.

```
sbatch -A [Project Account] -t 10 --wrap "echo hello world"

```

The options `-t` stands for *time* and sets a limit on the total run time of the job allocation. Note that each partition on which the jobs are run has its own time limit. If the set time limit exceeds the limit for the partition, the job will become "PENDING" (for more information on job statuses, see below).
`--wrap` option means that the following string (in "") will be turned by SLURM in a simple shell script.


### Monitoring your work on the cluster

We can monitor our job submissions using `squeue` command (which comes with many different options):

```
squeue -u your_username
```

And the output will look more or less like this:

```
   JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
61568899 merit_sho     wrap  apaw363 PD       0:00      1 (Resources)
61568901 merit_sho     wrap  apaw363 PD       0:00      1 (Resources)
61568970 merit_sho     wrap  apaw363 PD       0:00      1 (Resources)
```

Another useful SLURM command is `sacct` which retrieves information about submitted jobs with regards to the accounts. For example:

```
sacct -j 61568970
```

Will show us something like:

```
       JobID    JobName  Partition    Account  AllocCPUS      State ExitCode
------------ ---------- ---------- ---------- ---------- ---------- --------
61568970           wrap merit_sho+  nesi00357          1  COMPLETED      0:0
61568970.ba+      batch             nesi00357          1  COMPLETED      0:0
```

### Job outputs



### SLURM scripts

#### run_simple.sl

A script to run the R script simple.R as an example of submitting a R script using slurm

Output will be captured in the slurm output file

```
sbatch -A project_code run_simple.sl
```

#### run_print-args.sl

A script to demonstrate passing in commandline arguments to an Rscript as part submitting a slurm job

Output will be captured in the slurm output file

```
sbatch -A project_code run_print-args.sl first second third
```



#### run_array-analysis

A script to demonstrate using a slurm array job to run an analysis in parallel on different input datasets

Output will be captured in the slurm output file


```
sbatch -A project_code run_array-analysis.sl
```


```
sbatch --array=1 -A [project_code] run_array-analysis.sl something.txt
```

```
sbatch --array=1-2 -A [project_code] run_array-analysis.sl
```

```
sacct -j [jobid]
```


### Cancelling jobs

```
scancel [jobid]
```

```

```