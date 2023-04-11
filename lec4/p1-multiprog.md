---
noteId: "daf3ca20d86511edb7d82f06d610f713"
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

# Lecture 4 Multiprogramming and Time-sharing Multi-task
## Section 1 Process and Process Model
<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

<br>
<br>

Spring 2023

---
**Outline**

### 1. Multiprogramming and Cooperative Scheduling
2. Time-sharing Multi-task and Preemptive Scheduling
3. The Concept of Process
4. Process Model

---

#### History

The widespread use of OS began during the transition from mainframes to minicomputers.

- OS/360 is a multiprogramming batch OS in the mainframe (System/360) era
- PDP series minicomputers from DEC
- Many companies may have their own computers
- Multiprogramming becomes common

---

#### Multiprogramming

- Multiple executable programs in the memory
- The programs share the processor

##### Job
- One-time **execution process** of an application

Terms appeared in history: Job, Multiprogramming
- Job and Multiprogramming are concepts used by IBM for multiprogramming

---

#### Cooperative scheduling

- Executables program voluntarily relinquish the processor
- OS does not interrupt the running program
- OS is responsible for selecting the next program to take the processor for execution

---
**Outline**

1. Multiprogramming and Cooperative Scheduling
### 2. Time-sharing Multi-task and Preemptive Scheduling
3. The Concept of Process
4. Process Model

---
#### History

The widespread use of minicomputers led to a demand for time-sharing and multi-task capabilities, resulting in the development of time-sharing multi-user OS.

- Widely use of DEC's PDP and VAX minicomputers
- DEC's VMX operating system
- MIT's CTSS operating system
- AT&T's UNIX operating system

---
#### Time-sharing Multi-task from user's perspective

Time-sharing Multi-task: from user's perspective

- Multiple executable programs residing in memory
- Each executable program takes the processor in a time-sharing way
- The OS allocates CPU to each executable program by time slices
- **Process**: one-time execution process of the application


---
#### Time-sharing Multi-task from the perspective of OS



- **Process**: A dynamic **execution process** of a program with certain **independent functions** on a **data collection**. Also known as **Task**.
- **Process Switching**: switch from the process corresponding to one application to the process corresponding to another application.

---
#### Job, Task and Process

Terms appeared in history: Job, Task, Process
- Task and Process are concepts proposed by Multics and UNIX, respectively, for time-sharing multi-task
- A process is one-time execution process of an application. In OS context, task and process are the same meaning
- A job (goal) is a whole formed by a group of related program execution processes (processes, tasks) for the same goal

Reference: [Difference between Job, Task and Process](https://www.geeksforgeeks.org/difference-between-job-task-and-process/)


---
#### Preemptive Scheduling

- Process passively relinquishes processor
- The process uses the processor in turn according to time slice, which is a "pause-continue" execution process
- Based on the clock interrupt mechanism of hardware, OS can interrupt the running program at any time
- The OS is responsible for selecting the next program to take the processor for execution

---
**Outline**

1. Multiprogramming and Cooperative Scheduling
2. Time-sharing Multi-task and Preemptive Scheduling
### 3. The Concept of Process
4. Process Model

---

#### Process Switching

![w:1250](figs/task-features.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Process Features
- Dynamic
   - Execution starts --> Pause --> Continue --> Execution ends
- Concurrency
   - Multiple processes are executing during a time period
- Limited independence
   - Processes do not need to be aware of each other's existence in the system

<!--More powerful features that need to be developed:
- Stronger isolation, collaboration between tasks...
-->

---
#### Composition of Process and Program

![bg w:1100](figs/task-prog.png)

---
#### Composition of Process and Program

![bg w:600](figs/task-in-mem.png)


---
<style scoped>
{
  font-size: 32px
}
</style>

#### Relationship between Task (Process) and Program

- A task is an abstraction of a program in the execution of OS
   - program = file (static & executable)
   - task = program in execution = program + execution status
 
- Multiple executions of the same program lead to different tasks
   - E.g., multiple executions of the command "ls" lead to multiple tasks
 
- Resources required for task execution
   - Memory: store code and data
   - CPU: execute instructions


---
#### Differences between Task (Process) and Program

- Tasks are dynamic, programs are static
   - A program is a collection of ordered codes
   - A task is the execution of a program
- Tasks are temporary, programs are permanent
   - A task is a state-changing process
   - Programs can be saved permanently
- Tasks and programs have different compositions
   - The task consists of program codes, data and process control blocks


---
#### Process Status
A process contains all status information about a running program
- **Control Flow** for task execution
   - Code content and code execution location (code segment)
- **Data** accessed by the task
   - Memory read and written by tasks (heap, stack, data segment)
   - Registers read and written by tasks
     - general-purpose registers

---
#### Process Status
A process contains all status information about a running program
- OS manages the data related to the task (task **context** )
     - General purpose registers required for task switching
     - Status registers required for task switching (PC, etc.)
     - Other information: the stack address of the task, etc.
     - Other resources:  …

---
#### Task Control Block (TCB)
The core data structure of OS for managing tasks, also known as process control block (PCB)
- OS **manages and controls the information used by the process**
- OS uses TCB to describe the basic information and changing status of the process 
- TCB is the only symbol for a process
- Each task has a corresponding TCB in OS

---
#### Process Control Block Managed by OS

![bg w:800](figs/task-control-block.png)


---
**Outline**

1. Multiprogramming and Cooperative Scheduling
2. Time-sharing Multi-task and Preemptive Scheduling
3. The Concept of Process
### 4. Process Model

---
#### Process State: Created and Ready
- Created --> Ready
   - When was it created?
   - How to create?
![bg right:50% 60%](figs/task-create.png)

---
#### Process State: Running
- Created --> Ready --> Running
   - The kernel selects a ready task
   - How to run?

![bg right 70%](figs/task-run.png)


---
#### Process State: Waiting
- Created --> Ready --> Running --> Waiting
   - The reason why the task enters the waiting status
     - Itself
     - The outside world

![bg right 70%](figs/task-wait.png)


---
#### Process State Transition: Waking up
- Created --> Ready --> Running --> Waiting --> Waking up
   - The reason for waking up the task
     - Self: wake up naturally?
     - Outside: be Woke up?
       
![bg right 70%](figs/task-wakeup.png)


---
#### Process State Transition: Preempted
- Created --> Ready --> Running --> Preempted
   - Why is the task preempted?
  
![bg right 70%](figs/task-preempt.png)


---
#### Process State: Exited
- Created --> Ready --> Running --> ...... --> Exited
   - Why did the task exit?
     - Voluntary?
     - Forced?


![bg right 70%](figs/task-quit.png)


---
#### Three-State Process Model
![w:700](figs/task-model.png)

---
#### Process State Transition and System Call

- Created --> Ready --> Running --> ...... --> Exited
- Preempted, waiting, waking up

What system calls are involved?
- exit, sleep
- ...
![bg right 70%](figs/task-quit.png)


---
#### Process State Transition and Process Switching

- Created --> Ready --> Running --> ...... --> Exited
- Preempted, waiting, waking up

During the lifecycle of a task, when does a task switch occur?

![bg right 70%](figs/task-quit.png)


---
#### Process Switching

![](figs/task-switch.png)
