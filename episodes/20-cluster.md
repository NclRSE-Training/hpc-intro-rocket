---
title: "Working on an HPC system"
teaching: 25
exercises: 10
---


::: questions
 - How do I log on to an HPC system?
:::

::: objectives
 - Connect to an HPC system.
 - Understand the general HPC system architecture.
:::

## Logging In

The first step in using a cluster is to establish a connection from our laptop
to the cluster. When we are sitting at a computer (or standing, or holding it
in our hands or on our wrists), we have come to expect a visual display with
icons, widgets, and perhaps some windows or applications: a graphical user
interface, or GUI. Since computer clusters are remote resources that we connect
to over often slow or laggy interfaces (WiFi and VPNs especially), it is more
practical to use a command-line interface, or CLI, in which commands and
results are transmitted via text, only. Anything other than text (images, for
example) must be written to disk and opened with a separate program.

If you have ever opened the Windows Command Prompt or macOS Terminal, you have
seen a CLI. If you have already taken The Carpentries' courses on the UNIX
Shell or Version Control, you have used the CLI on your local machine somewhat
extensively. The only leap to be made here is to open a CLI on a *remote*
machine, while taking some precautions so that other folks on the network can't
see (or change) the commands you're running or the results the remote machine
sends back. We will use the Secure SHell protocol (or SSH) to open an encrypted
network connection between two machines, allowing you to send & receive text
and data without having to worry about prying eyes.

![Connect to cluster](fig/connect-to-remote.svg){alt="Connect to cluster"}

Make sure you have a SSH client installed on your laptop. Refer to the
[setup](../index.md) section for more details. SSH clients are
usually command-line tools, where you provide the remote machine address as the
only required argument. If your username on the remote system differs from what
you use locally, you must provide that as well. If your SSH client has a
graphical front-end, such as PuTTY or MobaXterm, you will set these arguments
before clicking "connect." From the terminal, you'll write something like `ssh
userName@hostname`, where the "@" symbol is used to separate the two parts of a
single argument.

Go ahead and open your terminal or graphical SSH client, then log in to the
cluster using your username.

```bash
[user@laptop ~]$ ssh userid@rocket.hpc.ncl.ac.uk
```

Remember to replace **userid** with your username or the one
supplied by the instructors. You may be asked for your password. Watch out: the
characters you type after the password prompt are not displayed on the screen.
Normal output will resume once you press `Enter`.

## Where Are We?

Very often, many users are tempted to think of a high-performance computing
installation as one giant, magical machine. Sometimes, people will assume that
the computer they've logged onto is the entire computing cluster. So what's
really happening? What computer have we logged on to? The name of the current
computer we are logged onto can be checked with the `hostname` command. (You
may also notice that the current hostname is also part of our prompt!)

```bash
[userid@login01 ~]$ hostname
```

```bash
login01.cluster
```

::: challenge

## What's in Your Home Directory?

The system administrators may have configured your home directory with some
helpful files, folders, and links (shortcuts) to space reserved for you on
other filesystems. Take a look around and see what you can find.
*Hint:* The shell commands `pwd` and `ls` may come in handy.
Home directory contents vary from user to user. Please discuss any
differences you spot with your neighbors.

::: solution

## It's a Beautiful Day in the Neighborhood

The deepest layer should differ: **userid** is uniquely yours.
Are there differences in the path at higher levels?

If both of you have empty directories, they will look identical. If you
or your neighbor has used the system before, there may be differences. What
are you working on?


Use `pwd` to **p**rint the **w**orking **d**irectory path:

```bash
[userid@login01 ~]$  pwd
```

You can run `ls` to **l**i**s**t the directory contents, though it's
possible nothing will show up (if no files have been provided). To be sure,
use the `-a` flag to show hidden files, too.

```bash
[userid@login01 ~]$  ls -a
```

At a minimum, this will show the current directory as `.`, and the parent
directory as `..`.

:::
:::

## Nodes

Individual computers that compose a cluster are typically called *nodes*
(although you will also hear people call them *servers*, *computers* and
*machines*). On a cluster, there are different types of nodes for different
types of tasks. The node where you are right now is called the *head node*,
*login node*, *landing pad*, or *submit node*. A login node serves as an access
point to the cluster.


::: callout 
## Avoid running jobs on the login node !
As a gateway, it is well suited for uploading and downloading files, setting up
software, and running quick tests. Generally speaking, the login node should
not be used for time-consuming or resource-intensive tasks. You should be alert
to this, and check with your site's operators or documentation for details of
what is and isn't allowed. In these lessons, we will avoid running jobs on the
login node.
:::

The real work on a cluster gets done by the *worker* (or *compute*) *nodes*.
Worker nodes come in many shapes and sizes, but generally are dedicated to long
or hard tasks that require a lot of computational resources.

All interaction with the worker nodes is handled by a specialized piece of
software called a scheduler (the scheduler used in this lesson is called
**Slurm**). We'll learn more about how to use the
scheduler to submit jobs next, but for now, it can also tell us more
information about the worker nodes.

For example, we can view all of the worker nodes by running the command **sinfo**.

```bash
[userid@login01 ~]$ sinfo
```

```
PARTITION   AVAIL  TIMELIMIT  NODES  STATE NODELIST
dell-gpu       up 21-00:00:0      1   idle gpu01
defq*          up 2-00:00:00      6   drng sb[019,025,029,062,069,073]
defq*          up 2-00:00:00      1  drain sb060
defq*          up 2-00:00:00     45    mix sb[001,003-004,006-007,009-012,014-015,017,023,027-028,032-036,038-039,041-045,047,050,053-054,058-059,061,066-068,072,078,084,086,092,104,108,110]
defq*          up 2-00:00:00     58  alloc sb[002,005,008,013,016,018,020-022,024,026,030-031,037,040,046,048-049,051-052,055-057,063-065,070-071,074-077,079-083,085,087-091,093-103,105-107,109]
short          up      10:00      6   drng sb[019,025,029,062,069,073]

```

There are also specialized machines used for managing disk storage, user
authentication, and other infrastructure-related tasks. Although we do not
typically logon to or interact with these machines directly, they enable a
number of key features like ensuring our user account and files are available
throughout the HPC system.

## What\'s in a Node?

All of the nodes in an HPC system have the same components as your own laptop
or desktop: *CPUs* (sometimes also called *processors* or *cores*), *memory*
(or *RAM*), and *disk* space. CPUs are a computer's tool for actually running
programs and calculations. Information about a current task is stored in the
computer's memory. Disk refers to all storage that can be accessed like a file
system. This is generally storage that can hold data permanently, i.e. data is
still there even if the computer has been restarted. While this storage can be
local (a hard drive installed inside of it), it is more common for nodes to
connect to a shared, remote fileserver or cluster of servers.

![Node anatomy](fig/node_anatomy.png){max-width="20%" alt="Node anatomy" caption=""}

::: challenge

## Explore Your Computer
Try to find out the number of CPUs and amount of memory available on your
personal computer.
Note that, if you're logged in to the remote computer cluster, you need to
log out first. To do so, type `Ctrl+d` or `exit`:

```bash
[userid@login01 ~]$  exit
[user@laptop ~]$ 
```

::: solution

There are several ways to do this. Most operating systems have a graphical
system monitor, like the Windows Task Manager. More detailed information
can sometimes be found on the command line. For example, some of the commands used on 
a Linux system are:

Run system utilities
```bash
[user@laptop ~]$  nproc --all
[user@laptop ~]$  free -m
```

Read from `/proc`
```bash
[user@laptop ~]$  cat /proc/cpuinfo
[user@laptop ~]$  cat /proc/meminfo
```

Run system monitor
```bash
[user@laptop ~]$  htop
```

:::
:::


::: challenge
## Explore the login node

Now compare the resources of your computer with those of the login node.

::: solution

```bash
[user@laptop ~]$  ssh userid@rocket.hpc
[userid@login01 ~]$  nproc --all
[userid@login01 ~]$  free -m
```

You can get more information about the processors using `lscpu`,
and a lot of detail about the memory by reading the file `/proc/meminfo`:

```bash
[userid@login01 ~]$  less /proc/meminfo
```

You can also explore the available filesystems using `df` to show **d**isk
**f**ree space. The `-h` flag renders the sizes in a human-friendly format,
i.e., GB instead of B. The **t**ype flag `-T` shows what kind of filesystem
each resource is.

```bash
[userid@login01 ~]$  df -Th
```
:::
:::

::: discussion
The local filesystems (ext, tmp, xfs, zfs) will depend on whether you're
on the same login node (or compute node, later on). Networked filesystems
(beegfs, cifs, gpfs, nfs, pvfs) will be similar --- but may include
userid, depending on how it is [mounted](
https://en.wikipedia.org/wiki/Mount_(computing)).
:::

::: callout
## Shared Filesystems

This is an important point to remember: files saved on one node
(computer) are often available everywhere on the cluster!

::: challenge
## Explore a Worker Node

Finally, let's look at the resources available on the worker nodes where your jobs will actually
run. Try running this command to see the name, CPUs and memory available on one of the worker nodes:

```bash
[userid@login01 ~]$  sinfo -n -o "%n %c %m" -o "%n %c %m"
```
:::
:::

::: discussion
## Compare Your Computer, the login node and the compute node
Compare your laptop's number of processors and memory with the numbers you
see on the cluster login node and worker node. Discuss the differences with
your neighbor.

What implications do you think the differences might have on running your
research work on the different systems and nodes?
:::

::: callout
## Differences Between Nodes

Many HPC clusters have a variety of nodes optimized for particular workloads.
Some nodes may have larger amount of memory, or specialized resources such as
Graphical Processing Units (GPUs).
:::

With all of this in mind, we will now cover how to talk to the cluster's
scheduler, and use it to start running our scripts and programs!

::: keypoints
 - An HPC system is a set of networked machines.
 - HPC systems typically provide login nodes and a set of worker nodes.
 - The standard method of interacting with such systems is via a command line
  interface called Bash.
 - The resources found on independent (worker) nodes can vary in volume and
   type (amount of RAM, processor architecture, availability of network mounted
   filesystems, etc.).
 - Files saved on one node are available on all nodes.
 - Avoid running jobs on the login node
:::
