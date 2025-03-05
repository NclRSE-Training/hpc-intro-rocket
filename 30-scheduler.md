---
title: "Working with the scheduler"
teaching: 50
exercises: 30
---


::: questions
 - What is a scheduler and why are they used?
 - How do I launch a program to run on any one node in the cluster?
 - How do I capture the output of a program that is run on a node in the
   cluster?
:::

::: objectives
 - Run a simple Hello World style program on the cluster.
 - Submit a simple Hello World style script to the cluster.
 - Use the batch system command line tools to monitor the execution of your
   job.
 - Inspect the output and error files of your jobs.
:::

## Job Scheduler

An HPC system might have thousands of nodes and thousands of users. How do we
decide who gets what and when? How do we ensure that a task is run with the
resources it needs? This job is handled by a special piece of software called
the scheduler. On an HPC system, the scheduler manages which jobs run where and
when.

The following illustration compares these tasks of a job scheduler to a waiter
in a restaurant. If you can relate to an instance where you had to wait for a
while in a queue to get in to a popular restaurant, then you may now understand
why sometimes your job do not start instantly as in your laptop.

![The waiter scheduler](fig/restaurant_queue_manager.svg){caption="" alt="Compare a job scheduler to a waiter in a restaurant"}

The scheduler used in this lesson is Slurm. Although
Slurm is not used everywhere, running jobs is quite similar
regardless of what software is being used. The exact syntax might change, but
the concepts remain the same.

## Running a Batch Job

The most basic use of the scheduler is to run a command non-interactively. Any
command (or series of commands) that you want to run on the cluster is called a
*job*, and the process of using a scheduler to run the job is called *batch job
submission*.

In this case, the job we want to run is a shell script -- essentially a
text file containing a list of UNIX commands to be executed in a sequential
manner. Our shell script will have three parts:

* On the very first line, add `#!/bin/bash. The `#!`
  (pronounced "hash-bang" or "shebang") tells the computer what program is
  meant to process the contents of this file. In this case, we are telling it
  that the commands that follow are written for the command-line shell (what
  we've been doing everything in so far).
* Anywhere below the first line, we'll add an `echo` command with a friendly
  greeting. When run, the shell script will print whatever comes after `echo`
  in the terminal.
  * `echo -n` will print everything that follows, _without_ ending
    the line by printing the new-line character.
* On the last line, we'll invoke the `hostname` command, which will print the
  name of the machine the script is run on.

```bash
[userid@login01 ~]$  nano example-job.sh
[userid@login01 ~]$  chmod +x example-job.sh
[userid@login01 ~]$  cat example-job.sh
```

```output
#!/bin/bash

echo -n "This script is running on "
hostname
```

::: challenge
## Creating Our Test Job

Run the script. Does it execute on the cluster or just our login node?

```bash
[userid@login01 ~]$  ./example-job.sh
```

::: solution

```output
This script is running on login01
```
This job runs on the login node. **!! Remember !!** we don't run jobs on the login node unless they are very small test jobs like this one.
:::
:::

If you completed the previous challenge successfully, you probably realise that
there is a distinction between running the job through the scheduler and just
"running it". To submit this job to the scheduler, we use the
`sbatch` command.

```bash
[userid@login01 ~]$  sbatch --partition=short example-job.sh
```

```output
Submitted batch job 36855
```

Our work is done --- now the scheduler takes over and tries to run the job for us. 
While the job is waiting
to run, it goes into a list of jobs called the *queue*. To check on our job's
status, we check the queue using the command
`squeue -u userid`.

```bash
[userid@login01 userid]$  squeue -u userid
```

```output
JOBID PARTITION     NAME     USER   ST    TIME  NODES NODELIST(REASON)
36855 short         example- userid PD    0:00  1     (None)
```

We can see all the details of our job, most importantly that it is in the `R`
or `RUNNING` state. Sometimes our jobs might need to wait in a queue
(`PD` or `PENDING`) or become terminated, for example due to `OUT_OF_MEMORY` (`OOM`) error, `TIMEOUT` (`TO`) or some other `FAILED` (`F`) condition.


::: discussion
## Where's the Output?
On the login node, this script printed output to the terminal --- but
when we exit `watch`, there's nothing. Where'd it go?
HPC job output is typically redirected to a file in the directory you
launched it from. Use `ls` to find and `cat` to read the file.

On some HPC systems you may need to redirect the output explictly in your job submission script. You can achieve this by setting the options for error `--error=<error_filename>` and output `--output=<output_filename>` filenames. On Rocket this is handled by default with output and error files named according to the job submission id.
:::

## Customising a Job

The job we just ran used some of the scheduler's default options. In a
real-world scenario, that's probably not what we want. The default options
represent a reasonable minimum. Chances are, we will need more cores, more
memory, more time, among other special considerations. To get access to these
resources we must customize our job script.

Comments in UNIX shell scripts (denoted by `#`) are typically ignored, but
there are exceptions. For instance the special `#!` comment at the beginning of
scripts specifies what program should be used to run it (you'll typically see
`#!/bin/bash`). Schedulers like Slurm also
have a special comment used to denote special scheduler-specific options.
Though these comments differ from scheduler to scheduler,
Slurm's special comment is `#SBATCH`. Anything
following the `#SBATCH` comment is interpreted as an
instruction to the scheduler.

Let's illustrate this by example. By default, a job's name is the name of the
script, but the `--job-name` option can be used to change the
name of a job. Add an option to the script:

```bash
[userid@login01 userid]$  cat example-job.sh
```

```output
#!/bin/bash
#SBATCH--job-name new_name

echo -n "This script is running on "
hostname
echo "This script has finished successfully."
```

Submit the job and monitor its status:

```bash
[userid@login01 userid]$  sbatch --partition=short example-job.sh
[userid@login01 userid]$  squeue -u userid
```

```output
JOBID USER         ACCOUNT     NAME     ST REASON   START_TIME TIME TIME_LEFT NODES CPUS
38191 userid yourAccount new_name PD Priority N/A        0:00 1:00:00   1     1
```

Fantastic, we've successfully changed the name of our job!

### Resource Requests

But what about more important changes, such as the number of cores and memory
for our jobs? One thing that is absolutely critical when working on an HPC
system is specifying the resources required to run a job. This allows the
scheduler to find the right time and place to schedule our job. If you do not
specify requirements (such as the amount of time you need), you will likely be
stuck with your site's default resources, which is probably not what you want.

The following are several key resource requests:

`--account=<project>` your account is typically your project code, for example `training`. Rocket does not charge users but other HPCs use the --account option for charging to a project budget.

`--partition=<partition>` The partition specifies the set of nodes you want to run on. More information on available partitions is given in the [Rocket documentation](https://www.ncl.ac.uk/itservice/research/hpc).

Other common options that are used are:

`--time=<hh:mm:ss>` the maximum walltime for your job. e.g. For a 6.5 hour walltime, you would use `--time=06:30:00`.

`--job-name=<jobname>` set a name for the job to help identify it in Slurm command output.

In addition, parallel jobs will also need to specify how many nodes, parallel processes and threads they require.

`--exclusive` to ensure that you have exclusive access to a compute node

`--nodes=<number>` the number of nodes to use for the job.

`--tasks-per-node=<processes per node>` the number of parallel processes (e.g. MPI ranks) per node.

`--cpus-per-task=<threads per task>` the number of threads per parallel process (e.g. number of OpenMP threads per MPI task for hybrid MPI/OpenMP jobs). Note: you must also set the `OMP_NUM_THREADS` environment variable if using OpenMP in your job and usually add the `--cpu-bind=cores` option to `srun`



Note that just *requesting* these resources does not make your job run faster,
nor does it necessarily mean that you will consume all of these resources. It
only means that these are made available to you. Your job may end up using less
memory, or less time, or fewer tasks or nodes, than you have requested, and it
will still run.

It's best if your requests accurately reflect your job's requirements. We'll
talk more about how to make sure that you're using resources effectively in a
later episode of this lesson.

::: callout
## Command line options or job script options?
All of the options we specify can be supplied on the command line (as we
do here for `--partition=short`) or in the job script (as we have done
for the job name above). These are interchangeable. It is often more convenient
to put the options in the job script as it avoids lots of typing at the command
line.
:::

::: challenge
## Submitting Resource Requests
Modify our `hostname` script so that it runs for a minute, then submit a job
for it on the cluster. You should also move all the options we have been specifying
on the command line (e.g. `--partition`) into the script at this point.

::: solution

```bash
[userid@login01 userid]$  cat example-job.sh
```

```output
#!/bin/bash
#SBATCH--time 00:01:15
#SBATCH--partition=short
echo -n "This script is running on "
sleep 60 # time in seconds
hostname
echo "This script has finished successfully."
```

```bash
[userid@login01 ~]$  sbatch example-job.sh
```

Why are the Slurm runtime and `sleep` time not identical?
:::
:::


::: challenge
## Job environment variables

When Slurm runs a job, it sets a number of environment variables for the job. One of these will let us check what directory our job script was submitted from. The `SLURM_SUBMIT_DIR` variable is set to the directory from which our job was submitted.

Using the `SLURM_SUBMIT_DIR` variable, modify your job so that it prints out the location from which the job was submitted.

::: solution
```bash
[userid@login01 ~]$  nano example-job.sh
[userid@login01 ~]$  cat example-job.sh
```

```output
#!/bin/bash
#SBATCH --partition=short
#SBATCH --time=00:01 # timeout in HH:MM

echo -n "This script is running on "
hostname

echo "This job was launched in the following directory:"
echo ${SLURM_SUBMIT_DIR}
```
:::
:::

Resource requests are typically binding. If you exceed them, your job will be
killed. Let's use walltime as an example. We will request 30 seconds of
walltime, and attempt to run a job for two minutes.

```bash
[userid@login01 userid]$  cat example-job.sh
```

```output
#!/bin/bash
#SBATCH--job-name long_job
#SBATCH--time 00:00:30
#SBATCH--partition=short

echo "This script is running on ... "
sleep 120 # time in seconds
hostname
echo "This script has finished successfully."
```

Submit the job and wait for it to finish. Once it is has finished, check the
log file.

```bash
[userid@login01 userid]$  sbatch example-job.sh
[userid@login01 userid]$  squeue -u userid
```

```bash
[userid@login01 userid]$  cat slurm-17866475.out
```
```output
This script is running on ...
slurmstepd: error: *** JOB 17866475 ON sb013 CANCELLED AT 2025-02-19T07:00:57 DUE TO TIME LIMIT ***
```

Our job was killed for exceeding the amount of resources it requested. Although
this appears harsh, this is actually a feature. Strict adherence to resource
requests allows the scheduler to find the best possible place for your jobs.
Even more importantly, it ensures that another user cannot use more resources
than they've been given. If another user messes up and accidentally attempts to
use all of the cores or memory on a node, Slurm will either
restrain their job to the requested resources or kill the job outright. Other
jobs on the node will be unaffected. This means that one user cannot mess up
the experience of others, the only jobs affected by a mistake in scheduling
will be their own.

::: callout
## But how much does it cost?
Rocket does not currently charge you to run jobs but other HPCs do charge.
Although your job will be killed if it exceeds the selected runtime,
a job that completes within the time limit is only charged for the
time it actually used. However, you should always try and specify a
wallclock limit that is close to (but greater than!) the expected
runtime as this will enable your job to be scheduled more
quickly.
If you say your job will run for an hour, the scheduler has
to wait until a full hour becomes free on the machine. If it only ever
runs for 5 minutes, you could have set a limit of 10 minutes and it
might have been run earlier in the gaps between other users' jobs.
:::

## Cancelling a Job

Sometimes we'll make a mistake and need to cancel a job. This can be done with
the `scancel` command. Let's submit a job and then cancel it using
its job number (remember to change the walltime so that it runs long enough for
you to cancel it before it is killed!).

```bash
[userid@login01 userid]$  sbatch example-job.sh
[userid@login01 userid]$  squeue -u userid
```

```output
Submitted batch job 38759

JOBID PARTITION     NAME     USER      ST    TIME  NODES NODELIST(REASON)
38759  short        long_job   ncb176  R     1:13      1 sb013
```

Now cancel the job with its job number (printed in your terminal). Absence of any
job info indicates that the job has been successfully cancelled.

```bash
[userid@login01 userid]$  scancel 38759
# It might take a minute for the job to disappear from the queue...
[userid@login01 userid]$  squeue -u userid
```

```output
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
```

::: challenge
## Cancelling multiple jobs
We can also cancel all of our jobs at once using the `-u` option. This will delete all jobs for a
specific user (in this case us). Note that you can only delete your own jobs.
Try submitting multiple jobs and then cancelling them all with `scancel -u userid`.
:::

## Other Types of Jobs

Up to this point, we've focused on running jobs in batch mode.
Slurm also provides the ability to start an interactive session.

There are very frequently tasks that need to be done interactively. Creating an
entire job script might be overkill, but the amount of resources required is
too much for a login node to handle. A good example of this might be building a
genome index for alignment with a tool like
[HISAT2](https://ccb.jhu.edu/software/hisat2/index.shtml). Fortunately, we can
run these types of tasks as a one-off with `srun`.

`srun` runs a single command in the queue system and then exits.
Let's demonstrate this by running the
`hostname` command with `srun`. (We can cancel an `srun` job with `Ctrl-c`.)

```bash
[userid@login01 userid]$  srun --partition=short --time=00:01:00 hostname
```
```output
srun: job 17866477 queued and waiting for resources
srun: job 17866477 has been allocated resources
sb013.cluster
```

`srun` accepts all of the same options as `sbatch`. However, instead of specifying these in a
script, these options are specified on the command-line when starting a job.

Typically, the resulting shell environment will be the same as that for
`sbatch`.

### Interactive jobs

Sometimes, you will need a lot of resource for interactive use. Perhaps it's our first time running
an analysis or we are attempting to debug something that went wrong with a previous job.
Fortunately, SLURM makes it easy to start an interactive job with `srun`:

```bash
[userid@login01 userid]$  srun --partition=short --pty /bin/bash
```

You should be presented with a bash prompt. Note that the prompt may change
to reflect your new location, in this case the compute node we are logged on.
You can also verify this with `hostname`.

When you are done with the interactive job, type `exit` to quit your session.


::: keypoints
 - "The scheduler handles how compute resources are shared between users."
 - "Everything you do should be run through the scheduler."
 - "A job is just a shell script."
 - "If in doubt, request more resources than you will need."
:::
