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

## Section 2 Overview of Multiprocessor Scheduling


<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023

---

**Outline**

### 1. Single Queue Multiprocessor Scheduling(SQMS)
2. Multi-Queue Multiprocessor Scheduling(MQMS)

---

#### Single Queue Multiprocessor Scheduling
Single Queue Multiprocessor Scheduling, SQMS
- Reuse the basic architecture of single-processor scheduling.
- All processes that need to be scheduled are put into **a single queue**
![w:800](figs/sqms.png)


---
#### Characteristics of Single Queue Multiprocessor Scheduling
- Lack of scalability
- Weak cache affinity
![w:800](figs/sqms.png)

---
#### Affinity and load balancing of multiprocessor scheduling
Processes should be scheduled to run on the same CPU as much as possible. While maintaining affinity for some processes, other processes may need to sacrifice affinity to achieve load balancing.
![bg right:50% 90%](figs/sqms-cache-affinity.png)

---

**Outline**

1. Single Queue Multiprocessor Scheduling(SQMS)
### 2. Multi-Queue Multiprocessor Scheduling(MQMS)

---

#### Multi-Queue MultiprocessorScheduling, MQMS
<style scoped>
{
  font-size: 30px
}
</style>
- Basic scheduling framework contains **multiple scheduling queues**, each of which may use different scheduling rules.
- When a process enters the system, it is placed in a scheduling queue based on some heuristic rules.
- **Each CPU schedules independently** to avoid data sharing and synchronization issues of single-queue scheduling.
![w:800](figs/multi-queue.png)
![w:800](figs/mqms.png)

---
#### Characteristics of multi-queue multi-processor scheduling
- **Scalable**: The number of queues increases with the number of CPUs, so the cost of lock and cache contention is not a big issue.
- Good **cache affinity**: All processes are kept on fixed CPUs, so cache data can be utilized well.
![w:800](figs/multi-queue.png)
![w:800](figs/mqms.png)


---
#### Load Imbalance in Multi-Queue Multiprocessor Scheduling
- Suppose there are 4 processes and 2 CPUs; all queues use round-robin scheduling strategy; process C completes.
- A obtains twice as much CPU time as B and D.

![w:1000](figs/mqms-problem-1.png)
![w:1000](figs/mqms-problem-2.png)


---

#### How to Solve Load Imbalance in MQMS?

- Suppose there are 4 processes and 2 CPUs; all queues use round-robin scheduling strategy; A and C complete, leaving only B and D in the system.
   - CPU1 is busy
   - CPU0 is idle

![w:900](figs/mqms-problem-3.png)
![w:900](figs/mqms-problem-4.png)



---
#### Process Migration
- Load balance can be achieved through migrating processes across CPUs.
   - Situation: CPU0 is idle, CPU1 has some processes.
   - Migrate: Migrate B or D to CPU0

![w:900](figs/mqms-problem-3.png)
![w:900](figs/mqms-problem-4.png)



---
#### How Does MQMS Determine When to Migrate Processes?

- Situation: A is left alone on CPU 0, B and D run alternately on CPU 1
- Migration: **Continuously migrate and switch** one or more processes

![w:900](figs/mqms-problem-5.png)
![w:900](figs/mqms-problem-6.png)



---
<style scoped>
{
  font-size: 32px
}
</style>

#### Work Stealing in MQMS
- he (source) queue with a relatively small number of processes periodically checks other (target) queues to see if they have significantly more processes than itself.
- f a target queue is significantly fuller than the source queue, one or more processes are "stolen" from the target queue to achieve load balance.


![w:900](figs/mqms-problem-5.png)
![w:900](figs/mqms-problem-6.png)

---
#### Queue Inspection Interval for Work Stealing
- Frequent inspection of other queues can result in high overhead and poor scalability.
- If the inspection interval is too long, it can lead to serious load imbalance.
![w:900](figs/mqms-problem-5.png)
![w:900](figs/mqms-problem-6.png)