---
description: Slurm
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
# Submit a job to the scheduler
$ sbatch test.sh
```

This creates a _job_ which will run the _script_ when _dispatched_ to a compute node which the queuing system has identified as being available to perform the work.

```bash
# Check the queue
$ squeue -u xli072
```

```
JOBID USER         ACCOUNT     NAME           ST REASON START_TIME         T...
36856 yourUsername yourAccount example-job.sh R  None   2017-07-01T16:47:02 ...
```

ST (status): R (RUNNING), PENDING, (E) ERROR.

Output is redirected to a file in the directory you launched the job from.

### Custome a job



\
