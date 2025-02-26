---
title: "Accessing software via Modules"
teaching: 30
exercises: 15
---


::: questions
 - How do we load and unload software packages?
:::

::: objectives
 - Understand how to load and use a software package.
:::

On a high-performance computing system, it is seldom the case that the software
we want to use is available when we log in. It is installed, but we will need
to "load" it before it can run.

Before we start using individual software packages, however, we should
understand the reasoning behind this approach. The three biggest factors are:

 - software incompatibilities
 - versioning
 - dependencies

Software incompatibility is a major headache for programmers. Sometimes the
presence (or absence) of a software package will break others that depend on
it. Two of the most famous examples are Python 2 and 3 and C compiler versions.
Python 3 famously provides a `python` command that conflicts with that provided
by Python 2. Software compiled against a newer version of the C libraries and
then used when they are not present will result in a nasty `'GLIBCXX_3.4.20' not found` error, for instance.

Software versioning is another common issue. A team might depend on a certain
package version for their research project - if the software version was to
change (for instance, if a package was updated), it might affect their results.
Having access to multiple software versions allow a set of researchers to
prevent software versioning issues from affecting their results.

Dependencies are where a particular software package (or even a particular
version) depends on having access to another software package (or even a
particular version of another software package). For example, the VASP
materials science software may depend on having a particular version of the
FFTW (Fastest Fourier Transform in the West) software library available for it
to work.

## Environment Modules

Environment modules are the solution to these problems. A *module* is a
self-contained description of a software package --- it contains the
settings required to run a software package and, usually, encodes required
dependencies on other software packages.

There are a number of different environment module implementations commonly
used on HPC systems: the two most common are *TCL modules* and *Lmod*. Both of
these use similar syntax and the concepts are the same so learning to use one
will allow you to use whichever is installed on the system you are using. In
both implementations the `module` command is used to interact with environment
modules. An additional subcommand is usually added to the command to specify
what you want to do. For a list of subcommands you can use `module -h` or
`module help`. As for all commands, you can access the full help on the *man*
pages with `man module`.

On login you may start out with a default set of modules loaded or you may
start out with an empty environment; this depends on the setup of the system
you are using.

### Listing Available Modules

To see available software modules, use `module avail`:

```bash
[userid@login01 userid]$  module avail
```
```output
------------------------------------------- /mnt/storage/apps/at11.0/share/modules --------------------------------------------
   modulefiles/at11.0-powerpc64le-linux-gnu

---------------------------------------------- /mnt/storage/apps/eb/modules/all -----------------------------------------------
   ABINIT/8.2.2-intel-2017.03-GCC-6.3
   ABINIT/8.4.4-foss-2017b
   ABINIT/8.4.4-intel-2017.03-GCC-6.3
   ABINIT/8.10.1-intel-2018b
   ABINIT/9.4.2-foss-2021b                                          (D)
   ABRicate/1.0.0-gompi-2021a
   AFNI/20160329-intel-2017.03-GCC-6.3-Python-2.7.12
   ALE/1.0.0-foss-2021a
   ANSYS/17.0
   ANSYS/18.1
   ANSYS/19.4
   ANSYS/2020-R2
   ANSYS/2021
   ANSYS/2022-R1
   ANSYS/2024R1                                                     (D)
   ANTLR/2.7.7-GCCcore-11.2.0-Java-11.0.2
   ANTLR/2.7.7-GCCcore-11.3.0-Java-11.0.2                           (D)
   ANTs/2.3.1-foss-2018b-Python-3.6.6
   ANTs/2.3.2-foss-2019b-Python-3.7.4
   ANTs/2.3.5-foss-2021a                                            (D)

```

### Listing Currently Loaded Modules

You can use the `module list` command to see which modules you currently have
loaded in your environment. If you have no modules loaded, you will see a
message telling you so

```bash
[userid@login01 userid]$ module list
```
```output
No modules loaded
```

## Loading and Unloading Software

To load a software module, use `module load`. In this example we will use Python 3.

Initially, Python 3 is not loaded. We can test this by using the which command. which looks for programs the same way that Bash does, so we can use it to tell us where a particular piece of software is stored.

```bash
[userid@login01 ~]$ which python3
```
If the `python3` command was unavailable, we would see output like
```output
/usr/bin/which: no python3 in (/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/mnt/nfs/home/userid/bin/:/opt/ibutils/bin:/mnt/nfs/home/userid/bin/:/mnt/nfs/home/userid/.local/bin:/mnt/nfs/home/userid/bin)
```
Note that this wall of text is really a list, with values separated by the : character. The output is telling us that the which command searched the following directories for python3, without success:

```bash
usr/local/bin
/usr/bin
/usr/local/sbin
/usr/sbin
/mnt/nfs/home/userid/bin/
/opt/ibutils/bin
/mnt/nfs/home/userid/bin/
/mnt/nfs/home/userid/.local/bin
/mnt/nfs/home/userid/bin
```



We can load the `python3` command with `module load`:

```bash
[userid@login01 ~]$ module load Python
[userid@login01 ~]$ which python3
```

```output
/mnt/storage/apps/eb/software/Python/3.7.0-foss-2018b/bin/python3
```

So, what just happened?

To understand the output, first we need to understand the nature of the `$PATH`
environment variable. `$PATH` is a special environment variable that controls
where a UNIX system looks for software. Specifically `$PATH` is a list of
directories (separated by `:`) that the OS searches through for a command
before giving up and telling us it can't find it. As with all environment
variables we can print it out using `echo`.

```bash
[userid@login01 ~]$ echo $PATH
```
```output
/mnt/storage/apps/eb/software/Python/3.7.0-foss-2018b/bin:/mnt/storage/apps/eb/software/OpenSSL/1.1.0h-GCCcore-7.3.0/bin:/mnt/storage/apps/eb/software/SQLite/3.24.0-GCCcore-7.3.0/bin:/mnt/storage/apps/eb/software/Tcl/8.6.8-GCCcore-7.3.0/bin:/mnt/storage/apps/eb/software/libreadline/7.0-GCCcore-7.3.0/bin:/mnt/storage/apps/eb/software/ncurses/6.1-GCCcore-7.3.0/bin:/mnt/storage/apps/eb/software/bzip2/1.0.6-GCCcore-7.3.0/bin:/mnt/storage/apps/eb/software/FFTW/3.3.8-gompi-2018b/bin:/mnt/storage/apps/eb/software/OpenMPI/3.1.1-GCC-7.3.0-2.30/bin:/mnt/storage/apps/eb/software/hwloc/1.11.10-GCCcore-7.3.0/sbin:/mnt/storage/apps/eb/software/hwloc/1.11.10-GCCcore-7.3.0/bin:/mnt/storage/apps/eb/software/libxml2/2.9.8-GCCcore-7.3.0/bin:/mnt/storage/apps/eb/software/XZ/5.2.4-GCCcore-7.3.0/bin:/mnt/storage/apps/eb/software/numactl/2.0.11-GCCcore-7.3.0/bin:/mnt/storage/apps/eb/software/binutils/2.30-GCCcore-7.3.0/bin:/mnt/storage/apps/eb/software/GCCcore/7.3.0/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/mnt/nfs/home/userid/bin:/opt/ibutils/bin:/mnt/nfs/home/userid/.local/bin
```


You'll notice a similarity to the output of the `which` command. In this case,
there's only one difference: the different directory at the beginning. When we
ran the `module load` command, it added a directory to the beginning of our
`$PATH`. Let's examine what's there:

```bash
[userid@login01 ~]$ ls /mnt/storage/apps/eb/software/Python/3.7.0-foss-2018b/bin
```
```output
2to3        cython            f2py     nosetests      pip3      python            python3.7m         pyvenv-3.7
2to3-3.7    cythonize         idle3    nosetests-3.7  pip3.7    python3           python3.7m-config  runxlrd.py
chardetect  easy_install      idle3.7  pbr            pydoc3    python3.7         python3-config     tabulate
cygdb       easy_install-3.7  netaddr  pip            pydoc3.7  python3.7-config  pyvenv             virtualenv
```

Taking this to its conclusion, `module load` will add software to your `$PATH`.
It "loads" software. A special note on this - depending on which version of the
`module` program that is installed at your site, `module load` will also load
required software dependencies.

To demonstrate, let’s use `module list`. `module list` shows all loaded software modules.

```bash
[userid@login01 ~]$ module list
```
```output
Currently Loaded Modules:
  1) GCCcore/7.3.0                     9) hwloc/1.11.10-GCCcore-7.3.0                 17) ncurses/6.1-GCCcore-7.3.0
  2) zlib/1.2.11-GCCcore-7.3.0        10) OpenMPI/3.1.1-GCC-7.3.0-2.30                18) libreadline/7.0-GCCcore-7.3.0
  3) binutils/2.30-GCCcore-7.3.0      11) OpenBLAS/0.3.1-GCC-7.3.0-2.30               19) Tcl/8.6.8-GCCcore-7.3.0
  4) GCC/7.3.0-2.30                   12) gompi/2018b                                 20) SQLite/3.24.0-GCCcore-7.3.0
  5) numactl/2.0.11-GCCcore-7.3.0     13) FFTW/3.3.8-gompi-2018b                      21) GMP/6.1.2-GCCcore-7.3.0
  6) XZ/5.2.4-GCCcore-7.3.0           14) ScaLAPACK/2.0.2-gompi-2018b-OpenBLAS-0.3.1  22) libffi/3.2.1-GCCcore-7.3.0
  7) libxml2/2.9.8-GCCcore-7.3.0      15) foss/2018b                                  23) OpenSSL/1.1.0h-GCCcore-7.3.0
  8) libpciaccess/0.14-GCCcore-7.3.0  16) bzip2/1.0.6-GCCcore-7.3.0                   24) Python/3.7.0-foss-2018b
```

Let's try unloading the Python module:

```bash
[userid@login01 ~]$ module unload Python
```
```output
[userid@login01 ~]$ module list

Currently Loaded Modules:
  1) GCCcore/7.3.0                     9) hwloc/1.11.10-GCCcore-7.3.0                 17) ncurses/6.1-GCCcore-7.3.0
  2) zlib/1.2.11-GCCcore-7.3.0        10) OpenMPI/3.1.1-GCC-7.3.0-2.30                18) libreadline/7.0-GCCcore-7.3.0
  3) binutils/2.30-GCCcore-7.3.0      11) OpenBLAS/0.3.1-GCC-7.3.0-2.30               19) Tcl/8.6.8-GCCcore-7.3.0
  4) GCC/7.3.0-2.30                   12) gompi/2018b                                 20) SQLite/3.24.0-GCCcore-7.3.0
  5) numactl/2.0.11-GCCcore-7.3.0     13) FFTW/3.3.8-gompi-2018b                      21) GMP/6.1.2-GCCcore-7.3.0
  6) XZ/5.2.4-GCCcore-7.3.0           14) ScaLAPACK/2.0.2-gompi-2018b-OpenBLAS-0.3.1  22) libffi/3.2.1-GCCcore-7.3.0
  7) libxml2/2.9.8-GCCcore-7.3.0      15) foss/2018b                                  23) OpenSSL/1.1.0h-GCCcore-7.3.0
  8) libpciaccess/0.14-GCCcore-7.3.0  16) bzip2/1.0.6-GCCcore-7.3.0
```

So using `module unload` “un-loads” a module, and depending on how a site is configured it may also unload all of the dependencies (in our case it does not). If we wanted to unload everything at once, we could run `module purge` (unloads everything).

```bash
[userid@login01 ~]$ module purge
[userid@login01 ~]$ module list
```
```output
No modules loaded
```

:::callout
## More on modules
Note that `module purge` is informative. It will also let us know if a default set of “sticky” packages cannot be unloaded (and how to actually unload these if we truly so desired).

Note that this module loading process happens principally through the manipulation of environment variables like `$PATH`. There is usually **little or no data transfer** involved.

The module loading process manipulates other special environment variables as well, including variables that influence where the system looks for **software libraries**, and sometimes variables which tell commercial software packages where to find **license servers**.

The module command also restores these shell environment variables to their previous state when a module is unloaded.
:::




## Software versioning

So far, we've learned how to load and unload software packages. This is very useful. However, we
have not yet addressed the issue of software versioning. At some point or other, you will run into
issues where only one particular version of some software will be suitable. Perhaps a key bugfix
only happened in a certain version, or version X broke compatibility with a file format you use. In
either of these example cases, it helps to be very specific about what software is loaded.

Let's look specifically for Python in `module avail`:
Because so many modules match `Python` we've cheated a little and added `/3` focus down on Python 3 modules

```bash
[userid@login01 userid]$  module avail Python/3
```
```output
----------------------- /mnt/storage/apps/eb/modules/all -----------------------
   GitPython/3.1.24-GCCcore-11.2.0
   Python/3.6.1-goolf-2017a
   Python/3.6.1-intel-2017.03-GCC-6.3
   Python/3.6.3-foss-2017b
   Python/3.6.6-foss-2018b
   Python/3.6.6-intel-2018b
   Python/3.7.0-foss-2018b                                   (D)
   Python/3.7.0-intel-2018b
   Python/3.7.2-GCCcore-8.2.0
   Python/3.7.4-GCCcore-8.3.0
   Python/3.7.5-GCCcore-8.3.0
   Python/3.8.2-GCCcore-9.3.0
   Python/3.8.6-GCCcore-10.2.0
   Python/3.9.5-GCCcore-10.3.0-bare
   Python/3.9.5-GCCcore-10.3.0
   Python/3.9.6-GCCcore-11.2.0-bare
   Python/3.9.6-GCCcore-11.2.0
   Python/3.10.4-GCCcore-11.3.0-bare
   Python/3.10.4-GCCcore-11.3.0
   Python/3.10.8-GCCcore-12.2.0-bare
   Python/3.10.8-GCCcore-12.2.0
   Python/3.11.3-GCCcore-12.3.0
   protobuf-python/3.3.0-intel-2017.03-GCC-6.3-Python-2.7.13
   protobuf-python/3.3.0-intel-2017.03-GCC-6.3-Python-3.6.1
   protobuf-python/3.13.0-foss-2020a-Python-3.8.2
   protobuf-python/3.14.0-GCCcore-10.2.0
   protobuf-python/3.17.3-GCCcore-10.3.0                     (D)

  Where:
   D:  Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching
any of the "keys".
```

Note that we have several different versions of `Python3`.
In this case, `Python/3.7.0-foss-2018b` has a `(D)` next to it. This indicates that it is the default - if we type `module load Python`, this is the copy that will be loaded.


::: challenge
## Using Software Modules in Scripts
Create a job that is able to run `python3 --version`. Running a job
is just like logging on to the system
(you should not assume a module loaded on the login node is loaded on a
compute node).

::: solution

```bash
[userid@login01 userid]$  nano python-module.sh
[userid@login01 userid]$  cat python-module.sh
```
```output
#!/bin/bash

#SBATCH --partition=defq
#SBATCH --time=00:00:30

module load Python

python3 --version
```

```bash
[userid@login01 userid]$  sbatch python-module.sh
```
:::
:::

## Default Versions and Module Swap

Let’s take a closer look at the `gcc` module. GCC is an extremely widely used C/C++/Fortran compiler. Lots of software is dependent on the GCC version, and might not compile or run if the wrong version is loaded. In this case, there are 18 different versions, named like `GCC/12.2.0`. How do we load each copy and which copy is the default?

```bash
[userid@login01 ~]$ module avail gcc/
```
```output
---------------------------------------------------------------------------- /mnt/storage/apps/eb/modules/all -----------------------------------------------------------------------------
   GCC/4.8.2                  GCC/4.9.3-2.25    GCC/5.4.0-2.26    GCC/6.3.0-2.27    GCC/7.3.0-2.30      GCC/8.3.0    GCC/10.2.0    GCC/11.2.0    GCC/12.2.0
   GCC/4.9.3-binutils-2.25    GCC/5.2.0         GCC/6.1.0-2.27    GCC/6.4.0-2.28    GCC/8.2.0-2.31.1    GCC/9.3.0    GCC/10.3.0    GCC/11.3.0    GCC/12.3.0 (D)

  Where:
   D:  Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".
```

In this case, `GCC/12.3.0` has a `(D)` next to it. This indicates that it is the default - if we type `module load GCC`, this is the copy that will be loaded.  Let's check this:

```bash
[userid@login01 ~]$ module purge
[userid@login01 ~]$ module load GCC
[userid@login01 ~]$ module list
```
```output
Currently Loaded Modules:
  1) GCCcore/12.3.0   2) zlib/1.2.13-GCCcore-12.3.0   3) binutils/2.40-GCCcore-12.3.0   4) GCC/12.3.0
```

```bash
[userid@login01 ~]$ gcc --version
```
```output
gcc (GCC) 12.3.0
Copyright (C) 2022 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

So how do we load the non-default copy of a software package? In this case, the only change we need to make is be more specific about the module we are loading. To load a non-default module, we need to make add the version number after the / in our module load command:

```bash
[userid@login01 ~]$ module load GCC/11.2
```
```output
The following have been reloaded with a version change:
  1) GCC/12.3.0 => GCC/11.2.0             3) binutils/2.40-GCCcore-12.3.0 => binutils/2.37-GCCcore-11.2.0
  2) GCCcore/12.3.0 => GCCcore/11.2.0     4) zlib/1.2.13-GCCcore-12.3.0 => zlib/1.2.11-GCCcore-11.2.0
```
What happened?  The module command is teling us that it swapped out `GCC/12.3.0` and replaced it with `GCC/11.2.0`



```bash
[userid@login01 ~]$ gcc --version
```
```output
gcc (GCC) 11.2.0
Copyright (C) 2021 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

:::discussion
## can't load a new module version?
Sometimes the module command gives a warning requiring you to unload the current version of a module before loading the new version.
As switching between different versions of the same module is often used you can use `module swap` rather than unloading one version before loading another. The equivalent of the steps above would be:

```bash
[userid@login01 ~]$ module purge
[userid@login01 ~]$ module load GCC
[userid@login01 ~]$ module swap GCC GCC/11.2.0
```
```output
The following have been reloaded with a version change:
  1) GCC/12.3.0 => GCC/11.2.0             3) binutils/2.40-GCCcore-12.3.0 => binutils/2.37-GCCcore-11.2.0
  2) GCCcore/12.3.0 => GCCcore/11.2.0     4) zlib/1.2.13-GCCcore-12.3.0 => zlib/1.2.11-GCCcore-11.2.0
```
And what happens when we load python again?
```bash
[userid@login01 ~]$ module load Python
```
```output
The following have been reloaded with a version change:
  1) GCC/11.2.0 => GCC/7.3.0-2.30        3) binutils/2.37-GCCcore-11.2.0 => binutils/2.30-GCCcore-7.3.0
  2) GCCcore/11.2.0 => GCCcore/7.3.0     4) zlib/1.2.11-GCCcore-11.2.0 => zlib/1.2.11-GCCcore-7.3.0
```
Because the version of python isn't compatible with the currently loaded version of gcc, module has done a swap for us.
:::


:::callout
## system python
Watch out for system provided python, it may not be the version you need.  _(It's best to always specify your python version ??)_
On Rocket, `which python` returns `/usr/bin/python`, but this turns out to be a link to Python2:
```bash
[userid@login01 ~]$ which python
/usr/bin/python
[userid@login01 ~]$ ls -l /usr/bin/python
lrwxrwxrwx. 1 root root 7 Nov  6 10:01 /usr/bin/python -> python2
```
:::

::: keypoints
 - "Load software with `module load softwareName`."
 - "Unload software with `module unload` or `module purge`"
 - "The module system handles software versioning and package conflicts for you
  automatically."
:::
