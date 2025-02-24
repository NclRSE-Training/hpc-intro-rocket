[ncb176@login02 ~]$ module load R
[ncb176@login02 ~]$ module list

Currently Loaded Modules:
  1) GCCcore/12.2.0
  2) zlib/1.2.12-GCCcore-12.2.0
  3) binutils/2.39-GCCcore-12.2.0
  4) GCC/12.2.0
  5) numactl/2.0.16-GCCcore-12.2.0
  6) XZ/5.2.7-GCCcore-12.2.0
  7) libxml2/2.10.3-GCCcore-12.2.0
  8) libpciaccess/0.17-GCCcore-12.2.0
  9) hwloc/2.8.0-GCCcore-12.2.0
 10) OpenSSL/1.1
 11) libevent/2.1.12-GCCcore-12.2.0
 12) UCX/1.13.1-GCCcore-12.2.0
 13) libfabric/1.16.1-GCCcore-12.2.0
 14) PMIx/4.2.2-GCCcore-12.2.0
 15) UCC/1.1.0-GCCcore-12.2.0
 16) OpenMPI/4.1.4-GCC-12.2.0
 17) OpenBLAS/0.3.21-GCC-12.2.0
 18) FlexiBLAS/3.2.1-GCC-12.2.0
 19) FFTW/3.3.10-GCC-12.2.0
 20) gompi/2022b
 21) FFTW.MPI/3.3.10-gompi-2022b
--More--
112) Qhull/2020.2-GCCcore-12.2.0
113) LERC/4.0.0-GCCcore-12.2.0
114) GDAL/3.6.2-foss-2022b
115) MPFR/4.2.0-GCCcore-12.2.0
116) libgit2/1.5.0-GCCcore-12.2.0
117) R/4.3.1-foss-2022b



[userid@login01 userid]$  echo $PATH
```
```output
/mnt/storage/apps/eb/software/R/4.3.1-foss-2022b/bin:/mnt/storage/apps/eb/software/libgit2/1.5.0-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/GDAL/3.6.2-foss-2022b/bin:/mnt/storage/apps/eb/software/Qhull/2020.2-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/Brunsli/0.1-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/OpenEXR/3.1.5-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/Xerces-C++/3.2.4-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/giflib/5.2.1-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/CFITSIO/4.2.0-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/HDF/4.2.15-GCCcore-12.2.0/bin
...
...
b/software/libxml2/2.10.3-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/XZ/5.2.7-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/numactl/2.0.16-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/binutils/2.39-GCCcore-12.2.0/bin:/mnt/storage/apps/eb/software/GCCcore/12.2.0/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/mnt/nfs/home/ncb176/bin:/opt/ibutils/bin:/mnt/nfs/home/ncb176/.local/bin
[ncb176@login02 ~]$ module purge R
[ncb176@login02 ~]$ echo $PATH
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/mnt/nfs/home/ncb176/bin:/opt/ibutils/bin:/mnt/nfs/home/ncb176/.local/bin

## GCC
[ncb176@login02 ~]$ module avail gcc/

----------------------- /mnt/storage/apps/eb/modules/all -----------------------
   GCC/4.8.2                  GCC/6.3.0-2.27      GCC/10.2.0 (L)
   GCC/4.9.3-binutils-2.25    GCC/6.4.0-2.28      GCC/10.3.0
   GCC/4.9.3-2.25             GCC/7.3.0-2.30      GCC/11.2.0
   GCC/5.2.0                  GCC/8.2.0-2.31.1    GCC/11.3.0
   GCC/5.4.0-2.26             GCC/8.3.0           GCC/12.2.0
   GCC/6.1.0-2.27             GCC/9.3.0           GCC/12.3.0 (D)

  Where:
   L:  Module is loaded
   D:  Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching
any of the "keys".


## gromacs


[ncb176@login02 ~]$ module list
No modules loaded
[ncb176@login02 ~]$ module load gromacs
Lmod has detected the following error:  The following module(s) are
unknown: "gromacs"

Please check the spelling or version number. Also try "module spider ..."
It is also possible your cache file is out-of-date; it may help to try:
  $ module --ignore-cache load "gromacs"

Also make sure that all modulefiles written in TCL start with the string
#%Module



[ncb176@login02 ~]$ module avail gromacs

----------------------- /mnt/storage/apps/eb/modules/all -----------------------
   GROMACS/5.1.2-goolf-1.7.20gcc493-mt
   GROMACS/2016.3-goolf-2017a
   GROMACS/2016.3-intel-2017.03-GCC-6.3
   GROMACS/2020.5-foss-2020b-Python-3.8.6
   GROMACS/2020.5-RAMD-2.0-foss-2020b     (D)

  Where:
   D:  Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching
any of the "keys".


[ncb176@login02 ~]$ module load GROMACS
[ncb176@login02 ~]$ module list

Currently Loaded Modules:
  1) GCCcore/10.2.0                    11) UCX/1.9.0-GCCcore-10.2.0
  2) zlib/1.2.11-GCCcore-10.2.0        12) libfabric/1.11.0-GCCcore-10.2.0
  3) binutils/2.35-GCCcore-10.2.0      13) OpenMPI/4.0.5-GCC-10.2.0
  4) GCC/10.2.0                        14) OpenBLAS/0.3.12-GCC-10.2.0
  5) numactl/2.0.13-GCCcore-10.2.0     15) gompi/2020b
  6) XZ/5.2.5-GCCcore-10.2.0           16) FFTW/3.3.8-gompi-2020b
  7) libxml2/2.9.10-GCCcore-10.2.0     17) ScaLAPACK/2.1.0-gompi-2020b
  8) libpciaccess/0.16-GCCcore-10.2.0  18) foss/2020b
  9) hwloc/2.2.0-GCCcore-10.2.0        19) GROMACS/2020.5-RAMD-2.0-foss-2020b
 10) libevent/2.1.12-GCCcore-10.2.0

 


