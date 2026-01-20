---
title: ""
permalink: /ec528/fall25/projects/lab2
author_profile: false  
classes: ec528-page
layout: single
---

# Lab 2 User Programs 

Now that you've worked with Pintos and are becoming familiar with its infrastructure and thread package, it's time to start working on the parts of the system that allow running user programs. The base code already supports loading and running user programs, but no I/O or interactivity is possible. In this project, you will enable programs to interact with the OS via system calls.

You will be working out of the userprog directory for this assignment, but you will also be interacting with almost every other part of Pintos. We will describe the relevant parts below.

You can build project 2 on top of your project 1 submission (dogfooding your own kernel changes like a product). Or you are free to start fresh if your project 1 is buggy. <span style="color:Crimson;">No code from project 1 is required for this assignment</span>. The "alarm clock" functionality may be useful in projects 3 and 4, but it is not strictly required. We ask that you hand in your code for this lab in a branch called lab2-handin. If you build on your project 1 submission (assuming it is in lab1-handin), create this branch with <span style="color:Crimson;">git checkout -b lab2-handin lab1-handin</span>. If you choose to start fresh, create this branch with <span style="color:Crimson;">git checkout -b lab2-handin c560a7f</span>

You might find it useful to go back and reread how to run the tests (see section [Testing](../testing.md#2-testing)).

Here are the sections in this chapter:

* [Background](./lab2_background.md)

* [Project 2 Requirements](./lab2_requirement.md)

* [FAQ](./lab2_faq.md)

# Acknowledgment
Part of this project's description and exercise is borrowed from the JHU CS318.