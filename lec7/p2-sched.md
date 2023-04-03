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
## Section 2 Single Processor Scheduling

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023

---

**Outline**

### 1. Concept of Processor scheduling
   - Timing and strategy for processor scheduling
   - Criteria for comparing scheduling algorithms
2. Scheduling Algorithms

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Time-Division Multiplexing of CPU resources

- Process switching: switching the current occupants of CPU resources
   - Save the execution context (CPU status) of the current process in the PCB
   - Restore the execution context of the next process

- Processor scheduling
    - Select the next process that will occupy the CPU from the ready queue
    - Select an available CPU to the ready process from multiple available CPUs
- Scheduler: a kernel function that picks a ready process to run
- Scheduling Algorithm
     - What principles are used to select a ready process?


---
#### Scheduling timing

- The conditions when the kernel starts to schedule
    - Process switches from running state to waiting/ready state
    - The process was terminated
- Non-preemptive systems
   - Current process voluntarily yields CPU
- Preemptive system
   - Interrupt requests are handled by the interrupt handler

---

#### Scheduling Algorithm

Determine how to select the next process from the ready queue for execution
- Issues to be addressed:
    - Based on what criteria is selection made?
    - Which process in the ready queue is picked?
- Scheduling Algorithms
    - Scheduling strategies implemented in kernel
- Criteria for comparing scheduling algorithms
    - Which strategy/algorithm is better?
 
---
#### Usage Patterns of Processor Resource 
- Process alternates between CPU computation and I/O operations
    - Each scheduling decision determines which task is assigned to CPU for the next computation.
    - With time slicing, a process may be forced to yield the CPU before completing its current computation.

  ![w:650](figs/cpu-usage.png)

 
---

**Outline**

1. Concept of Processor scheduling
   - Timing and strategy for processor scheduling
 ###  - Criteria for comparing scheduling algorithms
2. Scheduling Algorithms

---

#### Criteria for comparing scheduling algorithms
- CPU Utilization: the percentage of time when the CPU is busy
- Throughput: the number of processes completed per unit of time
- Turnaround time: the total time the process takes from initialization to completion (including waiting time)
- Ready-waiting time: the total time that the ready process waits in the ready queue
- Response time: the total time from submitting a request to producing a response
- Fairness: ensuring processes have equal access to resources, such as CPU time


 
---
<style scoped>
{
  font-size: 28px
}
</style>

#### Criteria for comparing Scheduling Algorithms: Throughput and Latency
- Requirements of scheduling algorithms: desire "faster" service
- What is "faster"?
   - High bandwidth for file transfers, high throughput for scheduling algorithms
   - Low latency when playing games, low response time for scheduling algorithms
   - These two factors affect each other
- Analogy with water pipes
   - Low latency: When you drink water, you want the water to flow out as soon as you open the tap
   - High bandwidth: You want a large volume of water to flow simultaneously from the faucet when filling a swimming pool, regardless of latency

 
---
<style scoped>
{
  font-size: 30px
}
</style>

#### Criteria for Comparing Scheduling Algorithms: Response Time
- Reduce response time
   - Handle the user's request promptly, and provide feedback to the user as soon as possible
- Reduce fluctuations of average response time
   - In interactive systems, predictable response time is more important than low average response time with high variance
- Low-latency scheduling improves user interaction experience
   - If the cursor on the screen does not move when the mouse is moved, the user may restart the computer
- Response time is the computing delay of the OS
 
---
#### Criteria for Comparing Scheduling Algorithms: Throughput
- Increase throughput
    - Reduce overhead (OS overhead, context switching)
    - Efficient utilization of system resources (CPU, I/O devices)
- Reduce waiting time in the ready queue
    - Reduce waiting time for each ready process
- The OS needs to ensure that throughput is not affected by user interaction
   - Even if there are many interactive tasks
- Throughput is the computing bandwidth of the OS

 
---
#### Criteria for Comparing Scheduling Algorithms: Fairness

If one user runs more processes than other users, is it fair? What can be done?

- Definition of fairness
   - Ensure each process has equal CPU time
   - Ensure each process has the same waiting time in the ready queue
- Fairness usually increases average response time

 
---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

1. Concept of processor scheduling
### 2. Scheduling Algorithms
- First-come First-served(FCFS), Short job first(SJF)
- Shortest remaining time(SRT), Highest Response Ratio Next(HRRN)
- Round-Robin(RR)
- MultiQueue(MQ), Multi-Level Feedback Queue(MLFQ)
- Fair Share Scheduling(FSS)
---

#### First Come, First Served (FCFS)

FCFS: First Come, First Served
- Processes are scheduled in the order they arrive in the ready queue.
- When a process becomes waiting or end state, the next process in the ready queue occupies the CPU
- Metric
    - Turnaround time of FCFS 
  
---

#### An Example of FCFS

- Example: 3 processes with calculation times of 12,3,3

![w:800](figs/sched-fifo.png)

   
---

#### Characteristics of FCFS

- Advantages: Simple
- Disadvantages:
   - High fluctuation in the average waiting time
   - Short jobs/tasks/processes may have to wait for a long one to finish.
   - Low utilization of I/O and CPU resources
      - CPU-intensive processes will lead to a situation that when I/O devices are idle, I/O-intensive processes have to wait.
  
---

#### Short Job First (SJF)

Short Job First
- Selects the process with the shortest expected CPU time from the ready queue to run.
- The ready queue is sorted by expected execution time.

  ![w:800](figs/sched-sjf.png)

---

#### Characteristics of SJF

**SJF has the best average turnaround time.**

Can changing the order of process execution reduce the average turnaround time?
  ![w:800](figs/sched-sjf-best.png)


  ---

#### Characteristics of SJF

- May cause starvation
   - A sequence of short jobs may prevent long jobs from obtaining CPU resources.

- Requires knowledge of the future
   - How to estimate the duration of the next CPU calculation?
   - Simple solution: ask the user
      - Kill the corresponding process if the user cheats
      - What to do if the user doesn't know?
---

#### Execution Time Estimation of SJF

- Use historical execution time to predict future execution time

$\tau_{n+1} = \alpha t_n+(1-\alpha) \tau_n, where 0\le \alpha \le 1$
$t_n$ -- the nth CPU calculation time
$\tau_{n+1}$ -- CPU calculation time estimate for the n+1th time

$\tau_{n+1} = \alpha t_n+(1-\alpha) \alpha t_{n-1} + (1-\alpha) (1-\alpha) \alpha t_{n-2} + .. .$


---

#### Execution Time Estimation of SJF

- Execution Time Estimation
  ![w:800](figs/sched-sjf-time.png)



---

#### Shortest Remaining Time (SRT)

Shortest Remaining Time (SRT)

- SRT supports preemption, i.e., if a new process is ready and its service time is smaller than the remaining time of the current process, then the new process will be scheduled to execute.
---

#### Highest Response Ratio Next, HRRN

Highest Response Ratio Next, HRRN

- HRRN is mainly used for job scheduling.
- This algorithm is a balanced combination of FCFS and SJF, considering both the waiting time and estimated running time of each job.
- In each job scheduling, calculate the response ratio of each job in the queue and select the job with the highest response ratio to run.

---

#### Highest Response Ratio Next, HRRN

- Select the process in the ready queue with the highest response ratio R value.

$R=(w+s)/s$
w: ready waiting time (waiting time)
s: execution time (service time)

- Improve the Shortest Job First algorithm.
- Focus on the waiting time of the process.
- Prevent indefinite postponement

---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

1. Concept of processor scheduling
2. Scheduling algorithm
- First-come, First-served(FCFS), Short job first(SJF)
- Shortest remaining time(SRT), Highest Response Ratio Next(HRRN)
### Round-Robin(RR)
- MultiQueue(MQ), Multi-Level Feedback Queue(MLFQ)
- Fair Share Scheduling(FSS)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Round-Robin (RR)

RR, Round-Robin

- Time slice
   - The basic time unit for allocating processor resources.
- Algorithm ideas
   - At the end of the time slice, switch to the next ready process according to FCFS algorithm
   - Execute a time slice q every (n – 1) time slices.
  ![w:700](figs/sched-rr-1.png)

---

#### An example of RR

  ![w:700](figs/sched-rr-2.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Time slice length of RR

- Overhead of the RR algorithm: additional context switching
- Too large time slice
   - Long waiting time, degenerates into FCFS in the extreme case.
- Too small time slice
   - Highly responsive, but produces lots of context switches
   - A lot of context switching's overhead affects system throughput
- Time slice length selection goal:
   - Choose an appropriate time slice length
   - Empirical rule: keep the context switching overhead within 1%

---

#### Comparison between FCFS and RR

![w:800](figs/sched-rr-3.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

1. The concept of processor scheduling
2. Scheduling algorithm
- First-come, First-served(FCFS), Short job first(SJF)
- Shortest remaining time(SRT), Highest Response Ratio Next(HRRN)
- Round-Robin(RR)
### MultiQueue(MQ), Multi-Level Feedback Queue(MLFQ)
- Fair Share Scheduling(FSS)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### MultiQueue Algorithm, MQ

- The ready queue is divided into multiple independent subqueues
    - For example, foreground process (interactive) sub-queue, background process (batch processing) sub-queue
    - Processes with the same priority belong to a certain queue and cannot cross queues
- Each queue has its own scheduling policy
    - Such as: foreground process – RR, background process – RR/FCFS with a large time slice
 
  - Rule 1: If A's priority > B's priority, run A (do not run B).
  - Rule 2: If A's priority = B's priority, run A and B alternately.
---

#### MultiQueue Algorithm, MQ

- Scheduling between queues
   - Fixed priority
     - Handle foreground (interactive) processes first, then process background processes
     - May cause starvation
   - Round-Robin
     - Each queue gets a fixed amount of CPU time to schedule its processes
     - For example: 80% CPU time is used for foreground processes, 20% CPU time is used for background processes


---

#### Multi-Level Feedback Queue, MLFQ

MLFQ, Multi-Level Feedback Queue
- In 1962, Prof. Corbato from MIT first proposed the multi-level feedback queue, which was applied to CTSS (Compatible Time-Sharing System)
- Solve two problems
   - How to optimize turnaround time without knowing how long the job will run
   - How to reduce the response time and provide a good interactive experience for users?


---

#### Multi-Level Feedback Queue, MLFQ

- Key issues: how to schedule without complete knowledge?
    - How to build a scheduler that reduces both response time and turnaround time when the length of the process is unknown?
- Inspiration: Learn from History
    - Predict the future with historical experience
- Inherit the scheduling rules of MultiQueue
    - If A's priority > B's priority, run A (do not run B)
    - If A's priority = B's priority, run A and B in a round-robin/FIFO manner
---

<style scoped>
{
  font-size: 28px
}
</style>
#### Multi-Level Feedback Queue, MLFQ

Basic scheduling rules
  - When a job enters the system, it is placed at the highest priority (top-level queue)
  - If the process doesn't finish within the current time slice, it will be moved to the next lower priority queue.
  - If a job actively yields the CPU within its time slice, its priority remains the same
  - Time slice size increases as priority level increases
  ![w:500](figs/sched-mlfq.png)

 
---

#### An Example of MLFQ with three priority queues

- CPU-intensive processes enter the highest priority queue first;
- After executing a 1ms time slice, the scheduler will reduce the priority of the process by 1 and moves it to the next highest priority queue;
- After executing a 2ms time slice, the process enters the lowest priority queue of the system, stays there, and executes using a 4ms time slice.

---
<style scoped>
{
  font-size: 32px
}
</style>

#### Multi-Level Feedback Queue, MLFQ

- Characteristics of the MLFQ 
    - The priority of CPU-intensive processes drops quickly
    - I/O-intensive processes stay at high priority

- Potential problems
   - CPU-intensive processes may starve
   - Malicious processes may try to stay at high priority
  ![bg right:40% 100%](figs/sched-mlfq.png)

 
---

<style scoped>
{
  font-size: 32px
}
</style>

#### Multi-Level Feedback Queue Scheduling Algorithm MLFQ

Basic scheduling rules
  - If A's priority > B's priority, run A (do not run B)
  - If A's priority = B's priority, run A and B in a round-robin/FIFO manner
  - When a job enters the system, it is placed at the highest priority (the top-level queue)
  - Once a job has used up its time slice in a certain level (regardless of how many times it has voluntarily given up the CPU), its priority is reduced (moved to a lower-level queue)
  - After a period of time S, move all jobs in the system to the highest priority queue

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Fair Share Scheduling Algorithm, FSS

FSS, Fair Share Scheduling
- Control users' access to system resources
    - Different users have multiple processes
    - Resources are allocated according to user priority
    - Ensures that unimportant users cannot monopolize resources
    - Unused resources are distributed proportionally
  
  ![bg right:38% 100%](figs/sched-fss.png)

  
---
<style scoped>
{
  font-size: 32px
}
</style>

#### Characteristics of different scheduling algorithm

- First Come First Served (FCFS) algorithm
    - Poor average waiting time
- Shortest job first(SJF) algorithm
    - Minimal average turnaround time
    - Requires accurate prediction of calculation time
    - No preemption allowed; may cause starvation
- Shortest Remaining Time (SRT) algorithm
    - Improve the short job first algorithm, allowing preemption
    - May cause starvation
  
---
<style scoped>
{
  font-size: 32px
}
</style>

#### Characteristics of different scheduling algorithm

- Highest Response Ratio Next (HRRN) algorithm
    - Based on SJF, non-preemptive
    - Consider both the waiting time and the estimated run time of each job
- Round Robin (RR) algorithm
    - Fair, but has a poor average waiting time
- Multi-Level Feedback Queue (MLFQ) algorithm
    - Integration of multiple algorithms
- Fair share scheduling(FSS) algorithm
    - Fairness is the primary consideration
---

### Summary

1. Concept of Processor Scheduling
- Timing and strategy of processor scheduling, criteria for comparing scheduling algorithms
2. Scheduling Algorithms
- FCFS, SJF, SRT, HRRN
- RR
- MQ, MLFQ, FSS