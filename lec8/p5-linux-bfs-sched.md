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

## Section 5 Linux/FreeBSD BFS Scheduling

-- From [Analysis of the BFS Scheduler in FreeBSD](http://vellvisher.github.io/papers_reports/doc/BFS_FreeBSD.pdf)

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023

---

**Outline**

### 1. BFS scheduler
2. Performance comparison of BFS and CFS (2012)

---

#### The idea of BFS
Full name of BFS: Brain Fuck Scheduler
- BFS is a variant of the time slice round-robin algorithm.
- A single ready queue (double linked list) is used in multiprocessor systems.
   - Increases the overhead of mutex access to the queue.
   - Reduces the overhead of load balance algorithm

---
#### Process Priorities in BFS

- There are 103 priorities for processes:
   - 100 static real-time priorities;
   - 3 normal priorities
       - SCHED_ISO (isochronous) : interactive tasks
       - SCHED_NORMAL : normal tasks
       - SCHED_IDLEPRIO : low-priority tasks


---
#### Ready Queue in BFS

- Ready queue
   - All CPUs share a **single ready queue** with a double linked list structure;
   - All processes are queued by priority;
   - Each process of the same priority has a time slice length and a virtual deadline;

---
<style scoped>
{
  font-size: 30px
}
</style>
#### BFS time slice
- Time Slice size: specified by the algorithm parameter and can be chosen between 1ms to 1000ms, with a default of 6ms.
- Virtual Deadline : a sort of the longest time a process has been waiting for the CPU in the ready queue, not an actual deadline.
   - When the **time slice is used up**, the virtual deadline is recalculated.
   - When the **event waiting is over**, the virtual deadline remains unchanged to preempt ready processes of the same priority.
   - To run the process on the **last run CPU** (affinity), different CPUs add a weight to the virtual deadline of the process.


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Virtual Deadline Calculation in BFS
- Calculated based on current time, process priority and time slice settings;
```
offset = niffies + (prioratio ∗ rr_interval)
Priority increases by 10% for every nice level
```
- Niffies is the current time; prio_ratios[priority] is a constant arraycorresponding to different priorities; rr_interval is timeslice, the time slice allocated to each task by the CPU, a constant.

- Virtual deadline calculation results: https://wikimili.com/en/Brain_Fuck_Scheduler


---
#### Scheduling Ideas in BFS
Using the **bitmap** concept in the O(1) scheduler, all processes are arranged in 103 queues, not by priority but by priority interval, with each interval having a queue.
<!-- https://www.cnblogs.com/dragonsuc/p/7144265.html -->
![bg right 100%](figs/bfs.png)


---
#### Scheduling Ideas in BFS
First, check the queue that is not 0 in the bitmap using the O(1) scheduler method, and then execute O(n) search in that queue to find the process with the smallest virtual deadline for execution.

![bg right 100%](figs/bfs.png)

---

#### Ready Queue Insertion in BFS

- Time slice used up: After resetting the virtual deadline, insert the process into the ready queue.
- Waiting for events: Keep the virtual deadline unchanged and preempt low-priority processes or insert into the ready queue.

---

**Outline**

1. BFS scheduler
### 2. Performance comparison of BFS and CFS (2012)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### [Performance Comparison](http://repo-ck.com/bench/cpu_schedulers_compared.pdf) of BFS and CFS  (2012)
Test Benchmark
- GCC **compilation** of Linux kernel v3.6.2.2
- lrzip **compression** of the Linux kernel v3.6.2 kernel source tree
- MPEG2 video ffmpeg **compression** from 720p to 360p 
![bg right:53% 90%](figs/test-machines.png)

---
#### Performance Comparison between BFS and CFS: Compression Test
![w:1000](figs/compression-test.png)

---
#### Performance Comparison between BFS and CFS: Compilation Test
![w:1000](figs/make-test.png)

---

#### Performance Comparison between BFS and CFS: Video Encoding Test
![w:1000](figs/video-test.png)

---

### References
- http://repo-ck.com/bench/cpu_schedulers_compared.pdf
- https://zhuanlan.zhihu.com/p/351876567
- https://blog.csdn.net/dog250/article/details/7459533
- https://www.cnblogs.com/dragonsuc/p/7144265.html
