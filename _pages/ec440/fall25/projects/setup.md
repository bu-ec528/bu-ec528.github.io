---
title: ""
permalink: /EC440/fall25/projects/setup
author_profile: false  
classes: ec440-page
layout: single
---

# Setup Instructions

To develop the Pintos projects, you will need to have a machine with the appropriate environment setup. The SCC lab machines support Pintos development. You can also work on the projects on your own machine (e.g., Ubuntu, Fedora, Mac OS). You’ll need two essential sets of tools:

* 80x86 cross-compiler toolchain for 32-bit architecture including a C compiler, assembler, linker, and debugger.
* x86 emulator, QEMU or Bochs

This page contains instructions to help you with the setup of the core development environment needed for Pintos on your own machines. They are intended for Unix and Mac OS machines. If you are running Windows, we recommend you to run a virtual machine with Linux or you will have to setup [Cygwin](https://www.cygwin.com/) first. This guide, and the course in general, assumes you are familiar with Unix commands.

## 1. Development Environment

### Option A (recommended for Linux): Docker

1.  
First, you need to install docker on your computer. Go to the [docker download page](https://www.docker.com/get-started) for help.

2. Then pull the docker image and run it. Just type the command below into your favorite shell (you can run <span style="color:Crimson">`docker run --help`</span> to find out what this command means in detail):

```
docker run -it buec440/pintos bash
```

This image is about 3GB (it contains a full Ubuntu18.04), so it may take some time at its first run. 
If everything goes well, you will enter a bash shell.

* Type `pwd` you will find your home directory is under `/home/BUOS`

* Type `ls` you will find that there is a `toolchain` directory that contains all the dependencies.

Now you own a tiny Ubuntu OS inside your host computer, and you can shut it down easily by `Ctrl+d` or type `exit + enter`. You can check that it has exited by running `docker ps -a`.

> **Note:**
> If your computer runs macOS, you may encounter issues with Docker due to unsupported system calls in macOS's virtualization framework.

### Option B (recommended for macOS and Windows): Virtual Machine

For macOS users, Docker may not work as expected. If you encounter issues, you can use a virtual machine for development. We provides a [UTM](https://mac.getutm.app/) running Ubuntu 18.04 with the necessary toolchain installed. You can download the image [here](https://bushare-my.sharepoint.com/:u:/g/personal/yigongh_bu_edu/EZX92xAMrqFHkl3Jh-CVswUBQQ1qfGbXlqcWqPoIEfHBuA). The image is large (5 GB), so the download may take some time. The initial login password is `buec440`.

### Option C: Build Your Own Machine

* If you computer runs Linux, it is the perfect development environment for Pintos. In terms of suitable Linux distributions, Ubuntu 18.04 is what we use. But you may also try other distributions like Ubuntu 24.04 or Fedora.
* If your computer runs macOS, it should work too. Follow step 2 below to build the toolchain, enabling native compilation of Pintos on macOS. However, the virtual machine option is recommended as native compilation may encounter issues. Feel free to choose the option that best suits your needs.
* If you are using a Windows machine, however, we recommend you to install a Linux virtual machine.

### Option D: SCC Machine

TBD


## 2. Compiler Toolchain

> **Note:**
> If you choose option A, you can skip the following part and go back to [Getting Started](../projects.md).
> This method is not fully tested, so we may not provide much help if you encounter unexpected errors.

### 2.1: Test Your Compiler Toolchain

The compiler toolchain is a collection of tools that turns source code into binary executables for a target architecture. Pintos is written in C and x86 assembly, and runs on 32-bit x86 machines. So we will need the appropriate C compiler (<span style="color:Crimson">`gcc`</span>), assembler (<span style="color:Crimson">`as`</span>), linker (<span style="color:Crimson">`ld`</span>) and debugger (<span style="color:Crimson">`gdb`</span>).

If you are using a Linux machine, it is likely equipped with the compiler toolchain already. But it should support 32-bit x86 architecture. A quick test of the support is to run <span style="color:Crimson">`objdump -i | grep elf32-i386`</span> in the terminal. If it returns matching lines, your system’s default tool chain supports the target, then you can skip Section 2.2. Otherwise, you will need to build the toolchain from source.

> **Note:**  
> If you are using MacOS, you have to build the toolchain from source because MacOS's object file format is not ELF that we need, and the objdump -i test won't work.

### 2.2: Build Toolchain from Source

When you are building the toolchain from source, to distinguish the new toolchain from your system’s default one, you should add a <span style="color:Crimson">`i386-elf-`</span> prefix to the build target, e.g., <span style="color:Crimson">`i386-elf-gcc`</span>,  <span style="color:Crimson">`i386-elf-as`</span>.

#### 2.2.1 Prerequisite

* Standard build tools including <span style="color:Crimson">`make`</span>, <span style="color:Crimson">`gcc`</span>, etc. To build GDB, you will need the <span style="color:Crimson">`ncurses`</span> and <span style="color:Crimson">`texinfo`</span> libraries.
* For Ubuntu, you can install these packages with

```
$ sudo apt-get install build-essential automake git libncurses5-dev texinfo
```

* For macOS, first you should have the command-line tools in Xcode installed:

```
$ xcode-select --install
```

You can then install  <span style="color:Crimson">`ncurses`</span> and <span style="color:Crimson">`texinfo`</span> with brew:

```
$ brew install ncurses texinfo
```

#### 2.2.2 The Easy Way

We've provided a [script](https://github.com/yigonghu/ec440-pintos/blob/main/src/misc/toolchain-build.sh) (pintos/src/misc/toolchain-build.sh) that automates the building instructions. So you can just run the script and modify your PATH setting after the build finishes. The script has been tested on recent version of Ubuntu, Mac OS and Fedora.

Replace <span style="color:Crimson">`/path/to/setup`</span> below with a real path to store the toolchain source and build, e.g., <span style="color:Crimson">`/home/BUOS/toolchain`</span> ; and replace <span style="color:Crimson">`/path/to/pintos`</span> with the real path where you cloned the pintos repo, e.g., <span style="color:Crimson">`/home/BUOS/pintos`</span>.

```
$ SWD=/path/to/setup
$ mkdir -p $SWD
$ cd /path/to/pintos
$ src/misc/toolchain-build.sh $SWD
```

If the above commands succeeded, add the toolchain path to your PATH environment variable settings in the <span style="color:Crimson">`.bashrc`</span>  (or <span style="color:Crimson">`zshrc`</span> . if you are using zsh ) file in your home directory.

```
export PATH=$SWD/x86_64/bin:$PATH
```

Don’t forget to replace the <span style="color:Crimson">`$SWD`</span> above with the real path, e.g., <span style="color:Crimson">`export PATH=/home/BUOS/toolchain/x86_64/bin:$PATH`</span>.

> **Note:**
> If you are using macOS and the above compilation failed. It might be caused by changes in the latest gcc or libraries on macOS. You can post the errors in the discussion forum. We will investigate.

## 3. Emulator

Besides the cross-compiler toolchain, we also need an x86 emulator to run Pintos OS. We will use two popular emulators QEMU and Bochs.

### 3.1 QEMU
* QEMU is modern and fast. To install it:
    * For Ubuntu: sudo apt-get install qemu
    * For MacOS: brew install qemu
    * Other: build it from source

### 3.2 Bochs
* Bochs is slower than QEMU but provides full emulation (i.e., higher accuracy).
* For Lab 1, we will use Bochs as the default emulator and for Lab 2-4, we will use QEMU as the default emulator. Nevertheless, nothing will prevent you from using one or another for all the labs.
* There are some bugs in Bochs that should be fixed when using it with Pintos. Thus, we need to install Bochs from source, and apply the patches that we have provided under pintos/src/misc/bochs*.patch. We will build two versions of Bochs: one, simply named bochs, with the GDB stub enabled, and the other, named bochs-dbg, with the built-in debugger enabled.
* Version 2.6.2 (note: not 2.2.6) has been tested to work with Pintos. Newer version of Bochs has not been tested.

> **Note:**
> We have provided a build script <span style="color:Crimson">`pintos/src/misc/bochs-2.6.2-build.sh`</span> that will download, patch and build two versions of the Bochs for you. But you need to make sure X11 and its library is installed.
* For Mac OS, you should install XQuartz 2.7.11 ([here](https://www.xquartz.org/releases/index.html)). Note: bochs build does not work with XQuartz version 2.8.x. Later XQuartz might prompt you to upgrade to 2.8.x, please do not upgrade it!
* For Ubuntu, <span style="color:Crimson">`sudo apt-get install libx11-dev libxrandr-dev`</span>

(<span style="color:Crimson">`$SWD`</span> should be set previously, e.g.,<span style="color:Crimson">`/home/BUOS/toolchain`</span>)

```
$ src/misc/bochs-2.6.2-build.sh $SWD/x86_64
```

## 4. Pintos Utility Tools

The Pintos source distribution comes with a few handy scripts that you will be using frequently. They are located within <span style="color:Crimson">`src/utils/`</span>. The most important one is the pintos Perl script, which you will be using to start and run tests in pintos. You need to make sure it can be found in your PATH environment variable. In addition, the <span style="color:Crimson">`src/misc/gdb-macros`</span> is provided with a number of GDB macros that you will find useful when you are debugging Pintos. The <span style="color:Crimson">`pintos-gdb`</span> is a wrapper around the <span style="color:Crimson">`i386-elf-gdb`</span> that reads this macro file at start. It assumes the macro file resides in <span style="color:Crimson">`../misc`</span>.

The commands to do the above setup for the Pintos utilities are: (make sure SWD is set previously to the correct directory path)

```
$ dest=$SWD/x86_64
$ cd pintos/src/utils && make
$ cp backtrace pintos Pintos.pm pintos-gdb pintos-set-cmdline pintos-mkdisk setitimer-helper squish-pty squish-unix $dest/bin
$ mkdir $dest/misc
$ cp ../misc/gdb-macros $dest/misc
```