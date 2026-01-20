---
title: ""
permalink: /ec528/fall25/projects/lab1/requirement
author_profile: false  
classes: ec528-page
layout: single
---

# Submission Instruction
We will be using [GitHub classroom](https://classroom.github.com/a/kYV5Orqs) to distribute and collect assignments. You do not have to do anything special to submit your project. We will use a snapshot of your GitHub repository as it exists at the deadline, and grade that version. You can still make changes to your repository after the deadline. But we will be only using the snapshot of your code as of the deadline.

We will collect your solution automatically through GitHub by taking a snapshot by the deadline. Thus, be sure to commit your changes and do a <span style="color:Crimson;">**git push**</span> to GitHub, especially in the last few minutes! <span style="color:Crimson;">**Your submission must reside in a branch called lab1-handin**</span>. You can create this branch with <span style="color:Crimson;">**git checkout -b lab1-handin**</span>. You can use other branches (e.g., <span style="color:Crimson;">**main**</span> or <span style="color:Crimson;">**lab1-dev**</span>) during development, but be sure to sync these changes to the submission branch with <span style="color:Crimson;">**git checkout lab1-handin**</span> then <span style="color:Crimson;">**git merge \<branch_name\>**</span>.

Double check that your submission resides in the correct branch <span style="color:Crimson;">**lab1-handin**</span> (note the middle part is a dash -, not underscore) by the deadline. Using a different branch name will result in failure to collect and grade your submission in time.

If you decide to use the late hour tokens, fill out this [form](https://docs.google.com/forms/d/e/1FAIpQLSci8AO4w-q_S-yxs4dTtt2ulYwnJl2aHqHc8tVWlJsfCuZAGA/viewform?usp=header) before the deadline, so that we won't be collecting and grading your solution immediately. When you finish (within the token limit), fill out this [form](https://docs.google.com/forms/d/e/1FAIpQLSfEN3MpgJ0FnWwg12-JK73pmQThRSpsGvV8wfmZTXl78vSIPg/viewform?usp=header) to indicate you are done. <span style="color:Crimson;">**Don't forget to fill out the form for the second time to avoid leaking your late tokens.**</span>


# Project 1 Requirement 

## Suggested Order of Implementation

We suggest first implementing the following, which can happen in parallel:

* Optimized `timer_sleep()` in Exercise 1.1.
* Basic support for priority scheduling in Exercise 2.1 when a new thread is created in thread_create().
* Implement fixed-point real arithmetic routines ([Fixed-Point Real Arithmetic](../reference/4.4bsd-scheduler.md#6-fixed-point-real-arithmetic)) that are needed for MLFQS in exercise 3.1.

Then you can add full support for priority scheduling in Exercise 2.1 by considering all other possible scenarios and the synchronization primitives.

Then you can tackle either Exercise 3.1 first or Exercise 2.2 first.


## Task 0: Design Document

* Download the [project 1 design document template](./threads.tmpl.txt). Read through it to motivate your design and fill it in after you finish the project.
* We recommend that you read the design document template before you start working on the project.
* See section [Project Documentation](../reference/documentation.md), for a sample design document that goes along with a fictitious project.

## Task 1: Alarm Clock

### Exercise 1.1

<div class="notice--success" markdown="1">
<span style="color:green;">**Exercise 1.1**</span>

<span style="color:green;">Reimplement</span> <span style="color:green;">**`timer_sleep()`**</span><span style="color:green;">, defined in</span> <span style="color:green;">`devices/timer.c`</span><span style="color:green;">.</span>

* <span style="color:green;">Although a working implementation is provided, it "busy waits," that is, it spins in a loop checking the current time and calling</span> <span style="color:green;">`thread_yield()`</span> <span style="color:green;">until enough time has gone by.</span>
* <span style="color:green;">Reimplement it to</span> <span style="color:green;">**avoid busy waiting**</span><span style="color:green;">.</span>
</div>


* <span style="color:CornFlowerBlue;">**Function: void timer\_sleep (int64\_t ticks)**</span>
  * **Suspends execution of the calling thread until time has advanced by at least&#x20;**_**ticks**_ **timer ticks.**
  * Unless the system is otherwise idle, the thread need not wake up after exactly _ticks_. Just put it on the ready queue after they have waited for the right amount of time.
  * `timer_sleep()` is useful for threads that operate in real-time, e.g. for blinking the cursor once per second.
  * **The argument to `timer_sleep()` is expressed in&#x20;**_**timer ticks**_**, not in milliseconds or any another unit.** There are `TIMER_FREQ` timer ticks per second, where `TIMER_FREQ` is a macro defined in `devices/timer.h`. The default value is 100. We don't recommend changing this value, because any change is likely to cause many of the tests to fail.
  * Separate functions **`timer_msleep()`, `timer_usleep()`**, and **`timer_nsleep()`** do exist for sleeping a specific number of milliseconds, microseconds, or nanoseconds, respectively, but **these will call `timer_sleep()`** automatically when necessary. You do not need to modify them.

If your delays seem too short or too long, reread the explanation of the `-r` option to `pintos` (see section [Debugging versus Testing](../test.md#1-debugging-versus-testing)).

<div class="notice--info" markdown="1">
**Hint**

<span style="color:red;">**You may find**</span>**&#x20;`struct list`&#x20;**<span style="color:red;">**and the provided functions to be useful for this exercise.**</span>

* Read the comments in `lib/kernel/list.h` carefully, since this list design/usage is different from the typical linked list you are familiar with (actually, Linux kernel [uses a similar design](https://0xax.gitbooks.io/linux-insides/content/DataStructures/linux-datastructures-1.html)).
* Searching the Pintos codebase to see how `struct list` is used may also give you some inspiration.
</div>

<div class="notice--info" markdown="1">
**Hint**

* You may want to leverage some synchronization primitive that provides some sort of thread _"waiting"_ functionality, e.g., semaphore.
* **You do not have to wait for the Synchronization Lecture to be able to use these primitives.** Reading through section [Synchronization](../reference/synchronization.md) is sufficient.
* In addition, when modifying some global variable, e.g., a global list, you will need to use some synchronization primitive as well to ensure it is not modified or read concurrently (e.g., a timer interrupt occurs during the modification and we switch to run another thread).
</div>

<div class="notice--info" markdown="1">
**Hint**

**You need to decide where to check whether the elapsed time exceeded the sleep time.**

* The original `timer_sleep` implementation calls `timer_ticks`, which returns **the current `ticks`**.
* **Check where the static `ticks` variable is \_updated**\_**.** You can search with `grep` or `rg` to help you find this out (see [Development Tools](../reference/developement_tool.md) for more details).
</div>

<span style="color:red;">**The alarm clock implementation is**</span><span style="color:red;">**&#x20;**</span>_<span style="color:red;">**not**</span>_<span style="color:red;">**&#x20;**</span><span style="color:red;">**needed for later projects**</span>, although it could be useful for project 4.

## Task 2: Priority Scheduling

### Exercise 2.1

<div class="notice--success" markdown="1">
<span style="color:green;">**Exercise 2.1**</span>

<span style="color:green;">**Implement**</span><span style="color:green;">**&#x20;**</span>_<span style="color:green;">**priority scheduling**</span>_<span style="color:green;">**&#x20;**</span><span style="color:green;">**in Pintos.**</span>

* <span style="color:green;">When a thread is added to the ready list that has a higher priority than the currently running thread, the current thread should</span> <span style="color:green;">**immediately yield**</span> <span style="color:green;">the processor to the new thread.</span>
* <span style="color:green;">Similarly, when threads are waiting for a lock, semaphore, or condition variable,</span> <span style="color:green;">**the highest priority waiting thread should be awakened first**</span><span style="color:green;">.</span>
* <span style="color:green;">A thread may raise or lower its own priority at any time, but</span> <span style="color:green;">**lowering its priority such that it no longer has the highest priority must cause it to immediately yield the CPU**</span><span style="color:green;">.</span>
</div>

* **Thread priorities range from `PRI_MIN` (0) to `PRI_MAX` (63).** Lower numbers correspond to lower priorities, so that priority 0 is the lowest priority and priority 63 is the highest.
* **The initial thread priority is passed as an argument to `thread_create()`**. If there's no reason to choose another priority, use **`PRI_DEFAULT` (31)**.
* The `PRI_` macros are defined in `threads/thread.h`, and you should not change their values.

<div class="notice--info" markdown="1">
**Hint**

For this exercise, **you need to consider&#x20;**_**all the scenarios**_ **where the priority must be enforced.**

* For example, **when an alarm clock for a thread fires off**, that thread should be made ready again, which entails a priority check.
* You can find some of these scenarios by **looking for places that modify `ready_list`** (directly and indirectly, rg can be helpful).
</div>

<div class="notice--info" markdown="1">
**Hint**

* **To yield the CPU,** you can check the thread APIs in **`threads/thread.h`**.
  * Read the comment and implementation of the corresponding thread function in `threads/thread.c`.
  * **That function may not be used in interrupt context** (i.e., should not call it inside an interrupt handler).
* **To yield the CPU** _<span style="color:red;">**in the interrupt context**</span>_, you can take a look at functions in **`threads/interrupt.c`**.
</div>

### Exercise 2.2.1

**One issue with priority scheduling is "priority inversion".**

* Consider high, medium, and low priority threads H, M, and L, respectively.
* If H needs to wait for L (for instance, for a lock held by L), and M is on the ready list, then H will never get the CPU because the low priority thread will not get any CPU time.
* **A partial fix for this problem is for H to "donate" its priority to L** while L is holding the lock, then **recall the donation** once L releases (and thus H acquires) the lock.

<div class="notice--success" markdown="1">
<span style="color:green;">**Exercise 2.2.1**</span>

<span style="color:green;">**Implement priority donation.**</span>

* <span style="color:green;">You will need to</span> <span style="color:green;">**account for all different situations in which priority donation is required**</span><span style="color:green;">.</span>
* <span style="color:green;">You must implement priority donation</span> <span style="color:green;">**for locks**</span><span style="color:green;">. You need</span> <span style="color:green;">**not**</span> <span style="color:green;">implement priority donation for the other Pintos synchronization constructs.</span>
* <span style="color:green;">You do need to</span> <span style="color:green;">**implement**</span> _<span style="color:green;">**priority scheduling**</span>_ <span style="color:green;">**in all cases**</span><span style="color:green;">.</span>
* <span style="color:green;">Be sure to</span> <span style="color:green;">**handle multiple donations**</span><span style="color:green;">, in which multiple priorities are donated to a single thread</span>.
</div>

### Exercise 2.2.2

<div class="notice--success" markdown="1">
<span style="color:green;">**Exercise 2.2.2**</span>

<span style="color:green;">**Support nested priority donation:**</span>

* <span style="color:green;">if H is waiting on a lock that M holds and M is waiting on a lock that L holds, then both M and L should be boosted to H's priority.</span>
* <span style="color:green;">If necessary, you may impose</span> <span style="color:green;">**a reasonable limit**</span> <span style="color:green;">on depth of nested priority donation, such as 8 levels.</span>
</div>

**Note:** if you support nested priority donation, you need to pass the `priority-donate-nest` and `priority-donate-chain` tests.

### Exercise 2.3

<div class="notice--success" markdown="1">
<span style="color:green;">**Exercise 2.3**</span>

<span style="color:green;">**Implement the following functions that allow a thread to examine and modify its own priority.**</span>

<span style="color:green;">Skeletons for these functions are provided in</span> <span style="color:green;">**threads/thread.c**</span><span style="color:green;">.</span>
</div>

* <span style="color:CornFlowerBlue;">**Function: void thread\_set\_priority (int new\_priority)**</span>
  * **Sets the current thread's priority to&#x20;**_**new\_priority**_**.**
  * If the current thread no longer has the highest priority, **yields**.
* <span style="color:CornFlowerBlue;">**Function: int thread\_get\_priority (void)**</span>
  * **Returns the current thread's priority.** In the presence of priority donation, returns the higher (donated) priority.

You need not provide any interface to allow a thread to directly modify other threads' priorities.

<span style="color:red;">**The priority scheduler is not used in any later project.**</span>

## Task3: Advanced Scheduler

### Exercise 3.1

<div class="notice--success" markdown="1">
<span style="color:green;">**Exercise 3.1**</span>

<span style="color:green;">**Implement a multilevel feedback queue scheduler**</span> <span style="color:green;">similar to the 4.4BSD scheduler to reduce the average response time for running jobs on your system.</span>

<span style="color:green;">See section</span> [<span style="color:green;">**4.4BSD Scheduler**</span>](../reference/4.4bsd-scheduler.md)<span style="color:green;">, for detailed requirements</span>.
</div>

Like the priority scheduler, the advanced scheduler chooses the thread to run based on priorities. However, **the advanced scheduler does not do priority donation**.

* Thus, we recommend that you have the priority scheduler working, except possibly for priority donation, before you start work on the advanced scheduler.

**You must write your code to allow us to choose a scheduling algorithm policy at Pintos startup time.**

* **By default, the priority scheduler must be active, but we must be able to choose the 4.4BSD scheduler with the `-mlfqs` kernel option.** Passing this option sets **`thread_mlfqs`**, declared in `threads/thread.h`, to true when the options are parsed by `parse_options()`, which happens early in `pintos_init()`.

When the 4.4BSD scheduler is enabled, threads no longer directly control their own priorities.

* **The priority argument to `thread_create()` should be ignored, as well as any calls to `thread_set_priority()`**,
* and **`thread_get_priority()` should return the thread's current priority as set by the scheduler**.

<div class="notice--info" markdown="1">
**Hint**

**Double check the implementations of your fixed-point arithmetic routines** (and ideally have some unit test for them).

Some simple mistake in these routines could result in mysterious issues in your scheduler.
</div>

<div class="notice--info" markdown="1">
**Hint**

<span style="color:red;">**Efficiency matters a lot for the MLFQS exercise.**</span>

* An inefficient implementation can distort the system.
* Read the comment in the test case **`mlfqs-load-avg.c`**.
* In fact, the inefficiency in your alarm clock implementation can also influence your MLFQS behavior.
* So double-check if your implementation there can be optimized.
</div>

<span style="color:red;">**The advanced scheduler is not used in any later project.**</span>

