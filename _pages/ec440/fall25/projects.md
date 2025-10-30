---
title: ""
permalink: /EC440/fall25/projects/
author_profile: false  
classes: ec440-page
layout: single
---

# Pintos Project Guide

A significant element of this class are programming projects using [Pintos](https://pintos-os.org/). Pintos is a teaching operating system for 80x86. It is simple and small (compared to Linux). On the other hand, it is realistic enough to help you understand core OS concepts in depth. It supports kernel threads, virtual memory, user programs, and file system. But its original implementations are premature or incomplete. Through the projects, you will be strengthening all of these areas of Pintos to make it complete.

Pintos could, theoretically, run on a regular IBM-compatible PC. Unfortunately, it is impractical to supply every student a dedicated PC for use with Pintos. Therefore, we will run Pintos projects in a system simulator, that is, a program that simulates an 80x86 CPU and its peripheral devices accurately enough that unmodified operating systems and software can run under it. In this class, we will use [Bochs](http://bochs.sourceforge.net/) or [QEMU](nongnu.org/qemu/). Pintos has also been tested with [VMWare Player](https://www.vmware.com/).

These projects are challenging and have a reputation for being time-intensive, but they are also incredibly rewarding. We will try our best to help reduce the workload, but significant effort will still be required. Your feedback is invaluable—if you have suggestions to minimize unnecessary overhead, streamline assignments, or help students focus on the core concepts, please share them with us. Together, we can make this journey both productive and enjoyable.

## History

Pintos was originally developed at Stanford by Ben Pfaff blp@cs.stanford.edu to substitute for the old OS course project Nachos. After more than a decade of iterations, Pintos has been adopted by over fifty institutes as the OS course project, including Stanford, UC Berkeley, Carnegie Mellon, Johns Hopkins, and so on. You can read the original [Pintos paper](https://benpfaff.org/papers/pintos.pdf) (Yes, they even write a paper for it !) to learn the details of Pintos' design philosophy and its comparison with other instructional operating system kernels, e.g., JOS, Nachos, GeekOS, and so on.

>**Why the name "Pintos"?**
>
>First, like nachos, pinto beans are common Mexican food. Second, Pintos is small and a "pint" is a small amount. Third, like drivers of the eponymous car, students are likely to have trouble with blow-ups. —— Ben Pfaff

## Project Overview

There are five labs in total. Lab0 is designed to prepare you for the later projects and practice your GDB ability, so it is intentionally much simpler than the remaining projects. In Lab1 - 4, you will extend Pintos in different dimensions and make it more robust and powerful.

| Project  | Release | Due | Content | Point |
|----------------| ----------------| ----------------|
| [Lab 0: Getting Real](./projects/lab/lab0.md) | 09/03 | 09/19 11:59 PM EST | Bootstrap Pintos | 6 |
| [Lab 1: Threads](./projects/lab/lab1.md) | 09/17 | 10/13 11:59 PM EST | Kernel Threads Scheduling | 12 |
| [Lab 2: User Programs](./projects/lab/lab2.md) | 10/13 | 11/07 11:59 PM EST |  User programs, System calls | 12 |
| [Lab 3a: Virtual Memory](./projects/lab/lab3.md)  | 10/29 | 12/01 11:59 PM EST |  Demand Paging | 13 |
| Lab 3b: Mmap Files |  |
|(Optional) Lab4: File Systems |

In each lab, we will provide all the test cases to support your local development. After the deadline, the same test cases will be used to grade your submissions. Rest assured, your teaching assistants will not trick you with obscure corner cases designed to lower your scores.

However, we encourage you to write high-quality code and efficient algorithms. Therefore, 30% of your score will be based on the quality of your design document and code. We will provide document templates to help you limit your document to a concise length of a few hundred words.

## Important Documentation

Before running the lab, please review the [Getting Started](#getting-started) section and the following guidance:

| Guide   |
|----------------| 
| [Setup](./projects/setup.md) | 
| [Build and Run](./projects/build.md) | 
| [Debug and Test](./projects/test.md) | 
| [Grading](./projects/grading.md) |  
| [Appendix](./projects/reference/appendix.md)|

## Groups

Lab 0 is an individual project. From Lab 1 and onwards, you can work in groups of 1-3 people. We will overlap Lab 0 with the stage of forming groups. So start talking with your classmates around once the course begins!



## Getting Started

Before you can compile and develop on Pintos, please read the [setup guide](./projects/setup.md) to setup the toolchain properly.

To get started, you need to clone the [Pintos repository](https://github.com/yigonghu/ec440-pintos.git) we provided.

```
$ git clone https://github.com/yigonghu/ec440-pintos.git
```

After your toolchain is ready, you can do a quick test. 

* If you are using docker image, run the docker image and mount your <span style="color:Crimson">`path/to/pintos`</span>

    ```
    docker run -it --rm --name pintos --mount type=bind,source=/path/to/pintos,target=/home/BUOS/pintos buec440/pintos bash
    ```
    Replace <span style="color:Crimson">`/path/to/pintos`</span> with the real path where you cloned the pintos repo, e.g., <span style="color:Crimson">`/home/yigonghu/pintos`</span>.

* If you are using a virtual machine image, start the virtual machine, open a terminal, and navigate to the pintos root directory.

When you <span style="color:Crimson">`ls`</span>, you will find there is a new directory called <span style="color:Crimson">`pintos `</span> under your home directory in the container, it is shared by the container and your host computer i.e. any changes in one will be synchronized to the other instantaneously.

Now, you can do a quick test:

```
$ cd pintos/src/threads
$ make
$ cd build
$ pintos --
```

If successful, you should see the QEMU window and If you see something like:

```
Pintos hda1
Loading............
Kernel command line:
Pintos booting with 3,968 kB RAM...
367 pages available in kernel pool.
367 pages available in user pool.
Calibrating timer...  32,716,800 loops/s.
Boot complete.
```

Your Pintos has been booted successfully, congratulations :)

You can shut down the qemu by `Ctrl+a+x` or `Ctrl+c`

Now, follow the more detailed [instructions](./projects/build.md) to browse the source tree, compile and run Pintos!

## Version Control

We will be using Git for version control in the class. If you are new to Git, there are plenty of tutorials online that you can read, e.g., [this one](https://www.atlassian.com/git/tutorials).

## Grading

We will grade your assignments based on test results (70% of your grade) as well as design quality (30% of your grade). Note that the testing grades are fully automated. So please turn in working code or there is no credit. The [grading policy page](./projects/grading.md) lists detailed information about how grading is done.

## Submission 

We will be using [Gradescope](https://www.gradescope.com/courses/1115359) to distribute and collect assignments. To submit your project, upload your code as a zip file to Gradescope before the deadline. Ensure your submission includes all required files and adheres to the submission guidelines provided. Detailed submission instructions are available on each lab's page. Late submissions will be managed according to the late policy outlined below.

## Late Policies

By default, each team will be given a 6-day late-tokens in total that can spread in the four labs. It can be used for team members to prepare interviews, attend conferences, etc. When you use the grace period tokens, you just need to let us know how much of the token you want to use. We won’t be asking why. We strongly recommend you to reserve these late tokens for use in later labs (especially lab 3 and lab4), which are much more challenging than earlier labs.

Late submissions without or exceeding grace period will receive penalties as follows: 1 day late, 15% deduction; 2 days late, 30% deduction; 3 days late, 60% deduction; after 4 days, no credit.

## Cheating and Collaboration

<span style="color:red">**Warning: This class has zero tolerance for cheating. We will run tools to check your submission against a comprehensive database of solutions including past and present submissions for potential cheating. The consequences are very high.**</span>

The basic policies for the project assignments are as follows.

* Never copy project code or text found on the Internet, e.g., GitHub.
* Never share code or text on the project. That also means do not make your solutions public on the Internet.
* Never use other group's code or text in your solutions. This includes code/text from prior years or other institutions.
* You may read but not copy Linux or BSD source code. You must cite any document or code that inspired your code. As long as you cite what you used, it's not cheating. In the worst case, we deduct points if it undermines the assignment.

On the other hand, we encourage collaboration in the following form:

* Explain a concept to another student, or asking another student to explain a concept to you.
* Discuss algorithms or approaches for an exercise. But you should not exchange, look at, or copy each other's code.
* Discuss testing strategies and approaches
* Help someone else debug if they've got stuck. But you should not give that student code solutions.

The course staff will actively detect possible ethics violations. For each project submission, we will run automated cheating detection tools to check your submission against a comprehensive database of solutions including solutions on the Internet, past submissions, and solutions from other institutions.
