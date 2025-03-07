---
title: "Transferring files with remote computers"
teaching: 15
exercises: 15
---


::: questions
 - How do I transfer files to (and from) the cluster?
:::

::: objectives
 - `wget` and `curl -O` download a file from the internet.
 - `scp` transfers files to and from your computer.
:::

::: prereq

## Required Files

The program used in this example can be retrieved using wget or a browser on your laptop and then copied to the remote HPC.

**Using wget**: 
```bash
[userid@login01 ~]$ wget http://training.researchcomputing.ncl.ac.uk/training-materials/hpc-intro-data.tar.gz
```

**Using a web browser**: 

[http://training.researchcomputing.ncl.ac.uk/training-materials/hpc-intro-data.tar.gz](http://training.researchcomputing.ncl.ac.uk/training-materials/hpc-intro-data.tar.gz)
:::

Computing with a remote computer offers very limited use if we cannot get files
to or from the cluster. There are several options for transferring data between
computing resources, from command line options to GUI programs, which we will
cover here.

## Download Files From the Internet

One of the most straightforward ways to download files is to use either `curl`
or `wget`, one of these is usually installed in most Linux shells, on Mac OS
terminal and in GitBash. Any file that can be downloaded in your web browser 
through a direct link can be downloaded using `curl -O` or `wget`. This is a 
quick way to download datasets or source code. 

The syntax for these commands is: `curl -O https://some/link/to/a/file` 
and `wget https://some/link/to/a/file`. Try it out by downloading
some material we'll use later on, from a terminal on your local machine.

```bash
[user@laptop ~]$  curl -O http://training.researchcomputing.ncl.ac.uk/training-materials/hpc-intro-data.tar.gz
```
or
```bash
[user@laptop ~]$  wget http://training.researchcomputing.ncl.ac.uk/training-materials/hpc-intro-data.tar.gz
```

::: discussion


## `tar.gz`?
This is an archive file format, just like `.zip`, commonly used and supported
by default on Linux, which is the operating system the majority of HPC
cluster machines run. You may also see the extension `.tgz`, which is exactly
the same. We'll talk more about "tarballs," since "tar-dot-g-z" is a
mouthful, later on.
:::

## Transferring Single Files and Folders With `scp`

To copy a single file to or from the cluster, we can use `scp` ("secure copy").
The syntax can be a little complex for new users, but we'll break it down.

To *upload to* another computer:

```bash
[user@laptop ~]$  scp path/to/local/file.txt userid@rocket.hpc:/path/on/Rocket
```

To *download from* another computer:

```bash
[user@laptop ~]$  scp userid@rocket.hpc:/path/on/Rocket/file.txt path/to/local/
```

Note that everything after the `:` is optional. If you don't specify a path on the
remote computer, the file will be transferred to your home directory.

```bash
[user@laptop ~]$  scp local-file.txt userid@rocket.hpc:
```

::: challenge
## Upload a File
Copy the file you just downloaded from the Internet to your home directory on
Rocket.

::: solution

```bash
[user@laptop ~]$  scp hpc-intro-data.tar.gz userid@rocket.hpc:~/
```
:::
:::


::: challenge

## Can you download to the HPC directly?

Try downloading the file directly using `curl` or `wget`. Do the commands understand file locations on your local machine over SSH? Note that it may well fail, and that's
OK!

::: solution

Using `curl` or `wget` commands like the following:

```bash
[user@laptop ~]$  ssh userid@rocket.hpc
[userid@login01 ~]$ curl -O http://training.researchcomputing.ncl.ac.uk/training-materials/hpc-intro-data.tar.gz
or
[userid@login01 ~]$ wget http://training.researchcomputing.ncl.ac.uk/training-materials/hpc-intro-data.tar.gz
```

Did it work? If not, what does the terminal output tell you about what
happened?

### Why Not Download on Rocket Directly?
Some computer clusters are behind firewalls set to only allow transfers
initiated from the *outside*. This means that the `curl` command will fail,
as an address outside the firewall is unreachable from the inside. To get
around this, run the `curl` or `wget` command from your local machine to 
download the file, then use the `scp` command (just below here) to upload
it to the cluster.
:::
:::

## Transferring a Directory
To copy a whole directory, we add the `-r` flag, for "**r**ecursive": copy the
item specified, and every item below it, and every item below those... until it
reaches the bottom of the directory tree rooted at the folder name you
provided.

```bash
[user@laptop ~]$  scp -r some-local-folder userid@rocket.hpc:target-directory/
```

::: callout
## Caution

For a large directory --- either in size or number of files --- copying with `-r` can take a long time to complete.
:::

## What's in a `/`?

When using `scp`, you may have noticed that a `:` *always* follows the remote
computer name; sometimes a `/` follows that, and sometimes not, and sometimes
there's a final `/`. On Linux computers, `/` is the ***root*** directory, the
location where the entire filesystem (and others attached to it) is anchored. A
path starting with a `/` is called *absolute*, since there can be nothing above
the root `/`. A path that does not start with `/` is called *relative*, since
it is not anchored to the root.

If you want to upload a file to a location inside your home directory ---
which is often the case --- then you don't need a leading `/`. After the
`:`, start writing the sequence of folders that lead to the final storage
location for the file or, as mentioned above, provide nothing if your home
directory *is* the destination.

A trailing slash on the target directory is optional, and has no effect for
`scp -r`, but is important in other commands, like `rsync`.

## Transferring files to and from Campus Storage for Research Data (RDW)
RDW is mounted on Rocket at `/rdw`.  You can use scp and rsync to transfer data to RDW in the same way as copying to any other directory on Rocket.  

### Using cp to copy to RDW
Because `/rdw` is a mounted filesystem, we an use `cp` instead of `scp`:

```bash
[userid@login01 ~]$ cp file.txt /rdw/03/rse-hpc/rockhpc_training_TEMP/userid/
[userid@login01 rse-hpc]$ cd /rdw/03/rse-hpc/rockhpc_training_TEMP/userid/
[userid@login01 userid]$ pwd
/rdw/03/rse-hpc/rockhpc_training_TEMP/userid
[userid@login01 userid]$ ls
```
```ouptput
file.txt
```



::: discussion

## A Note on `rsync`
As you gain experience with transferring files, you may find the `scp`
command limiting. The [rsync](https://rsync.samba.org/) utility provides
advanced features for file transfer and is typically faster compared to both
`scp` and `sftp` (see below). It is especially useful for transferring large
and/or many files and creating synced backup folders.
The syntax is similar to `cp` and `scp`. 

To transfer *to* RDW from your work area on Rocket

Try out a dry run:
```bash
[userid@login01 ~]$ rsync -av testDir/ /rdw/03/rse-hpc/rockhpc_training_TEMP/userid --dry-run
```
```output
sending incremental file list
./
file.txt
file2.txt

sent 119 bytes  received 25 bytes  288.00 bytes/sec
total size is 39  speedup is 0.27 (DRY RUN)
```
Run ‘for real’:
```bash
[userid@login01 ~]$ rsync -av testDir/ /rdw/03/rse-hpc/rockhpc_training_TEMP/userid
```
```output

sending incremental file list
rsync: chgrp "/rdw/03/rse-hpc/rockhpc_training_TEMP/userid/." failed: Invalid argument (22)
./
file.txt
file2.txt
rsync: chgrp "/rdw/03/rse-hpc/rockhpc_training_TEMP/userid/.file.txt.SS0gps" failed: Invalid argument (22)
rsync: chgrp "/rdw/03/rse-hpc/rockhpc_training_TEMP/userid/.file2.txt.XAYkoB" failed: Invalid argument (22)

sent 234 bytes  received 376 bytes  1,220.00 bytes/sec
total size is 39  speedup is 0.06
rsync error: some files/attrs were not transferred (see previous errors) (code 23) at main.c(1179) [sender=3.1.2]
```
What happened?  `rsync` returned an error. `files/attrs were not transferred `  This is because RDW doesn't 'know' about rocket's groups.  The transfer was successful though! Only the 'group' attribute of the file couldn't be transferred.  RDW has 'trumped' our local permissions and imposed its own standard permissions.  This isn't important, the correct user keeps ownership of the files.

```bash
[userid@login01 ~]$ ls -l testDir/
```
```output
total 4
-rw------- 1 userid rocketloginaccess  0 Feb 26 15:31 file2.txt
-rw------- 1 userid rocketloginaccess 39 Feb 26 15:31 file.txt
```
```bash
[userid@login01 ~]$ ls -l /rdw/03/rse-hpc/rockhpc_training_TEMP/userid/
```
```output
total 57
-rwxrwx--- 1 userid domainusers  0 Feb 26 15:52 file2.txt
-rwxrwx--- 1 userid domainusers 39 Feb 26 15:52 file.txt
```

It's easier to read output without errors that we have to ignore, so let's remove that error by allowing the destination to set its default group using `--no-group`.  It's also possible to add `--no-owner`, allowing the destination to set file ownership to match the directory we're copying into.

```bash
[userid@login01 ~]$ rsync -av testDir/ /rdw/03/rse-hpc/rockhpc_training_TEMP/userid --no-group
```
```output
sending incremental file list
./
file.txt
file2.txt

sent 203 bytes  received 57 bytes  
```
:::


## Windows Users - Transferring Files interactively with MobaXterm
MobaXterm is a free ssh client.  It allows connections via a 'jump host' so can even be used from home.


:::callout
## Large data copies
When copying large amounts of data, rsync really comes into its own.  If a copy is interrupted, the same command can pick up where it left off.

### special case:  rsync on the same filesytem / very fast connection
RDW has a super-fast connection to Rocket, which means that it takes more resource to compress and un-compress the data than it does to do the transfer

[userid@login01 ~]$ rsync -rltv testDir/ /rdw/03/rse-hpc/rockhpc_training_TEMP/userid

The `-a` option preserves permissions, this is why we saw group modification errors above.  For Rocket and RDW, replace `-avzP` with `-rltv`
`-r` = recurse through subdirectories
`-l` = copy symlinks
`-t` = preserve timestamps
--inplace --whole-file --size-only to speed up transfer and prevent rsync filling up space with a large temp directory
--itemize-changes --progress --stats for more informative output

For big transfers, rerun the rsync command to check it completed.
:::

:::callout
## To transfer *to* Rocket from a campus laptop
Because the transfer has to pass through the slower campus network, or even the internet, it's a good idea to use rsync's built-in compression `-z`
commonly used options:

```bash
[user@laptop ~]$  rsync -avzP path/to/local/file.txt userid@rocket.hpc:directory/path/on/Rocket/
```

The `a` (archive) option preserves file timestamps and permissions among
other things; the `v` (verbose) option gives verbose output to help monitor
the transfer; the `z` (compression) option compresses the file during transit
to reduce size and transfer time; and the `P` (partial/progress) option
preserves partially transferred files in case of an interruption and also
displays the progress of the transfer.

To recursively copy a directory, we can use the same options:

```bash
[user@laptop ~]$  rsync -avzP path/to/local/dir userid@rocket.hpc:directory/path/on/Rocket/
```

As written, this will place the local directory and its contents under the
specified directory on the remote system. If the trailing slash is omitted on
the destination, a new directory corresponding to the transferred directory
('dir' in the example) will not be created, and the contents of the source
directory will be copied directly into the destination directory.

The `a` (archive) option implies recursion.

To download a file, we simply change the source and destination:

```bash
[user@laptop ~]$  rsync -avzP userid@rocket.hpc:path/on/Rocket/file.txt path/to/local/
```
:::

::: discussion

## Large copies over fast and slow networks with `rsync`
When you're copying a lot of data, it's important to keep track in case the copy is interrupted.  Rsync is great because it can pick up where it left off, rather than starting the copy all over again.  It's useful to output to a log so you can see what was transferred and find any errors that need to be addressed.

### Fast Connections
Transfers from Rocket to RDW don’t leave our fast data centre network.  If you're using rsync with a fast network or disk to disk in the same machine:

Don’t use compression -z
Do use --inplace

Why?  compression uses lots of CPU, Rsync usually creates a temp file on disk before copying.  For fast transfers, this places too much load on the CPU and hard drive.  --inplace tells rsync not to create the temp file but send the data straight away.  It doesn’t matter if the connection is interrupted, because rsync keeps track and tries again.  Always re-run transfer command to ensure nothing was missed.  The second run should be very fast, just listing all the files and not copying anything.

### Slow Connections
For a slow connection like the internet, DO use compression and DON’T use --inplace.  
:::

:::challenge
### Back up data from rocket to RDW
Set up an rsync command to copy files and provide helpful output to a log file.

:::solution
Try out a dry run:
```bash
rsync --dry-run -avv --inplace --itemize-changes --progress --stats --whole-file --size-only /nobackup/myusername/source /rdw/path/to/my/share/destination/ 2>&1 | tee /home/myusername/meaningful-log-name.log1
```
Run ‘for real’:
```bash
rsync -avv --inplace --itemize-changes --progress --stats --whole-file --size-only /nobackup/myusername/source /rdw/path/to/my/share/destination/ 2>&1 | tee /home/myusername/meaningful-log-name.log2
```
:::
:::


::: discussion
## A Note on Ports
All file transfers using the above methods use SSH to encrypt data sent
through the network. So, if you can connect via SSH, you will be able to
transfer files. By default, SSH uses network port 22. If a custom SSH port is
in use, you will have to specify it using the appropriate flag, often `-p`,
`-P`, or `--port`. Check `--help` or the `man` page if you're unsure.

:::

::: challenge
#### Rsync Port

Say we have to connect `rsync` through port 768 instead of 22. How would we
modify this command?

```bash
[user@laptop ~]$  rsync test.txt userid@rocket.hpc:
```

::: solution

```bash
[user@laptop ~]$  rsync --help | grep port
     --port=PORT             specify double-colon alternate port number
See https://rsync.samba.org/ for updates, bug reports, and answers
[user@laptop ~]$  rsync --port=768 test.txt userid@rocket.hpc:
```
:::
:::

## Archiving Files

One of the biggest challenges we often face when transferring data between
remote HPC systems is that of large numbers of files. There is an overhead to
transferring each individual file and when we are transferring large numbers of
files these overheads combine to slow down our transfers to a large degree.

The solution to this problem is to *archive* multiple files into smaller
numbers of larger files before we transfer the data to improve our transfer
efficiency. Sometimes we will combine archiving with *compression* to reduce
the amount of data we have to transfer and so speed up the transfer.

The most common archiving command you will use on a (Linux) HPC cluster is
`tar`. `tar` can be used to combine files into a single archive file and,
optionally, compress it.

Let's start with the file we downloaded from the lesson site,
`hpc-lesson-data.tar.gz`. The "gz" part stands for *gzip*, which is a
compression library. Reading this file name, it appears somebody took a folder
named "hpc-lesson-data," wrapped up all its contents in a single file with
`tar`, then compressed that archive with `gzip` to save space. Let's check
using `tar` with the `-t` flag, which prints the "**t**able of contents"
without unpacking the file, specified by `-f <filename>`, on the remote
computer. Note that you can concatenate the two flags, instead of writing
`-t -f` separately.

```bash
[user@laptop ~]$  ssh userid@rocket.hpc
[userid@login01 ~]$ tar -tf hpc-lesson-data.tar.gz
hpc-intro-data/
hpc-intro-data/north-pacific-gyre/
hpc-intro-data/north-pacific-gyre/NENE01971Z.txt
hpc-intro-data/north-pacific-gyre/goostats
hpc-intro-data/north-pacific-gyre/goodiff
hpc-intro-data/north-pacific-gyre/NENE02040B.txt
hpc-intro-data/north-pacific-gyre/NENE01978B.txt
hpc-intro-data/north-pacific-gyre/NENE02043B.txt
hpc-intro-data/north-pacific-gyre/NENE02018B.txt
hpc-intro-data/north-pacific-gyre/NENE01843A.txt
hpc-intro-data/north-pacific-gyre/NENE01978A.txt
hpc-intro-data/north-pacific-gyre/NENE01751B.txt
hpc-intro-data/north-pacific-gyre/NENE01736A.txt
hpc-intro-data/north-pacific-gyre/NENE01812A.txt
hpc-intro-data/north-pacific-gyre/NENE02043A.txt
hpc-intro-data/north-pacific-gyre/NENE01729B.txt
hpc-intro-data/north-pacific-gyre/NENE02040A.txt
hpc-intro-data/north-pacific-gyre/NENE01843B.txt
hpc-intro-data/north-pacific-gyre/NENE01751A.txt
hpc-intro-data/north-pacific-gyre/NENE01729A.txt
hpc-intro-data/north-pacific-gyre/NENE02040Z.txt
```

This shows a folder containing another folder, which contains a bunch of files.
If you've taken The Carpentries' Shell lesson recently, these might look
familiar. Let's see about that compression, using `du` for "**d**isk
**u**sage".

```bash
[userid@login01 ~]$ du -sh hpc-lesson-data.tar.gz
36K     hpc-intro-data.tar.gz
```

::: callout
## Files Occupy at Least One "Block"

If the filesystem block size is larger than 36 KB, you'll see a larger
number: files cannot be smaller than one block.
:::

Now let's unpack the archive. We'll run `tar` with a few common flags:

* `-x` to e**x**tract the archive
* `-v` for **v**erbose output
* `-z` for g**z**ip compression
* `-f` for the file to be unpacked

When it's done, check the directory size with `du` and compare.

::: challenge

## Extract the Archive
Using the four flags above, unpack the lesson data using `tar`.
Then, check the size of the whole unpacked directory using `du`.
Hint: `tar` lets you concatenate flags.

::: solution

#### Commands

```bash
[userid@login01 ~]$ tar -xvzf hpc-lesson-data.tar.gz
```
```output
hpc-intro-data/
hpc-intro-data/north-pacific-gyre/
hpc-intro-data/north-pacific-gyre/NENE01971Z.txt
hpc-intro-data/north-pacific-gyre/goostats
hpc-intro-data/north-pacific-gyre/goodiff
hpc-intro-data/north-pacific-gyre/NENE02040B.txt
hpc-intro-data/north-pacific-gyre/NENE01978B.txt
hpc-intro-data/north-pacific-gyre/NENE02043B.txt
hpc-intro-data/north-pacific-gyre/NENE02018B.txt
hpc-intro-data/north-pacific-gyre/NENE01843A.txt
hpc-intro-data/north-pacific-gyre/NENE01978A.txt
hpc-intro-data/north-pacific-gyre/NENE01751B.txt
hpc-intro-data/north-pacific-gyre/NENE01736A.txt
hpc-intro-data/north-pacific-gyre/NENE01812A.txt
hpc-intro-data/north-pacific-gyre/NENE02043A.txt
hpc-intro-data/north-pacific-gyre/NENE01729B.txt
hpc-intro-data/north-pacific-gyre/NENE02040A.txt
hpc-intro-data/north-pacific-gyre/NENE01843B.txt
hpc-intro-data/north-pacific-gyre/NENE01751A.txt
hpc-intro-data/north-pacific-gyre/NENE01729A.txt
hpc-intro-data/north-pacific-gyre/NENE02040Z.txt
```

Note that we did not type out `-x -v -z -f`, thanks to the flag
concatenation, though the command works identically either way.

```bash
[userid@login01 ~]$ du -sh hpc-lesson-data
144K    hpc-intro-data
```
:::

#### Was the Data Compressed?

Text files compress nicely: the "tarball" is one-quarter the total size of the raw data!
:::

If you want to reverse the process --- compressing raw data instead of
extracting it --- set a `c` flag instead of `x`, set the archive filename,
then provide a directory to compress:

```bash
[user@laptop ~]$  tar -cvzf compressed_data.tar.gz hpc-intro-data
```

::: discussion

## Working with Windows
When you transfer text files to from a Windows system to a Unix system (Mac,
Linux, BSD, Solaris, etc.) this can cause problems. Windows encodes its files
slightly different than Unix, and adds an extra character to every line.
On a Unix system, every line in a file ends with a `\n` (newline). On
Windows, every line in a file ends with a `\r\n` (carriage return + newline).
This causes problems sometimes.
Though most modern programming languages and software handles this correctly,
in some rare instances, you may run into an issue. The solution is to convert
a file from Windows to Unix encoding with the `dos2unix` command.
You can identify if a file has Windows line endings with `cat -A filename`. A
file with Windows line endings will have `^M$` at the end of every line. A
file with Unix line endings will have `$` at the end of a line.
To convert the file, just run `dos2unix filename`. (Conversely, to convert
back to Windows format, you can run `unix2dos filename`.)

:::

::: keypoints
 - "`wget` and `curl -O` download a file from the internet."
 - "`scp` transfers files to and from your computer."
:::
