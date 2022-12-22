---
description: Job scheduler based on Slurm (NSCC based on PBS-pro)
---

# Scheduler

* Job: commands to run on the cluster
* Batch job submission: the process of using a scheduler to run the job&#x20;

### Run a batch job

Normally jobs are run on login node in this way:

```bash
$ nano test.sh
$ chmod 744 test.sh
$ ./test.sh
```

If want to run on compute nodes, we queue up the job with scheduler:

```bash
# Submit a job to the scheduler and dispatched to a computer node
$ sbatch test.sh

# Check the queue
$ squeue -u <username>

# Also, for interactive jobs (echo hello-world on 2 cpu cores)
$ srun -n 2 echo "Hello world."

# Cancel a job by job id
$ scancel <jobid>

# Cancel my all jobs
$ scancel -u <username>
```

```
Output of squeue be like:
JOBID USER         ACCOUNT     NAME           ST REASON START_TIME         T...
36856 yourUsername yourAccount example-job.sh R  None   2017-07-01T16:47:02 ...
```

ST (status): R (RUNNING), PENDING, (E) ERROR.

Output is redirected to a file in the directory you launched the job from.

### Customize a job

Anything following the `#SBATCH` comment is interpreted as an instruction to the scheduler.&#x20;

```bash
#!/usr/bin/env bash
#SBATCH -J hello-world # change job name
#SBATCH -t 00:00:30 # timeout in DD:HH:MM
echo -n "Test"
hostname 
```

* `--ntasks=<ntasks>` or `-n <ntasks>`: How many CPU cores does your job need, in total?
* `--time <days-hours:minutes:seconds>` or `-t <days-hours:minutes:seconds>`: How much real-world time (walltime) will your job take to run? The `<days>` part can be omitted.
* `--mem=<megabytes>`: How much memory on a node does your job need in megabytes? You can also specify gigabytes using by adding a little “g” afterwards (example: `--mem=5g`)
* `--nodes=<nnodes>` or `-N <nnodes>`: How many separate machines does your job need to run on? Note that if you set `ntasks` to a number greater than what one machine can offer, Slurm will set this value automatically.

**!IMPORTANTLY**

* R_equesting_ these resources only means that these are made available to you. Your job may end up using less memory, or less time, or fewer nodes than you have requested, and it will still run.
* Our job was killed for exceeding the amount of resources it requested.&#x20;

