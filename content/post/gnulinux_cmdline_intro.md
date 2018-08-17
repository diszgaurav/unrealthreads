---
title: "An Introduction to GNU/Linux Commandline Interface"
date: 2018-07-21T13:29:52+05:30
Description: ""
Tags: []
Categories: ["GNU/Linux"]
---

# Introduction

A 'personal' computer is intended to provide user interaction. The user executes some program which runs on top of the computer's operating system and get the output. There are two types of interfaces available to do your work:

1. Graphical (User) Interface (GUI): Stuff like you click some icon which invokes the related program or does some work in an already running application. This is available through a [Desktop Environment](https://en.wikipedia.org/wiki/Desktop_environment).
2. Commandline Interface: Stuff like you type in some command and invoke the related program. This is available through a program named __shell__.

> An Operating system sits between your applications and computer hardware. It virtualizes and protects the hardware resources so that multiple process can run concurrently and safely.


# Why Commandline?

Why do we need commandline when GUI is available? The kind of work you can perform with commandline, you can't do that with GUI or do that efficiently. This is because a GUI is designed to cater some specific purposes and all the generalizations can't be met. For example, you want to list all the images of size greater than 8MB and move them into a separate dir? Think of it for a moment, how would you do it in GUI? With commandline, things like these are easy with the availability of myriad programs, its ability of argument parsing and other shell facilities (PIPE, shell expansion etc.). Nonetheless, GUI has its own place, but the kind of power you get as a user with commandline is indispensable!

> once a commandline user, always a commandline user!


# Shell and Terminal Emulator

## Shell

A shell is basically a programming language. When you invoke the shell interpreter interactively, it becomes a commandline interface. So, shell is a generic term. This means that you can have a shell with any interpreted programming language i.e. python shell, tcl shell.

For their rich set of features for commandline interface, following different shells are available on GNU/Linux systems:

| shell                                      | about                                                              |
|--------------------------------------------|--------------------------------------------------------------------|
| sh                                         | Bourne shell, first one to be released with Unix                   |
| [bash](https://www.gnu.org/software/bash/) | Bourne-Again shell, incorporates features from csh and ksh         |
| csh                                        | C shell, BSD default shell                                         |
| [tcsh](https://en.wikipedia.org/wiki/Tcsh) | Improved csh                                                       |
| [ksh](http://www.kornshell.com/)           | Korn shell                                                         |
| [zsh](http://zsh.sourceforge.net/)         | Extended Bourne shell  including useful features from tcsh and ksh |


Among these *bash* shell is the default on most GNU/Linux distributions. But, __zsh__ is becoming popular too.

> If you don't have a running GNU/Linux system, install one. [Debian](https://www.debian.org/) is a good choice.


## Terminal Emulator

A terminal emulator is a graphical window embedding a shell program. There are different terminal emulators which come pre-installed with different desktop environments:

| Desktop Environment             | Name of Terminal Emulator |
|---------------------------------|---------------------------|
| [Gnome](https://www.gnome.org/) | Terminal                  |
| [KDE](https://www.kde.org/)     | Konsole                   |
| [LXDE](http://lxde.org/)        | lxterminal                |
| [XFCE](https://www.xfce.org/)   | xfce4-terminal            |

> In case of interest, read more about [Computer Terminal](https://en.wikipedia.org/wiki/Computer_terminal)


This tutorial uses __bash__ on __Gnome Terminal__.


# Commandline Syntax

There is a [POSIX Standard](http://pubs.opengroup.org/onlinepubs/9699919799/) for command line interface. So, all standard commands (or say utilities) follow a general syntax:

```sh
utility_name[-a][-b][-c option_argument]
    [-d|-e][-f[option_argument]][operand...]
```

Commands accept options (flags or with arguments) and operands. There are short options (single character) with single hyphen (-) and long options (strings) with two hyphens (--). If it is not making sense, worry not, shortly, it will.


# A word on Filesystem

Before we move on, let's look at how files are organized on a Unix like system (yes, GNU/Linux is not Unix but is Unix like). There is a standard named Filesystem Hierarchy Standard ([FHS](http://www.pathname.com/fhs/)) for this. GNU/Linux follows __everything is a file philosophy__. Filesystem is laid out in a tree like structure. The ```root``` of the tree starts with ```/``` and paths are separated by ```/``` only. So, all files (regular), directories, devices appear in a single tree irrespective of the underlying physical location. For example, ```/bin```, ```/home/gv/```, ```/dev/audio``` are paths in a filesystem.


# Running a command

Let's enter our first command:

```sh
$ ps
```

Here __ps__ is the command and ```$``` is the __shell prompt__. ```ps``` command reports the snapshot of the current processes. A new shell, displays following output with ps command:

```sh
$ ps
  PID TTY          TIME CMD
23320 pts/5    00:00:00 bash
23391 pts/5    00:00:00 ps
```

Note that the process with PID (process id) 23320 is the shell itself! So, one way (may not be the best) to figure out which shell you are running by looking at the ps output and checking CMD column.


# The PATH Environment Variable

When we run any command, an executable file (some program on disk) gets run actually. So, when we ran ```ps``` command, a file named ps got run. Where is the file located and how did the shell figure out its location? ```which``` command tells the location of the file:

```sh
$ which ps                      # /bin/ps
```

You see that ```ps``` is located under ```/bin/``` but we are able to call it with just the name and not the complete path. How? When we execute any command, shell searches for the executable file under all ```dirs``` (directories) listed in ```PATH``` environment variable.

Let's display ```PATH``` variable:

```sh
$ echo $PATH                    # /usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
```

So, shell searches all dirs separated by colon and in priority (i.e. if there is same file in ```/usr/bin/``` and ```/bin/```, file under ```/usr/bin/``` will be executed).
Here, ```echo``` command displays any string given to it. Note that first ```$``` is part of prompt, but ```$``` in $PATH dereferences the PATH environment variable. You may display all environment variables with ```env``` command:

```sh
$ env
```


# stdin, stdout, stderr

Three file descriptors are open by default for any process unless you explicitly close them. These are:

| file descriptor(fd) | assignment               |
|---------------------|--------------------------|
| 0                   | standard input (stdin)   |
| 1                   | standard output (stdout) |
| 2                   | standard error (srderr)  |

Normally, stdin is linked to keyboard, and, stdout/stderr to the screen. stdout is for general output, and stderr, as the name suggests, for errors/warnings in the program.

So, the commands we execute, take inputs from fd 0, and shows results on fds 1 and 2.

Why this sudden talk? March on to PIPE and Redirection...


# PIPE and Redirection

One of the powerful features of shell is commands chaining i.e. output of one can be fed into input of another command and so on and so forth. This facility is provided by ```PIPE ("|")```. General syntax is:

```sh
$ cmd1 | cmd2 ...
# or,
$ cmd1 |& cmd2 ...
```

With "```|```" only stdout is connected to stdin of cmd2, but, with "```|&```" both stdout and sdterr of cmd1 is connected to stdin of cmd2.

For example, to count the lines of code of a python project:

```sh
$ find . -name '*.py' | xargs wc -l
```

Here, first ```find``` command finds all the python files (ending with .py extension) in the current working directory (explained below) and the output is fed to another program named ```wc``` (wc - print newline, word, and byte counts for each file) which with ```-l``` option shows the total line counts.
You see that separating stdout and stderr is a good design. In example above, if ```find``` throws any warning messages (to stderr), they can be displayed on shell without effecting stream being fed to ```wc```.

Command's output can be redirected to a file with the redirection operator (```>``` and ```>&```) with '```&```' carrying similar meaning. e.g.

```sh
$ echo "Shivohum" > some_file
```


# Shell Expansion
In above ```find``` example, shell searches for all python files as _*.py_. Such expansion is another powerful feature of shell i.e. shell after tokenizing the commandline, performs possible expansion. For example, ```~``` gets expanded to user's home directory, ```*```.py matches all the files having .py extension, etc.


# Users and Permissions

GNU/Linux provides a multi-user environment i.e. many users can be simultaneously logged on to the same system and working seamlessly. However, there is single ```root``` user. You may check your username and users who are logged into the system with ```who``` commands:

```sh
$ whomai
$ who
```

You might be thinking if there is a common disk and a common filesystem, how is this seamlessness maintained? Permissions! Each file (remember, everything is a file) has three kinds of access rights: *read(r)*, *write(w)* and *execute(x)*, each for three different categories: *user* (owner of the file), file *group* and *others*. This is defined by 9 bits and are represented as 3 octal digits which are arranged as:

| Owner(rwx) | Group(rwx) | Others(rwx) |

Any permission which is missing for a particular category is denoted by "```-```". Example:

```sh
$ ls -l /bin/ps
-rwxr-xr-x 1 root root 127K Jul 13 16:50 /bin/ps
```

```ls``` command lists information about files, when the file is directory, it shows its contents. Here, ```ls``` with ```-l(long listing)``` flag shows somewhat more information of the file. column 1 shows the permissions, column 3 shows the owner of the file and column 4 shows the group. So, here, owner is root, group is root and permissions are:

```sh
-rwxr-xr-x
```

- Initial ```-``` says it is a file not a directory. In case of directory, it would be ```d```
- Next triplet ```rwx``` says that the owner has full permissions to read, write and execute
- Middle triplet ```r-x``` says that groups members can read and execute file but can't write to it
- Last triplet ```r-x``` carries same information as groups for others

Instead of ```rwx```, another way of denoting permissions is with octal digits. For above example, it would be: 0755. Initial 0 signifying octal number.

# Notion of current working directory and navigation

Every process has a notion of current working directory (__cwd__). You may check the cwd of your shell with ```pwd``` (print working directory) command:

```sh
$ pwd
```

There is an environment variable ```PWD``` which reflects cwd and gets updated as you navigate in the filesystem with ```cd``` (change directory) command. ```cd``` changes the working directory. It may take absolute pathname (e.g. ```/opt/```) or path relative to cwd. Relative navigation from cwd is achieved with two special files found in every directory:

- ```.``` (dot): denotes current directory
- ```..``` (double dots): denotes parent directory

Few examples:

```sh
$ cd ~                          # cd to home directory with shell expansion
$ cd /home/gv                   # explicit cd to home dir. Your username might differ:)
$ cd ./../..                    # go two levels above from cwd
```

try it!

# Getting help

There are info/manual (help) pages available for commands, so it is a good practice to leverage that. You can pull info page with ```info``` command:

```sh
$ info ps                       #  Type 'q' to exit info screen.
```

Additionally, you may get command's options help with ```--help```:

```sh
$ ps --help                     # i.e. command --help
```

This concludes our discussion and we have just scratched the surface. The intent has been to give you the basics with a hope that it steers you in the right direction.
