---
title: Setup
---

# Setup

There are several pieces of software you will wish to install before the
workshop. Though installation help will be provided at the workshop, we
recommend that these tools are installed (or at least downloaded) beforehand.
1. [A terminal application or command-line interface](#where-to-type-commands-how-to-open-a-new-shell)
2. [A Secure Shell application](#ssh-for-secure-connections)


::: prereq

## Bash and SSH

This lesson requires a terminal application (`bash`, `zsh`, or others) with
the ability to securely connect to a remote machine (`ssh`).

:::

## Where to type commands: How to open a new shell

The shell is a program that enables us to send commands to the computer and
receive output. It is also referred to as the terminal or command line.

Some computers include a default Unix Shell program. The steps below describe
some methods for identifying and opening a Unix Shell program if you already
have one installed. There are also options for identifying and downloading a
Unix Shell program, a Linux/UNIX emulator, or a program to access a Unix Shell
on a server.

## Shell Setup

::: discussion

### Details

Setup for different systems can be presented in dropdown menus via a `solution`
tag. They will join to this discussion block, so you can give a general overview
of the software used in this lesson here and fill out the individual operating
systems (and potentially add more, e.g. online setup) in the solutions blocks.

:::

### Unix Shells on Windows

Computers with Windows operating systems do not automatically have a Unix Shell
program installed. In this lesson, we encourage you to use an emulator included
in MobaXterm for Windows, which gives you access to both Bash shell commands and SSH.

Once installed, you can open a terminal by running the program MobaXterm from
the Windows start menu.

#### Shell Programs for Windows

- [MobaXterm](https://mobaxterm.mobatek.net/download-home-edition.html) &mdash; *Recommended*
- [Git for Windows](https://gitforwindows.org/) &mdash; Alternative option used by other Software Carpentries
- [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10) &mdash; advanced option for Windows 10
- 

::: discussion
## Alternatives to MobaXterm and Git for Windows 
Other solutions are available for running Bash commands on Windows. There is
now a Bash shell command-line tool available for Windows 10. Additionally,
you can run Bash commands on a remote computer or server that already has a
Unix Shell, from your Windows machine. This can usually be done through a
Secure Shell (SSH) client. One such client available for free for Windows
computers is PuTTY. See the reference below for information on installing and
using PuTTY, using the Windows 10 command-line tool, or installing and using
a Unix/Linux emulator.

For advanced users, you may choose one of the following alternatives: 

 * Install the [Windows Subsystem for   Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10)
 * Use the Windows [Powershell](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-7)
 * Read up on [Using a Unix/Linux emulator (Cygwin) or Secure Shell (SSH) client (Putty)](https://faculty.smu.edu/reynolds/unixtut/windows.html)


## Warning

Commands in the Windows Subsystem for Linux (WSL), Powershell, or Cygwin
may differ slightly from those shown in the lesson or presented in the
workshop. Please ask if you encounter such a mismatch &mdash; you're
probably not alone.

:::



### Unix Shell on macOS

On macOS, the default Unix Shell is accessible by running the Terminal program
from the `/Application/Utilities` folder in Finder.

To open Terminal, try one or both of the following:

* In Finder, select the Go menu, then select Utilities. Locate Terminal in the
  Utilities folder and open it.
* Use the Mac ‘Spotlight’ computer search function. Search for: `Terminal` and
  press <kbd>Return</kbd>.

For an introduction, see [How to Use Terminal on a Mac](
https://www.macworld.co.uk/feature/mac-software/how-use-terminal-on-mac-3608274/)




### Linux

On most versions of Linux, the default Unix Shell is accessible by running the
[(Gnome) Terminal](https://help.gnome.org/users/gnome-terminal/stable/) or
[(KDE) Konsole](https://konsole.kde.org/) or
[xterm](https://en.wikipedia.org/wiki/Xterm), which can be found via the
applications menu or the search bar.

#### Special cases

If none of the options above address your circumstances, try an online search
for: `Unix shell [your operating system]`.

This setup takes you through getting ready for the course by ensuring you have an SSH client installed
and registering for an ARCHER2 account (you can also use an existing ARCHER2 account if you are already
a user).


## Setup an SSH client

::: discussion

### Details

All attendees should have an SSH client installed.
SSH is a tool that allows us to connect to and use a remote computer as our own.
Please follow the directions below to install an SSH client for your system if you do not 
already have one.

:::

::: solution

### Windows

Modern versions of Windows have SSH available in Powershell. You can test if it is available by typing `ssh --help` in Powershell. If it is
installed, you should see some useful output. If it is not installed, you will get an error. If SSH is not available in Powershell, then
you should install MobaXterm as described below.

An alternative is to install MobaXterm from [https://mobaxterm.mobatek.net](https://mobaxterm.mobatek.net). You will want to get the Home edition (Installer edition). However, if Powershell works, you do not need this.

:::

::: solution

### macOS

macOS comes with SSH pre-installed, so you should not need to install anything. Use your "Terminal" app.

:::

::: solution
### Linux

Linux users do not need to install anything, you should be set! Use your terminal application.

:::
