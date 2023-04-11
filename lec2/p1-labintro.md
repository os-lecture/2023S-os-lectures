---
noteId: "daf1ce51d86511edb7d82f06d610f713"
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

# Lecture 2 Experiment Introduction
## Section Introduction of Basic Experiments

<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

<br>
<br>

Spring 2023

---
Outline

### 1. Principle, Practice and Experiments
2. Step-by-step OS Experiments
3. Experiment Schedule

---

##### Meet the increasing demands of applications
* LibOS
* Batch Processing OS
* Multiprogramming and time-sharing multi-task OS

---

##### Gradually show the conceptual abstraction of OS

* OS with the abstraction of address space  
* OS with the abstraction of process 
* OS with the abstraction of file system
---

##### Gradually show the key capabilities of OS

* OS with inter-process communication
* OS with concurrent processing
* OS with I/O device management
---
Outline

1. Principles, Practice and Experiments
### 2. Step-by-step OS Experiments
3. Experiment Schedule

---

<style scoped>
{
  font-size: 30px
}
</style>


#### LibOS

- A prototype of ancient OS
- A modern and simple embedded OS

##### Related Knowledge Points
- Function call: cooperation of the compiler and the OS
- Hardware start and software start
- Write/debug bare metal programs

Bare Metal Program: an OS-type program that cannot be regarded as OS

![bg right:35% 100%](figs/os-as-lib.png)

---

#### Batch OS
- Support system calls

##### Related Knowledge Points

- Privilege level / privileged operation
- RISC-V privilege level/privileged operation
- system calls/exceptions
- Load & Execute & Switch Apps
- Privilege level switching

![bg right:45% 100%](figs/batch-os.png)

---

#### Multiprogramming OS
- Support multiple programs **residing in the memory at the same time**
- Support multiple programs **executing in a sequential way**
##### Related Knowledge Points
- Memory division and management
- Collaborative Scheduling

![bg right:46% 100%](figs/multiprog-os.png)

---

#### Time-sharing multitasking OS
- Support multiple programs **execute in turn**

##### Related Knowledge Points
- **Interrupt handling**
- context switching
- Preemptive scheduling

![bg right:45% 100%](figs/timesharing-os.png)

---
#### Address Space Abstraction
- Support **memory space isolation** between programs

##### Related Knowledge Points
- Address space abstraction
- Static memory allocation
- Dynamic memory allocation
- Paged memory management

![bg right:48% 100%](figs/address-space-os.png)

---
#### Address Space Abstraction
- **Virtual Memory**
##### Related Knowledge Points
- Principle of locality
- Page fault
- Virtual page storage
- Page Replacement algorithm
![bg right:48% 100%](figs/address-space-os.png)

---

#### Process Abstraction

- Support **dynamic creation and execution** for programs

##### Related Knowledge Points

- Process abstraction
- Process management
- Scheduling mechanisms
  
![bg right:48% 100%](figs/process-os.png)

---

#### Process Abstraction

- Support **parallel processing** among multiple processors 

##### Related Knowledge Points
- Multi-processor/multi-core architecture
- Multi-processor scheduling
- Scheduling implementation in OS
  
![bg right:48% 100%](figs/process-os.png)

---
#### File Abstraction
- **Convenient persistent storage** for data processing

##### Related Knowledge Points
- File abstraction
- File architecture
- File system design and implementation

![bg right:50% 100%](figs/fs-os.png)

---

<style scoped>
{
  font-size: 33px
}
</style>


#### OS with Interprocess Communication
- Explicit/implicit **information exchange**  between processes
##### Related Knowledge Points
- Signal, pipe
- Message queue, shared memory
- I/O redirection

![bg right:50% 100%](figs/ipc-os.png)


---
#### OS with Concurrent Processing
- Improve CPU **utilization**
##### Related Knowledge Points
- Thread: user/kernel thread
- Coroutine
- The relationship of process, thread and coroutine

![bg right:50% 100%](figs/sync-os.png)

---

<style scoped>
{
  font-size: 32px
}
</style>


#### OS with Concurrent Processing
- **Resources sharing**
- Synchronization & mutex
##### Related Knowledge Points
- Mechanisms of synchronization & mutex
- How to solve concurrency problems, such as deadlock
![bg right:45% 100%](figs/sync-os.png)
---
#### OS with I/O management
- Support various peripherals
##### Related Knowledge Points
- Device abstraction
- Device Execution Model
- Synchronous/Asynchronous I/O
- I/O device management
![bg right:50% 100%](figs/io-os.png)


---
Outline

1. Principles, Practice and Experiments
2. Step-by-step OS Experiments
### 3. Experiment Schedule

---

#### Experiment 1: Basic support of the operating system
##### Covered Knowledge
* LibOS, batch OS, multiprogramming and time-sharing multitasking OS
##### Knowledge Point: Privilege Level and Switching
- Computer/OS startup
- Privilege level switching, system calls, privilege level related exceptions, task switching
- Relationship among application, library and OS kernel 

---

#### Experiment 2: Address Space
##### Covered Knowledge
* Address Space Abstraction
##### Knowledge point: page table
- Address space
- Data exchange/control interaction between application and kernel in different address spaces
- Memory/address-related exceptions (e.g., page fault)

---

#### Experiment 3: Process Management and Scheduling
##### Covered content
* Process abstraction
##### Knowledge point: process control block (PCB)
- Process management
- Process scheduling algorithms

---

#### Experiment 4: File System and Inter-process Communication
##### Covered content
* File abstraction, inter-process communication
##### Knowledge point: File
- Implementation of File system 
- Inter-process communication mechanisms

---
#### Experiment 5: Synchronization & Mutex
##### Covered content
* Concurrent processing
##### Knowledge points
- Thread
- The mechanism of synchronization & mutex, solving the problem of synchronization & mutex, solving the deadlock problem
- Priority inversion problem

---

<style scoped>
{
  font-size: 33px
}
</style>


#### Reference Implementation

* Reference Implementation
     * [uCore](https://github.com/uCore-RV-64/uCore-RV-64-answer)
     * [rCore](https://github.com/zflcs/rCore)

* Suggestions for preparing the experiment reports
     * Start time, completion time and the number of code submissions
     * The main problems and solutions you faced in the experiments
     * How does your solution differ from the reference implementation?

---

<style scoped>
{
  font-size: 33px
}
</style>


#### Course Project

After completing the basic experiment 1~5 within 4 weeks, discuss with your teacher to choose a project.


Implement support for new features (multi-core, new peripherals, new processors, new functions)

Such as supporting games, Raspberry Pi/SiFive, network, USB, AI, etc.

National OS Development Competition for Undergraduates
