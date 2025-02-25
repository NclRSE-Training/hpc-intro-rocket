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

To load a software module, use `module load`. Let's say we would like
to use the NetCDF utility `ncdump`. 

On login, `ncdump` is not available. We can test this by using the `which`
command. `which` looks for programs the same way that Bash does,
so we can use it to tell us where a particular piece of software is stored.

```bash
[userid@login01 ~]$ which R
```
```output
/usr/bin/which: no R in (/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/mnt/nfs/home/ncb176/bin/:/opt/ibutils/bin:/mnt/nfs/home/ncb176/bin/:/mnt/nfs/home/ncb176/.local/bin:/mnt/nfs/home/userid/bin)
```

We can find `R` by using `module load`:

```bash
[userid@login01 userid]$  module load R
[userid@login01 userid]$  which R
```
```output
/mnt/storage/apps/eb/software/R/4.3.1-foss-2022b/bin/R
```

So, what just happened?

To understand the output, first we need to understand the nature of the
`$PATH` environment variable. `$PATH` is a special environment variable
that controls where a UNIX system looks for software. Specifically,
`$PATH` is a list of directories (separated by `:`) that the OS searches
through for a command before giving up and telling us it can't find it.
As with all environment variables we can print it out using `echo`.

```bash
[userid@login01 userid]$  echo $PATH
```
```output
/mnt/storage/apps/eb/software/R/4.3.1-foss-2022b/bin:/mnt/storage/apps/eb/software/libgit2/1.5.0-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/GDAL/3.6.2-foss-2022b/bin:/mnt/storage/apps/eb/software/Qhull/2020.2-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/Brunsli/0.1-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/OpenEXR/3.1.5-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/Xerces-C++/3.2.4-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/giflib/5.2.1-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/CFITSIO/4.2.0-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/HDF/4.2.15-GCCcore-12.2.0/bin
...
```

You'll notice a similarity to the output of the `which` command. In this case,
there's only one difference: the different directory at the beginning. When we
ran the `module load` command, it added a directory to the beginning of our
`$PATH`. Let's examine what's there:

```bash
[userid@login01 userid]$  ls /mnt/storage/apps/eb/software/R/4.3.1-foss-2022b/bin
```
```output
R  Rscript
```

In summary, `module load` will add software to your `$PATH`.
`module load` may also load additional modules with software dependencies.

To unload a module, use `module unload` with the relevant module name.

::: challenge
## Unload!
Confirm you can unload the `R` module and check what happens to the `PATH` environment variable.

::: solution
```bash
module unload R
echo $PATH
```
```output
/mnt/storage/apps/eb/software/libgit2/1.5.0-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/GDAL/3.6.2-foss-2022b/bin:/mnt/storage/apps/eb/software/Qhull/2020.2-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/Brunsli/0.1-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/OpenEXR/3.1.5-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/Xerces-C++/3.2.4-GCCcore-12.2.0/bin:
...
```
What happened?

$PATH still contains a lot of software which wasn't there before!  Looking carefully, we can see that `/mnt/storage/apps/eb/software/R/4.3.1-foss-2022b/bin` (the path to `R`) is no longer at the start of the list. We unloaded R, but not all its software dependencies. To unload R and all its dependencies, use `module purge`.
```bash
module purge R
echo $PATH
```
```output
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/mnt/nfs/home/ncb176/bin:/opt/ibutils/bin:/mnt/nfs/home/ncb176/.local/bin
```
:::
:::

## Software versioning

So far, we've learned how to load and unload software packages. This is very useful. However, we
have not yet addressed the issue of software versioning. At some point or other, you will run into
issues where only one particular version of some software will be suitable. Perhaps a key bugfix
only happened in a certain version, or version X broke compatibility with a file format you use. In
either of these example cases, it helps to be very specific about what software is loaded.

Let's examine the output of `module avail` more closely.

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

Note that we have several different versions of `Python3` plus .

::: challenge
## Using `module swap`
Load module `ANSYS` as before. Note that if we do not specifify
a particular version, we load a default version.
If we wish to change versions, we can use
`module swap <old-module> <new-module>`. Try this to obtain
`ANSYS/2022-R1`. Check what has happened to the location of the `ANSYS` software.
:::

::: challenge
## Using Software Modules in Scripts
Create a job that is able to run `ncdump --version`. Running a job
is just like logging on to the system
(you should not assume a module loaded on the login node is loaded on a
compute node).

::: solution

```bash
[userid@login01 userid]$  nano ncdump-module.sh
[userid@login01 userid]$  cat ncdump-module.sh
```
```output
#!/bin/bash
#SBATCH --partition=short
#SBATCH --qos=short
module load epcc-job-env
module load cray-netcdf
ncdump --version
```

```bash
[userid@login01 userid]$  srun python-module.sh
```
:::
:::

::: keypoints
 - "Load software with `module load softwareName`."
 - "Unload software with `module unload` or `module purge`"
 - "The module system handles software versioning and package conflicts for you
  automatically."
:::
