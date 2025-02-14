---
title: "Using resources effectively"
teaching: 10
exercises: 30
---


::: questions
 - How do we monitor our jobs?
 - How can I get my jobs scheduled more easily?
:::

::: objectives
 - Understand how to look up job statistics and profile code.
 - Understand job size implications.
:::

We've touched on all the skills you need to interact with an HPC cluster:
logging in over SSH, loading software modules, submitting parallel jobs, and
finding the output. Let's learn about estimating resource usage and why it
might matter. To do this we need to understand the basics of *benchmarking*.
Benchmarking is essentially performing simple experiments to help understand
how the performance of our work varies as we change the properties of the
jobs on the cluster - including input parameters, job options and resources used.

::: discussion
## Our example
In the rest of this episode, we will use an example parallel application that calculates 
an estimate of the value of Pi. Although this is a toy problem, it exhibits all the properties of a full
parallel application that we are interested in for this course.

The main resource we will consider here is the use of compute core time as this is the
resource you are usually charged for on HPC resources. However, other resources - such
as memory use - may also have a bearing on how you choose resources and constrain your
choice.

For those that have come across HPC benchmarking before, you may be aware that people
often make a distinction between *strong scaling* and *weak scaling*:

- Strong scaling is where the problem size (i.e. the *application*) stays the same 
  size and we try to use more cores to solve the problem faster.
- Weak scaling is where the problem size increases at the same rate as we increase
  the core count so we are using more cores to solve a larger problem.

Both of these approaches are equally valid uses of HPC. This example looks at strong scaling. 

:::

Before we work on benchmarking, it is useful to define some terms for the example we will
be using

  - **Program** The computer program we are executing (`pi-mpi.py` in the examples below)
  - **Application** The combination of computer program with particular input parameters

## Accessing the software and input

::: prereq

## Required Files

The program used in this example can be retrieved using wget or a browser and copied to the remote.

**Using wget**: 
```bash
[username3@login02 ]$ wget http://training.researchcomputing.ncl.ac.uk/training-materials/pi-mpi.py
```

**Using a web browser**:

[http://training.researchcomputing.ncl.ac.uk/training-materials/pi-mpi.py](http://training.researchcomputing.ncl.ac.uk/training-materials/pi-mpi.py)

:::

## Baseline: running in serial

Before starting to benchmark an application to understand what resources are best to use,
you need a *baseline* performance result. In more formal benchmarking, your baseline
is usually the minimum number of cores or nodes you can run on. However, for understanding
how best to use resources, as we are doing here, your baseline could be the performance on
any number of cores or nodes that you can measure the change in performance from.

Our `pi-mpi.py` application is small enough that we can run a serial (i.e. using a single core)
job for our baseline performance so that is where we will start

::: challenge
## Run a single core job
Write a job submission script that runs the `pi-mpi.py` application on a single core. You
will need to take an initial guess as to the walltime to request to give the job time 
to complete. Submit the job and check the contents of the STDOUT file to see if the 
application worked or not.

::: solution

Creating a file called `submit-pi-mpi.slurm`:

```bash
#!/bin/bash
#SBATCH --partition=short

#SBATCH --job-name=pi-mpi
#SBATCH --nodes=1
#SBATCH --tasks-per-node=1
#SBATCH --time=00:15:00
srun python pi-mpi.py 10000000
```

Run application using a single process (i.e. in serial) with a blocking `srun` command:
```bash
module load cray-python
[userid@login01 userid]$  srun --partition=short python pi-mpi.py 10000000
```

Submit with to the batch queue with:

```bash
[userid@login01 userid]$  srun submit-pi-mpi.slurm
```

Output in the job log should look something like:

```output
Generating 10000000 samples.
Rank 0 generating 10000000 samples on host nid001246.
Numpy Pi:  3.141592653589793
My Estimate of Pi:  3.1416708
1 core(s), 10000000 samples, 228.881836 MiB memory, 0.423903 seconds, -0.002487% error
```
:::
:::

Once your job has run, you should look in the output to identify the performance. Most 
HPC programs should print out timing or performance information (usually somewhere near
the bottom of the summary output) and `pi-mpi.py` is no exception. You should see two 
lines in the output that look something like:

```bash
256 core(s), 100000000 samples, 2288.818359 MiB memory, 0.135041 seconds, -0.004774% error
Total run time=0.18654435999997077s
```

You can also get an estimate of the overall run time from the final job statistics. If
we look at how long the finished job ran for, this will provide a quick way to see
roughly what the runtime was. This can be useful if you want to know quickly if a 
job was faster or not than a previous job (as you do not have to find the output file
to look up the performance) but the number is not as accurate as the performance recorded
by the application itself and also includes static overheads from running the job
(such as loading modules and startup time) that can skew the timings. To do this on
use `sacct -l -j 12345` with the job ID, e.g.:

```bash
[userid@login01 userid]$  sacct -l -j 12345
```
```output
JOBID USER         ACCOUNT     NAME           ST REASON START_TIME         T...
36856 yourUsername yourAccount example-job.sh R  None   2017-07-01T16:47:02 ...
```

## Running in parallel and benchmarking performance

We have now managed to run the `pi-mpi.py` application using a single core and have a baseline
performance we can use to judge how well we are using resources on the system.

Note that we also now have a good estimate of how long the application takes to run so we can
provide a better setting for the walltime for future jobs we submit. Lets now look at how
the runtime varies with core count.

::: challenge
## Benchmarking the parallel performance
Modify your job script to run on multiple cores and evaluate the performance of `pi-mpi.py`
on a variety of different core counts and use multiple runs to complete a table like the one
below.
If you examine the log file you will see that it contains two timings: the total time taken by the
entire program and the time taken solely by the calculation. The calculation of Pi from the Monte-Carlo counts
is not parallelised so this is a serial overhead, performed by a single processor.
The calculation part is, in theory, perfectly parallel (each processor operates on independent sets of unique random numbers
) so this should get faster on more cores. The Calculation core seconds is the
*calculation time* multiplied by the number of cores.

| Cores      | Overall run time (s) | Calculation time (s) | Calculation core seconds |
|------------|----------------------|----------------------|--------------------------|
| 1 (serial) |                      |                      |                          |
| 2          |                      |                      |                          |
| 4          |                      |                      |                          |
| 8          |                      |                      |                          |
| 16         |                      |                      |                          |
| 32         |                      |                      |                          |
| 64         |                      |                      |                          |
| 128        |                      |                      |                          |
| 256        |                      |                      |                          |

Look at your results – do they make sense? Given the structure of the code, you would
expect the performance of the calculation to increase
linearly with the number of cores: this would give a roughly constant figure for the Calculation core
seconds. Is this what you observe?

::: solution

The table below shows example timings for runs on ARCHER2 (HPC at Edinburgh)

| Cores      | Overall run time (s) | Calculation time (s) |       Calculation core seconds |
|-----------:|---------------------:|---------------------:|-------------------------------:|
|          1 |                3.931 |                3.854 |                          3.854 |
|          2 |                2.002 |                1.930 |                          3.859 |
|          4 |                1.048 |                0.972 |                          3.888 |
|          8 |                0.572 |                0.495 |                          3.958 |
|         16 |                0.613 |                0.536 |                          8.574 |
|         32 |                0.360 |                0.278 |                          8.880 |
|         64 |                0.249 |                0.163 |                         10.400 |
|        128 |                0.170 |                0.083 |                         10.624 |
|        256 |                0.187 |                0.135 |                         34.560 |

:::
:::

## Understanding the performance

Now we have some data showing the performance of our application we need to try and draw some
useful conclusions as to what the most efficient set of resources are to use for our jobs. To
do this we introduce two metrics:

  - **Actual speedup** The ratio of the baseline runtime (or runtime on the lowest core count)
    to the runtime at the specified core count. i.e. baseline runtime divided by runtime
    at the specified core count.
  - **Ideal speedup** The expected speedup if the application showed perfect scaling. i.e. if
    you double the number of cores, the application should run twice as fast.
  - **Parallel efficiency** The fraction of *ideal speedup* actually obtained for a given
    core count. This gives an indication of how well you are exploiting the additional resources
    you are using.

We will now use our performance results to compute these two metrics for the sharpen application
and use the metrics to evaluate the performance and make some decisions about the most 
effective use of resources.

::: challenge
## Computing the speedup and parallel efficiency
Use your *Overall run times* from above to fill in a table like the one below.

| Cores      | Overall run time (s) | Actual speedup  | Ideal speedup | Parallel efficiency |
|------------|----------------------|-----------------|----------------|---------------------|
| 1 (serial) |        $t_{c1}$      |       -         |       1        |          1         |
| 2          |        $t_{c2}$      | $s_2 = t_{c1}/t_{c2}$ | $i_2 = 2$ |  $s_2 / i_2$     |
| 4          |        $t_{c4}$      | $s_4 = t_{c1}/t_{c4}$ | $i_4 = 4$ |  $s_4 / i_4$     |                   
| 8          |                      |                 |                |                     | 
| 16         |                      |                 |                |                     |
| 32         |                      |                 |                |                     |
| 64         |                      |                 |                |                     |             
| 128        |                      |                 |                |                     |
| 256        |                      |                 |                |                     |

Given your results, try to answer the following questions:

1. What is the core count where you get the **most** efficient use of resources, irrespective
  of run time?
2. What is the core count where you get the fastest solution, irrespective of efficiency?
3. What do you think a good core count choice would be for this application that balances
   time to solution and efficiency? Why did you choose this option?

::: solution

The table below gives example results for ARCHER2 (HPC at Edinburgh) based on the example 
runtimes given in the solution above.

| Cores      | Overall run time (s) | Actual speedup | Ideal speedup | Parallel efficiency |
|-----------:|---------------------:|---------------:|--------------:|--------------------:|
|          1 |                3.931 |          1.000 |         1.000 |               1.000 |
|          2 |                2.002 |          1.963 |         2.000 |               0.982 |
|          4 |                1.048 |          3.751 |         4.000 |               0.938 |
|          8 |                0.572 |          6.872 |         8.000 |               0.859 |
|         16 |                0.613 |          6.408 |        16.000 |               0.401 |
|         32 |                0.360 |         10.928 |        32.000 |               0.342 |
|         64 |                0.249 |         15.767 |        64.000 |               0.246 |
|        128 |                0.170 |         23.122 |       128.000 |               0.181 |
|        256 |                0.187 |         21.077 |       256.000 |               0.082 |

### What is the core count where you get the **most** efficient use of resources?
Just using a single core is the cheapest (and always will be unless your speedup is better
than perfect – “super-linear” speedup). However, it may not be possible to run on small
numbers of cores depending on how much memory you need or other technical constraints.
**Note:** on most high-end systems, nodes are not shared between users. This means you are
charged for all the CPU-cores on a node regardless of whether you actually use them. Typically
we would be running on many hundreds of CPU-cores not a few tens, so the real question in
practice is: what is the optimal number of nodes to use?
### What is the core count where you get the fastest solution, irrespective of efficiency?
256 cores gives the fastest time to solution.
The fastest time to solution does not often make the most efficient use of resources so 
to use this option, you may end up wasting your resources. Sometimes, when there is 
time pressure to run the calculations, this may be a valid approach to running 
applications.
### What do you think a good core count choice would be for this application to use?

8 cores is probably a good number of cores to use with a parallel efficiency of 86%.
Usually, the best choice is one that delivers good parallel efficiency with an acceptable
time to solution. Note that *acceptable time to solution* differs depending on circumstances
so this is something that the individual researcher will have to assess. Good parallel
efficiency is often considered to be 70% or greater though many researchers will be happy
to run in a regime with parallel efficiency greater than 60%. As noted above, running with
worse parallel efficiency may also be useful if the time to solution is an overriding factor.

:::
:::

## Tips

Here are a few tips to help you use resources effectively and efficiently on HPC systems:

 - Know what your priority is: do you want the results as fast as possible or are you happy
  to wait longer but get more research for the resources you have been allocated?
 - Use your real research application to benchmark but try to shorten the run so you can turn
  around your benchmarking runs in a short timescale. Ideally, it should run for 10-30 minutes;
  short enough to run quickly but long enough so the performance is not dominated by static startup
  overheads (though this is application dependent). Ways to do this potentially include, for example:
  using a smaller number of time steps, restricting the number of SCF cycles, restricting the
  number of optimisation steps.
 - Use basic benchmarking to help define the best resource use for your application. One 
  useful strategy: take the core count you are using as the baseline, halve the number of
  cores/nodes and rerun and then double the number of cores/nodes from your baseline and
  rerun. Use the three data points to assess your efficiency and the impact of different
  core/node counts.

::: keypoints
 - "The smaller your job, the faster it will schedule."
:::
