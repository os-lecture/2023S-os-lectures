---
marp: true
theme: default
paginate: true
_paginate: false
header: ''
footer: ''
backgroundColor: white
---

<!-- theme: gaia -->
<!-- _class: lead -->

# Lecture 8 Multiprocessor Scheduling

## Section 3 Linux O(1) Scheduling

<br>
<br>

Xiang Yong Chen Yu Li Guoliang

Fall 2022

---

**Outline**

### 1. SMP and early Linux kernels
2. Linux O(n) scheduler
3. Linux O(1) scheduler

---

#### [Evolution] of Linux scheduler (https://www.eet-china.com/mp/a111242.html)

* O(n) scheduler: kernel version 2.4-2.6
* O(1) scheduler: kernel version 2.6.0-2.6.22
* CFS scheduler: kernel version 2.6.23-present

![w:550](figs/linux-schedulers.png)

<!--
https://www.scaler.com/topics/operating-system/process-scheduling/
-->

---
<style scoped>
{
  font-size: 33px
}
</style>

#### Key issues that the scheduler needs to consider

- What kind of **data structure** is used to organize the process
- How to determine **process running time** according to process priority
- How to judge **process type** (I/O intensive, CPU intensive, real-time, non-real-time)
- How to determine the dynamic **priority** of a process: Influencing factors
   - Static priority, nice value
   - Priority rewards and punishments for I/O-intensive and CPU-intensive
- How to **adapt to multiprocessor** situations

---
#### SMP and Early Linux Kernels

<!-- https://courses.engr.illinois.edu/cs423/sp2018/slides/13-linux-schedulers.pdf
Linux History -->

- Linux 1.2
   - Ring queue + Round Robin scheduling strategy
- Linux 2.0
   - SMP support consists of a "big lock" that serializes kernel access
   - Parallelism is supported in user mode, the Linux kernel itself cannot take advantage of multiprocessor acceleration
- Linux 2.2
   - Introduce scheduling class (real-time, non-real-time)
<!-- ![w:600](figs/sqms.png) -->



<!-- Introduction to scheduler, and Linux scheduling strategy https://www.cnblogs.com/vamei/p/9364382.html -->


<!-- 10,000-character long text, hammer it! Demystifying the Linux Process Scheduler https://www.eet-china.com/mp/a111242.html -->
---

**Outline**

1. SMP and early Linux kernels
### 2. Linux O(n) scheduler
3. Linux O(1) scheduler

---

#### Linux 2.4 kernel: Linux $O(n)$ scheduler

![w:700](figs/linux-o-n-sched.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Linux $O(n)$ Scheduler
- Using multiple processors can speed up the processing speed of the kernel, and the scheduler has a complexity of $O(n)$
   - The name $O(n)$ comes from the big $O$ notation for algorithmic complexity
   - The letter $n$ here represents the number of active processes in the operating system
   - $O(n)$ means that the time complexity of this scheduler is proportional to the number of active processes

![bg right:35% 90%](figs/linux-o-n-sched.png)

---
#### The idea of Linux $O(n)$ scheduling algorithm

- Divide time into a large number of tiny time slices (Epoch)
- at the beginning of each time slice
   - Calculate dynamic priority of processes
   - Map the static priority of the process to the default time slice
   - Then choose the process with the highest priority to execute
- After the process is switched to execute by the scheduler, it can use up this time slice without being disturbed
- If the process has not exhausted the time slice, the remaining time is added to the next time slice of the process


<!-- Talk about scheduling - Linux O(1) https://cloud.tencent.com/developer/article/1077507
Introduction to Linux Kernel Scheduling Mechanism
https://loda.hala01.com/2017/06/linux-kernel.html
-->
---
#### $O(n)$ Scheduling Algorithm Complexity
The complexity of the O(n) scheduling algorithm is $O(n)$
- **Check the priority of all ready processes** before using the time slice every time
- **check time** is proportional to the number of processes $n$ in the process

---
#### Linux O(n) scheduler data structure
- Use only one global runqueue to place ready tasks
- Each core needs to compete for tasks in the same runqueue

![bg right:52% 90%](figs/one-task-queue.png)

---

#### Disadvantages of the Linux $O(n)$ scheduler

- **Execution overhead** of $O(n)$
    - The performance of this scheduler will be greatly reduced when there are a large number of processes running
- Multiprocessor **competing access** tasks in the same runqueue
    - $O(n)$ scheduler does not have good scalability (scalability)

![bg right:40% 100%](figs/one-task-queue.png)

<!-- ---
#### Linux 2.4 kernel: SMP implemented in kernel mode
- The use of multiple processors can speed up the processing speed of the kernel, and the scheduler has a complexity of O(n)
   - The kernel scheduler maintains two queues: runqueue and expired queue
   - Both queues are always in order
   - When a process runs out of time slice, it will be inserted into the expired queue
   - When the runqueue is empty, swap the runqueue with the expired queue
![w:800](figs/linux-2.4-sched.png)


---
#### Linux 2.4 kernel: SMP implemented in kernel mode
- The use of multiple processors can speed up the processing speed of the kernel, and the scheduler has a complexity of O(n)
   - Globally shared ready queue
   - Find the next executable process, this operation is usually O(1)
   - Every time a process runs out of time slices, find a suitable location to perform an insertion operation, and it will traverse all tasks with a complexity of O(n)
![w:800](figs/linux-2.4-sched.png)


---
#### Linux 2.4 kernel: SMP implemented in kernel mode
- The use of multiple processors can speed up the processing speed of the kernel, and the scheduler has a complexity of O(n)
   - Modern operating systems are capable of running thousands of processes
   - The O(n) algorithm means that every time you schedule, for the currently executed process, you need to go through all the processes in the expired queue and find a suitable place to insert
   - This will not only bring a huge loss in performance, but also make the scheduling time of the system very uncertain -- depending on the load of the system, there may be several times or even hundreds of times of difference
   ![w:800](figs/linux-2.4-sched.png) -->



<!-- 10,000-character long text, hammer it! Demystifying the Linux Process Scheduler https://www.eet-china.com/mp/a111242.html -->
---

**Outline**

1. SMP and early Linux kernels
2. Linux O(n) scheduler
### 3. Linux O(1) scheduler

---

#### Linux O(1) scheduler
The scheduler for Linux 2.6 was designed and implemented by Ingo Molnar.
- Build a fully O(1) scheduler for wakeup, context switch and timer interrupt overhead
![bg right:52% 90%](figs/linux-o-1-sched.png)

---

#### The idea of Linux O(1) scheduler

- Implemented per-cpu-runqueue, each CPU has a ready process task queue
- Adopt global priority
   - Real-time progress 0-99
   - Common process 100-139


![bg right:45% 90%](figs/linux-o-1-sched.png)


---

#### The idea of Linux O(1) scheduler

- Active array active: put ready process
- Expiration array expire: place expired process
![bg right:62% 90%](figs/linux-o-1-queues.png)
---

#### The idea of Linux O(1) scheduler

- Each priority corresponds to a linked list
- Introduce a bitmap array to record the active processes in 140 linked lists
![bg right:55% 90%](figs/O1.jpeg)


  
---
<style scoped>
{
  font-size: 30px
}
</style>

#### Time complexity of common data structure access
- A data structure that satisfies O(1)?
- Four basic operations and time complexity of commonly used data structures
   - Access: **random access**
     - Array: average and worst case O(1)
     - Linked list is O(N)
     - Tree is generally O(log N)
![bg right:50% 100%](figs/O1.jpeg)

  
---
<style scoped>
{
  font-size: 30px
}
</style>

#### Search operations for commonly used data structures
- Search: search
   - hash table time complexity is O(1), but its worst case is O(N)
   - Most trees (b-tree / red-black tree) are O(log N) on average and worst case
![bg right:50% 100%](figs/O1.jpeg)

  
---
#### Insertion and deletion operations of commonly used data structures
- Insert/deletion: insert and delete
   - hash table time complexity is O(1), but its worst case is O(N)
   - linked list, stack, queue are O(1) in average and worst case

![bg right:43% 100%](figs/O1.jpeg)

    
---
<style scoped>
{
  font-size: 30px
}
</style>

#### Time complexity of Linux O(1) scheduler

- There are 140 priorities for a process, and an array with a length of 140 can be used to record the priority.
   - access is $O(1)$
- Bitmap bitarray assigning a bit to each priority
   - If there is a process under this priority queue, then color the corresponding bit and set it to 1, otherwise set it to 0.
   - The problem is simplified to find the bit (left-most bit) whose highest bit in the bitmap is 1, which can be implemented with one CPU instruction.

![bg right:30% 100%](figs/O1.jpeg)
    
---

#### Time complexity of Linux O(1) scheduler

- Each priority level uses a FIFO queue to manage the processes under this priority level.
   - The new arrival is inserted at the end of the queue, first in first out, insert/deletion is $O(1)$

![bg right:43% 100%](figs/O1.jpeg)
    
---

#### Linux $O(1)$ active array and expired array

<!-- How does Linux schedule processes? https://jishuin.proginn.com/p/763bfbd2df25 -->

Active Priority Array (APA) Expired Priority Array (EPA)

- Find the position x of the left-most bit in the active bitarray;
- Find the corresponding queue APA[x] in APA;
- Take a process from queue APA[x];


![bg right:40% 100%](figs/O1.jpeg)

    
---
<style scoped>
{
  font-size: 32px
}
</style>

#### Linux $O(1)$ active array and expired array

- For the currently executed process, recalculate its priority, and then put it into the corresponding queue EPA[priority] of EPA;
- If the bit corresponding to the process priority in the expired bitarray is 0, set it to 1;
- If active bitarray is all zeros, swap active bitarray and expired bitarray;

![bg right:40% 100%](figs/O1.jpeg)

    
---
#### Multicore/SMP support for Linux O(1) scheduler
- Analyze each CPU load at a certain time interval
   - Calculate CPU load after each clock interrupt
   - by lightly loaded CPU pulling processes instead of pushing processes
![bg right:52% 100%](figs/O1.jpeg)

---

### Summary

1. SMP and early Linux kernels
2. Linux O(n) scheduler
3. Linux O(1) scheduler

---

### references
- http://www.wowotech.net/process_management/scheduler-history.html
- https://courses.engr.illinois.edu/cs423/sp2018/slides/13-linux-schedulers.pdf
- https://www.cnblogs.com/vamei/p/9364382.html
- https://cloud.tencent.com/developer/article/1077507?from=article.detail.1603917
- https://www.eet-china.com/mp/a111242.html
- https://loda.hala01.com/2017/06/linux-kernel.html
- https://jishuin.proginn.com/p/763bfbd2df25