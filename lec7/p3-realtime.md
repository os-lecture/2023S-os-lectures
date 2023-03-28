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

# Lecture 7 Process Management and Single Processor Scheduling
### Section 3 Real-time Scheduling

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023

---

**Outline**

### 1. Real-time OS
- Definition of real-time OS
- Real-time tasks
2. Real-time Scheduling
3. Priority Inversion

---
#### Definition of real-time OS
- Definition of real-time operating system
   - An operating system that depends on both time and functionality for correctness.
- Performance metrics for real-time OS
    - Timeliness of time-constrained tasks (deadlines)
    - Speed and average performance are relatively unimportant
- Characteristics of real-time OS
   - Predictability of time-constrained tasks


---
#### Classification of Real-Time OS 
- Strong/hard real-time OS
    - Requires critical tasks to be completed within a specified time.
- Weak/soft real-time OS
   - Processes with high priority should be completed as soon as possible, but not necessarily required.


---
#### Real-time tasks
- Task (Work Unit)
   - A single computation, file read, message transfer, etc.
- Task properties
   - Resources required to complete the task.
   - Timing parameters

![w:1000](figs/rt-task.png)

---
#### Periodic real-time tasks
- Periodic real-time tasks: a series of similar tasks
   - Tasks are repeated regularly.
   - Period p = task request interval (0 <p)
   - Execution time e = maximum execution time (0 < e < p)
   - Utilization U = e/p
![w:900](figs/rt-task-2.png)
- Schedulable: if $\sum_{p_i} \frac{e_i}{p_i}\leq 1$; otherwise non-schedulable

---
#### Soft Deadline and Hard Deadline
- Hard deadline
   - Missing the task deadline will result in catastrophic and very serious consequences.
   - It must be verified that the deadline can be met in the worst-case scenario.
- Soft deadline
   - The task deadline is usually met.
   - If it cannot be met, the requirements will be lowered.
   - Do your best to meet the deadline.

---
#### Schedulability
<style scoped>
{
  font-size: 29px
}
</style>
- Schedulability refers to the ability of a real-time operating system to meet task deadlines.
    - The execution order of real-time tasks needs to be determined.
    - **Static** priority scheduling: The priority of a task does not change during execution.
    - **Dynamic** priority scheduling: The priority of a task can change during execution.
![w:750](figs/rt-task-3.png)


---

**Outline**

1. Real-time OS
### 2. Real-time scheduling
- Rate Monotonic(RM) Algorithm
- Earliest Deadline First(EDF) Algorithm
- Least Laxity First(LLF) Algorithm
3. Priority Inversion

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Real-time scheduling
- **Static** Priority Scheduling: Rate Monotonic(RM) Algorithm
   - Priorities are assigned based on the task periods
   - The shorter the period, the higher the priority
   - Execute the task with the shortest period

- **Dynamic** Priority Scheduling: Earliest Deadline First(EDF) Algorithm
   - The task with the earliest deadline has the highest priority
   - The task closest to the deadline is executed first

If there is **shared resource occupation** between tasks, tasks with high priority may be delayed!

---
#### Rate Monotonic(RM) Algorithm

- Determine the task priority based on task periods (the shorter the period, the higher the priority, preemptive)

- Process P1: e=20 p=50
- Process P2: e=35 p=100

![w:900](figs/ddsl1.gif)


---
#### Rate Monotonic(RM) Algorithm

- Determine the task priority based on task periods (the shorter the period, the higher the priority, preemptive)

- Process P1: e=25 p=50
- Process P2: e=35 p=80

![w:900](figs/ddsl2.gif)

---

#### Earliest Deadline First(EDF) Algorithm

- Issues of Fixed priority: some tasks may miss the deadline


- Process P1: e=10 p=20
- Process P2: e=25 p=50

![w:1000](figs/edf1.png)

---

#### Earliest Deadline First(EDF) Algorithm

- Issues of Fixed priority: some tasks may miss the deadline


- Process P1: e=10 p=20
- Process P2: e=25 p=50

![w:900](figs/edf2.png)


---

#### Earliest Deadline First(EDF) Algorithm

- Issues of Fixed priority: some tasks may miss the deadline


- Process P1: e=10 p=20
- Process P2: e=25 p=50

![w:850](figs/edf3.png)

---

#### Earliest Deadline First Algorithm (EDF, Earliest Deadline First)

- Priorities of tasks are dynamically assigned based on their deadlines. The closer the deadline, the higher the priority.
- Process P1: e=10 p=20
- Process P2: e=25 p=50

![w:800](figs/edf4.png)

---

#### Lowest Laxity First Algorithm (LLF)


- Determine task priority according to **task urgency or slack level**

- The higher the urgency, the higher the priority.

- Laxity = Deadline - Execution Time - Current Time

- Process P1: e=10 p=20; Process P2: e=25 p=50

![w:800](figs/llf2.png)



---

**Outline**

1. Real-time OS
2. Real-time Scheduling
### 3. Priority Inversion
- priority inheritance
- Priority Ceiling Protocol

---
#### Priority Inversion

The phenomenon of a high-priority process waiting for a resource occupied by a low-priority process for a long time.

- Priority inversion problem exists in priority-based preemptive scheduling algorithms.
Priority: T1>T2>T3
![w:600](figs/rt-pi.png)



---
#### Priority Inversion
<style scoped>
{
  font-size: 28px
}
</style>
The phenomenon of a high-priority process waiting for a resource occupied by a low-priority process for a long time.

- Priority inversion problem exists in priority-based preemptive scheduling algorithms.
Priority: T1>T2>T3
![bg right:50% 100%](figs/priority1.png)


---
#### Priority Inheritance
- A low-priority process that is holding a resource inherits the priority of a high-priority process that requests the same resource.
- The priority of the resource-holding process is raised only when the low-priority process is blocked.
![bg right:50% 100%](figs/priority2.png)




---
<style scoped>
{
  font-size: 30px
}
</style>
#### Priority Inheritance
- A low-priority process that is holding a resource inherits the priority of a high-priority process that requests the same resource.
- The priority of the resource-holding process is raised only when the low-priority process is blocked.
![bg right:45% 100%](figs/rt-pi-1.png)
Note: Critical section refers to a code segment that accesses shared resources in a mutually exclusive manner.

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Priority Inheritance
- A low-priority process that is holding a resource inherits the priority of a high-priority process that requests the same resource.
- The priority of the resource-holding process is raised only when the low-priority process is blocked.
![bg right:45% 100%](figs/rt-pi-2.png)
Note: Critical section refers to a code segment that accesses shared resources in a mutually exclusive manner.


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Priority Inheritance
- A low-priority process that is holding a resource inherits the priority of a high-priority process that requests the same resource.
- The priority of the resource-holding process is raised only when the low-priority process is blocked.
![bg right:45% 100%](figs/rt-pi-3.png)
Note: Critical section refers to a code segment that accesses shared resources in a mutually exclusive manner.


---
#### Priority Inheritance
- A low-priority process that is holding a resource inherits the priority of a high-priority process that requests the same resource.
- The priority of the resource-holding process is raised only when the low-priority process is blocked.

![bg right:50% 100%](figs/rt-pi-4.png)
Note: Critical section refers to a code segment that accesses shared resources in a mutually exclusive manner.

---
#### Priority Inheritance
- A low-priority process that is holding a resource inherits the priority of a high-priority process that requests the same resource.
- The priority of the resource-holding process is raised only when the low-priority process is blocked.

![bg right:50% 100%](figs/rt-pi-5.png)
Note: Critical section refers to a code segment that accesses shared resources in a mutually exclusive manner.


---
#### Priority Inheritance
- A low-priority process that is holding a resource inherits the priority of a high-priority process that requests the same resource.
- The priority of the resource-holding process is raised only when the low-priority process is blocked.

![bg right:50% 100%](figs/rt-pi-6.png)
Note: Critical section refers to a code segment that accesses shared resources in a mutually exclusive manner.

---
#### Priority ceiling protocol
- The priority of a process holding a resource is set equal to the highest priority of any process that might potentially request that resource.
   - The priority of the resource-holding process is elevated regardless of whether a wait occurs or not.
   - The priority of the resource-holding process is higher than the upper limit of the priority of all locked resources in the system, thereby preventing blocking of task execution within a critical section.

---
<style scoped>
{
  font-size: 32px
}
</style>
### Summary

1. Real-time OS
- Definition of real-time OS, real-time tasks
2. Real-time scheduling
- Rate Monotonic(RM) Algorithm, Earliest Deadline First(EDF) Algorithm, Least Laxity First(LLF) Algorithm
3. Priority Inversion
- Priority Inheritance, Priority Ceiling Protocol