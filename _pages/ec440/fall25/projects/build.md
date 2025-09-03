---
title: ""
permalink: /EC440/fall25/projects/build
author_profile: false  
classes: ec440-page
layout: single
---

# Build and Run

## 1. Code Guidance

Now you've had a taste of the booting process of Pintos. Let's take a closer look at what's inside. Here's the directory structure that you can see in `pintos/src`:

* <span style="color:CornFlowerBlue">`threads/`</span>

   Source code for the base kernel, which you will modify starting in project 1.

* <span style="color:CornFlowerBlue">`userprog/`</span>

   Source code for the user program loader, which you will modify starting with project 2.

* <span style="color:CornFlowerBlue">`vm/`</span>

   An almost empty directory. You will implement virtual memory here in project 3.

* <span style="color:CornFlowerBlue">`filesys/`</span>

   Source code for a basic file system. You will use this file system starting with project 2, but you will not modify it until project 4.

* <span style="color:CornFlowerBlue">`devices/`</span>

   Source code for I/O device interfacing: keyboard, timer, disk, etc. You will modify the timer implementation in project 1. Otherwise, you don't need to change this part.

* <span style="color:CornFlowerBlue">`lib/`</span>

   An implementation of a subset of the standard C library. The code in this directory is compiled into both the Pintos kernel and user programs starting from project 2 that run under it. In both kernel code and user programs, headers in this directory can be included using the #include <...> notation. You have little need to modify this part.

* <span style="color:CornFlowerBlue">`lib/kernel/`</span>
   
    Parts of the C library that are included only in the Pintos kernel. This also includes implementations of some data types that you are free to use in your kernel code: bitmaps, doubly linked lists, and hash tables. In the kernel, headers in this directory can be included using the #include <...> notation.

* <span style="color:CornFlowerBlue">`lib/user/`</span>

  Parts of the C library that are included only in Pintos user programs. In user programs, headers in this directory can be included using the #include <...> notation.

* <span style="color:CornFlowerBlue">`tests/`</span>

  Tests for each project. You can modify their codes if it helps you test your submissions. <span style="color:Crimson">When grading, we will replace your `tests/` directory with the original before we run the tests</span>.

* <span style="color:CornFlowerBlue">`examples/`</span>

  Example user programs for use starting from project 2.

* <span style="color:CornFlowerBlue">`misc/`, `utils/`</span>

  These files may come in handy if you try working with Pintos on your own machine. Otherwise, you can ignore them.

For the full code structure, you can check [here](https://web.eecs.umich.edu/~ryanph/jhu/cs318/fall22/project/pintos_1.html#SEC3)

## 2. Building Pintos

Now let's build the kernel from the source code supplied for Lab0

1. `cd` into the `threads/` directory.

2. issue the `make` command. This will create a `build` directory under `threads/`, populate it with a `Makefile` and several subdirectories, and then compile the kernel inside. The entire build process should take less than 30 seconds.

After building, the followings are the interesting files in the build/ directory:

* <span style="color:CornFlowerBlue">`Makefile`</span>

   A copy of pintos/src/Makefile.build. It describes how to build the kernel.

* <span style="color:CornFlowerBlue">`kernel.o`</span>

   Object file for the entire kernel. This is the result of linking object files compiled from each individual kernel source file into a single object file. It contains debug information, so you can run GDB or backtrace (see section Debugging) on it.

* <span style="color:CornFlowerBlue">`kernel.bin`</span>

   Memory image of the kernel, that is, the exact bytes loaded into memory to run the Pintos kernel. This is just kernel.o with debug information stripped out, which saves a lot of space, which in turn keeps the kernel from bumping up against the 512 kB size limit imposed by the kernel loader's design.

* <span style="color:CornFlowerBlue">`loader.bin`</span>
  
   Memory image for the kernel loader, a small chunk of code written in assembly language that reads the kernel from disk into memory and starts it up. It is exactly 512 bytes long, a size fixed by the PC BIOS.

Subdirectories of build contain object files (.o) and dependency files (.d), both produced by the compiler. The dependency files tell make which source files need to be recompiled when other source or header files are changed.

## 3. Running Pintos

We've supplied a program for conveniently running Pintos in a simulator (Qemu or Bochs), called `pintos`. In the simplest case, you can invoke `pintos` as `pintos [argument...]`. Each argument is passed to the Pintos kernel for it to act on.

1. `cd` into the build directory.

2. issue the command `pintos -- run alarm-multiple`, which passes the arguments run `alarm-multiple` to the Pintos kernel.

  * In these arguments, `run` instructs the kernel to run a test and alarm-multiple is the test to run.

  * This command invokes Qemu. Then Pintos boots and runs the `alarm-multiple` test program, outputing a few lines of text. When it's done, you can close Qemu by <span style="color:Crimson">`Ctrl+a+c`</span> .

  * You can log the output to a file by redirecting at the command line, e.g. pintos -- run alarm-multiple > logfile.

#### 3.1 Options

**The `pintos` program provides several options for configuring the simulator or virtual hardware.** If you specify any options, they must precede the arguments passed to the Pintos kernel and be separated from them by <span style="color:Crimson">`--`</span>. The command format is as follows:

```
pintos option1 option2 ... -- arg1 arg2 ....
```

You can invoke <span style="color:Crimson">`pintos -h`</span> to see a list of available options.

* Options can select a simulator to use: the default is Qemu, but `--bochs` selects Bochs.

* You can run the simulator with a debugger. Just select `--gdb` option.

* You can set the amount of memory to give the VM with option `-m`.

* Finally, you can select how you want VM output to be displayed: use `-v` to turn off the VGA display, `-t` to use your terminal window as the VGA display instead of opening a new window (Bochs only), or `-s` to suppress serial input from `stdin` and output to `stdout`.