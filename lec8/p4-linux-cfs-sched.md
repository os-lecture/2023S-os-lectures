---
noteId: "db85f801d86511edb7d82f06d610f713"
tags: []
marp: true
theme: "default"
paginate: true
_paginate: false
header: ""
footer: ""
backgroundColor: "white"

---

<!-- theme: gaia -->
<!-- _class: lead -->

# Lecture 8 Multiprocessor Scheduling

## Section 4 Linux CFS Scheduling
Completely Fair Scheduler (CFS, Completely Fair Scheduler)

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023

---

**Outline**

### 1. Principle of CFS
2. Implementation of CFS

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Background of  CFS   
<!-- 10,000-character long text, hammer it! Demystifying the Linux Process Scheduler https://www.eet-china.com/mp/a111242.html -->
- Both O(1) and O(n) divide CPU resources into time slices
     - Using a fixed quota allocation mechanism, the available time slice of each scheduling cycle is determined
     - reassigned at the end of the scheduling cycle 
- The O(1) scheduler is essentially based on the MLFQ algorithm.
     - drawback: not very responsive to process interaction
- Requirements
     - determine whether a process is CPU-intensive or IO-intensive based on its running status, and then apply priority rewards and punishments
     - There are errors in this speculation itself, the more complex the scene, the more difficult it is to judge


---
####  Background of  CFS  

CFS scheduling algorithm was proposed and implemented by Hungarian Ingo Molnar
- He is also the proposer of the O(1) scheduling algorithm

![bg right:40% 100%](figs/ingo-molnar.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### The Idea of CFS
<!-- - CFS does not calculate priority, but determines who will schedule by calculating the CPU time consumed by the process (normalized virtual CPU time). So as to achieve the so-called fairness. -->
- Abandon fixed time slice allocation, adopt **dynamic time slice allocation**
- Each process's allotted time in each scheduling cycle is related to the total number of processes, total CPU time, process weight, etc., and the value of each scheduling cycle may be different
- Each process has a **nice** value, indicating its static priority

![bg right 90%](figs/prio-to-weight.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### The Idea of CFS
- **Treat the CPU as a resource**, and record the use of this resource by each process
     - When scheduling, the scheduler always chooses the process that consumes the least resources to run (**fair distribution**)
- Since the work of some processes is more important than others, this absolute fairness is sometimes an unfair one
     - **Allocate CPU resources according to weight**
![bg right:47% 90%](figs/prio-to-weight.png)

---
#### Dynamic Allocation of Process Running Time in CFS

- Allocate running time according to the priority weight of each process
     - The greater the weight of the process, the more running time it gets
`Running time allocated to a process = scheduling cycle * process weight / sum of all process weights`
- Scheduling cycle
     - The time to schedule all processes in the TASK_RUNNING state

<!-- ![bg right:40% 100%](figs/prio-to-weight.png) -->


---
<!-- CFS (Completely Fair Scheduler) https://www.jianshu.com/p/1da5cfd5cee4 -->
#### Relative Fairness of CFS
- There are two processes A and B in the system, the weights are 1 and 2 respectively, and the scheduling period is set to 30ms,
- CPU time for A is: 30ms * (1/(1+2)) = 10ms
- CPU time for B is: 30ms * (2/(1+2)) = 20ms
- Out of these 30ms A will run for 10ms and B will run for 20ms

Their runtimes are not the same. How is fairness reflected?

<!-- ![bg right 100%](figs/rbtree.png) -->



---

#### Virtual Time of CFS

* Virtual runtime (vruntime): records the time the process has been running
     * vruntime increases or decreases the running time according to the weight of the process.
`vruntime = actual running time * 1024 / process weight`
     * 1024 is the weight of the process whose nice is 0, the code is NICE_0_LOAD
     * All processes use the weight 1024 of the process with nice as 0 as a benchmark to calculate their own vruntime increase speed

---

#### Virtual Time of CFS

Taking the above two processes A and B as an example, the weight of B is twice that of A, so the vruntime increase rate of B is only half of that of A.
```
vruntime = (scheduling period * process weight / total weight of all processes) * 1024 / process weight
          = scheduling period * 1024 / total weight of all processes
```
Although the weights of the processes are different, their vruntime growth rates should be the same, regardless of the weight.

<!-- O(n), O(1) and CFS schedulers http://www.wowotech.net/process_management/scheduler-history.html

Virtual runtime = (physical runtime) X (weight of nice value 0) / weight of the process

Through the above formula, we construct a virtual world. The two-dimensional (load weight, physical runtime) physical world becomes a one-dimensional virtual runtime virtual world. In this virtual world, the vruntime of each process can be compared to determine its position in the red-black tree, and the fairness of the CFS scheduler is to maintain the fairness of the vruntime in the virtual world, that is, the vruntime of each process is equal.

-->

---
#### CFS Virtual Time Calculation

From a macro perspective, the vruntime growth rate of all processes should be pushed forward simultaneously, so that this vruntime can be used to select the running process.
* If the vruntime value of a process is small, it means that it has used the CPU for a shorter period of time and has been treated unfairly, so the next running process will be the one with the smaller vruntime value.
* This can not only select processes fairly, but also ensure that high-priority processes get more running time.





---
#### Example of CFS Virtual Time Calculation 

CFS makes the vruntime of each scheduling entity (process or process group) catch up with each other, and the vruntime of each scheduling entity increases at different speeds. The greater the weight, the slower the increase, so that more CPU execution time can be obtained.

      A has 6 time slices per cycle, B has 3 time slices per cycle, and C has 2 time slices per cycle
      vruntime:
      A: 0 6 6 6 6 6 6 12 12 12 12 12 12
      B: 0 0 3 3 3 6 6 6 9 9 9 12 12
      C: 0 0 0 2 4 4 6 6 6 8 10 10 12
      Scheduling: A B C C B C A B C C B C


<!-- ![bg right 100%](figs/rbtree.png) -->


---

**Outline**

1. Principle of CFS
### 2. Implementation of CFS

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Red-Black Tree: Process Vruntime Data Structure in CFS
- Linux uses a red-black tree to record the vruntime of each process
     - In a multi-core system, each core has a red-black tree.
     - When scheduling, select the process with the smallest vruntime from the red-black tree to run
![bg right:53% 90%](figs/rbtree.png)

---

#### Process Weight for CFS
- The weight is determined by the nice value,and each process's weight corresponds to its nice value
     - The larger the nice value, the lower the weight
- Convert by global array prio_to_weight
![bg right:48% 90%](figs/prio-to-weight.png)


---
<style scoped>
{
  font-size: 32px
}
</style>
#### How to set the vruntime of the newly created process in CFS?

-If the initial value of vruntime of the new process is 0, which is much smaller than the value of the old process,it will maintain the advantage of preempting the CPU for a long time, and the old process will starve , which is obviously unfair.
![bg right:46% 90%](figs/rbtree.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Vruntime settings for newly created processes in CFS

- Each CPU's queue **cfs_rq** maintains a **min_vruntime field**
     - Record the minimum value of vruntime for all processes in the queue
- The new process's initial vruntime value is set to the min_vruntime of the  queue 
     - maintaining a reasonable difference from the vruntime values of existing processes.
![bg right:41% 90%](figs/rbtree.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Does the vruntime of a sleeping process in CFS stay the same all the time?
If the vruntime of a sleeping process remains the same while the vruntime of other running processes keeps advancing, then when the sleeping process is finally awakened, its vruntime will be much smaller than others, which will give it a long-term advantage in preempting the CPU, and other processes will starve.

![bg right:47% 100%](figs/rbtree.png)


---

#### Vruntime of sleeping process in CFS

- Reset the vruntime value when the sleeping process is woken up. based on the min_vruntime value, give some compensation, but not too much compensation.
![bg right:45% 90%](figs/rbtree.png)


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Will a sleeping process in CFS preempt the CPU immediately when it wakes up?
- It is highly probable for a sleeping process to **preempt** the CPU when it wakes up, which is the main purpose of the Completely Fair Scheduler (CFS) algorithm. It aims to ensure the response time of interactive processes, as **interactive processes** frequently sleep while waiting for user input.

![bg right:45% 100%](figs/rbtree.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Will a dormant process in CFS seize the CPU immediately when it wakes up?

- **Processes that are actively sleeping** will also be compensated when they wake up. Such processes often do not require fast response. They will also wake up and preempt each time, which may cause other more important application processes Being preempted, detrimental to overall performance.
- The **WAKEUP_PREEMPT** bit of sched_features presents that the wakeup preemption feature is disabled. The process that has just been woken up **does not immediately preempt** the running process, but waits until the running process runs out of time slices

![bg right:30% 100%](figs/rbtree.png)


---

#### Will the vruntime change when the process in CFS migrates between CPUs?
- On a multi-CPU system,  the workload of different CPUs varies, with some CPUs being busier than others. Each CPU has its own running queue, and the vruntime of processes in each queue progresses at different rates. The min_vruntime value of each CPU's running queue also differs.
![bg right:40% 90%](figs/smp-min-vruntime.png)



---

#### Process Migration in CFS

- When a process **comes out** of a CPU's running queue, its vruntime is **subtracted** from the min_vruntime value of the queue;
- When a process **joins** another CPU's running queue, its vruntime is **added** to the queue's min_vruntime value.

![bg right:45% 90%](figs/smp-min-vruntime.png)

---
#### Vruntime Overflow Problem in CFS 
- vruntime type: usigned long
- The virtual time of the process is an increasing positive value, so it will not be negative, but it has its upper limit, which is the maximum value that can be represented by unsigned long
- If it overflows, then it will roll back from 0, if so, what will happen?

![bg right:40% 100%](figs/rbtree.png)

---

#### CFS vruntime overflow example
```C
unsigned char a = 251;
unsigned char b = 254;
b += 5;
//b overflows, causing a > b, should b = a + 8
//How to achieve the real result? Change to the following:
unsigned char a = 251;
unsigned char b = 254;
b += 5;
signed char c = a - 250,
signed char d = b - 250;
//Here to judge the size of c and d
```
![bg right:54% 90%](figs/rbtree.png)

---

#### [Modularization] of the Linux scheduler (http://www.wowotech.net/process_management/scheduler-history.html)

![w:650](figs/Linux-scheduler-module.png)

---
### References
- https://www.eet-china.com/mp/a111242.html
- https://www.jianshu.com/p/1da5cfd5cee4
- https://developer.ibm.com/tutorials/l-completely-fair-scheduler/
- http://www.wowotech.net/process_management/scheduler-history.html

---
<style scoped>
{
  font-size: 32px
}
</style>
### Course experiment three process and process management

* Chapter 5: Process and process management -> chapter5 exercises ->
     * [rCore](https://learningos.github.io/rCore-Tutorial-Guide-2022A/chapter5/4exercise.html)
     * [uCore](https://learningos.github.io/uCore-Tutorial-Guide-2022A/chapter5/4exercise.html)
* Experimental tasks
     * spawn system call
     * stride scheduling algorithm
* Experiment submission requirements
     * May 7, 2023;
