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

# Lecture 3 Isolation and batch processing based on privilege level
## Section 1 Computer Systems: OS's Perspective

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

<br>
<br>

Spring 2023

---
**Outline**

### **<font size=5>1. The relationship between OS and hardware</font>**
### 2. The relationship between OS and applications
### 3. Isolation mechanism

---

#### Computer System

![bg right:50% 100%](figs/computer-arch-app.png)

Computer architecture is an **level of abstraction** designed to implement **information processing** applications that make use of **existing manufacturing technologies** efficiently.
<br>
-- cs-152 berkeley
 
---
#### Abstraction Levels of Computer System

**HARDWARE** SUPPORTED **OS** SUPPORTED **APPLICATIONS**


![bg right:35% 100%](figs/abstract-of-system.png)

* The operating system is located between the hardware (HW) and applications (APP)
* Understanding the relationship between OS and HW/APP can help you better master the OS

---
#### Instruction Set Architecture(ISA): hardware and software interface
**Boundary** between **hardware** and **OS**: ISA + registers
![w:1150](figs/hardware-software-interface.png)

---
#### OS: Virtualization and Abstraction of Hardware

![bg 55%](figs/arch-os-relation.png)

---
#### Architecture of RISC-V Processors
 
![w:800](figs/rv-arch.png)

---
#### Framework of u/rCore
![w:900](figs/ucore-arch.png)


---
**Outline**

1. The relationship between OS and hardware
### **<font size=5>2. The relationship between OS and applications</font>**
3. Isolation mechanism

---
#### OS Support for Application Execution
- Provide services
- Provide system calls
- Manage address space layout
---
#### Provides services
* Provide services through **system calls**
* **System call**: OS/APP interface (one of the boundaries)
![w:1100](figs/syscall-overview.png)

---
#### Details of system call implementation
- What happens when we call `ssize_t read(int fd, void *buf, size_t count);`?
- Can code in user space call functions in kernel space?
- Can code in kernel space call functions in user space?


---
#### System calls : Enhance system security and reliability

- Characteristics of function calls
   - Advantages: fast execution;
   - Advantages: flexible - easy to pass and return complex data types;
   - Advantages: Familiar mechanisms for programmers,...
   - Disadvantages: Unreliable/malicious app may crash the whole system.


<!--
---
  ## Relationship between OS and application -- syscall

![w:1200](figs/syscall-proc.png)

---
## Relationship between OS and application -- syscall

![w:1200](figs/syscall-file.png) -->

---
#### Address space of a Process
The address space (memory layout) of the process defines the **boundary** between OS and APP.
![w:750](figs/app-mem-layout.png)

---
#### Address Space Division

The address space (memory layout) of the process defines the **boundary** between OS and APP.
![w:530](figs/syscall-read.png)

---
#### Kernel page table isolation (KPTI, kernel page-table isolation)
![w:800](figs/KPTI.png)

---
**Outline**

1. The relationship between OS and hardware
2. The relationship between OS and applications
### **<font size=5>3. Isolation mechanism</font>**
* Why we need isolation?
* What problem can we solve through isolation?
* How to achieve isolation?

---
#### Isolation can:
- prevent program X from destroying or monitor program Y
   - read/write memory, 100% CPU usage, change file descriptors
- Prevent processes from interfering with the operating system
- Protection from malicious programs, viruses, trojans and bugs
   - erroneous processes may try to trick the hardware or the kernel

---
#### What is Isolation?
- **Definition** of isolation
   - Applications can not **affect** (or disrupt) the normal **execution** of other applications/OS. There is no information **disclosure** in the whole system. 
- The **Essence of Isolation**
   - Appears only when there is a need to exchange information or share resources
- Isolation doesn't mean to forbid resource/information share

---
#### Isolation Boundary
Isolation requires building boundary.
- Boundaries determine protection domain of each process
   - Shared resources crossing protection domains may be **risky**
- Mandatory isolation
   - Avoid the safety impact of the faulty unit on the whole system
- Isolated unit
   - The running programs.


<!-- https://blog.csdn.net/ceshi986745/article/details/51787424
Ape Science~Six isolation techniques that programmers must know -->

---
#### Isolation method

- Classification of isolation methods
   - **Software** based isolation
   - **Hardware** based isolation
   - **Network** based isolation
---

#### Classification of OS isolation APP
*  **Control** Isolation : Privileged Level Mechanism
   * User Mode vs Kernel Mode
*  **Data** Isolation : address space
   * User address space vs kernel address space
*  **Time** Isolation : interrupt Handler
   * Interrupt the running App in user mode at any time
* Handling of breaking isolation: **exception handling**
   * OS handles the abnormal behavior of user-mode App in kernel mode timely


---

#### Data Isolation: Virtual Memory

- Virtual Memory
   - Security issues of reading and writing memory
   - Security issues of Inter-Process communication
   - The problem of memory space utilization
   - The efficiency of memory read and write
- Address spaces
   - A program can only access its own memory
   - **Any memory access request to other process's space will be denied** without permission


---
#### How virtual memory works
![w:800](figs/vm.png)

<!-- ---
---
## Isolation mechanism -- the main isolation method -- virtual memory
![w:600](figs/vm-pagetable.png)



## Isolation mechanism -- the main isolation method -- virtual memory
![w:1100](figs/tlb.png)

---
## Isolation mechanism -- the main isolation method -- virtual memory
![w:900](figs/mmu.png)

---
## Isolation mechanism -- the main isolation method -- virtual memory
![w:900](figs/arch-with-tlb-mmu.png) -->

---
#### Control Isolation: Privileged Mode
- Privileged mode in CPU hardware
   - Prevent applications from accessing device and sensitive CPU registers
     - Address Space Configuration Register
     - Shutdown related instrutions or registers
     - ...

---
#### Privileged Mode

- CPU hardware supports different privileged modes
   - Kernel Mode (kernel mode) vs User Mode (user mode)
   - Kernel mode can perform privileged operations forbidden in user mode
     - Access to **Peripherals**
     - **configure** address space (virtual memory)
     - Read/write special **system-level registers**
- The OS kernel runs in kernel mode
- The application runs in user mode
- Every microprocessor has similar user/kernel mode flags

---
#### Time Isolation: Interrupt/Exception Mechanism
- CPU/hardware supports interrupt/exception handling
- Response to and handle **abnormal behavior** of the application timely
- Interrupt applications occupying CPU for long time.
- interrupt occurs **asynchronously**,  as a result of signals from external I/O devices.
   - Asynchronous means that hardware interrupt is not caused by any dedicated CPU instruction.

---
#### Interrupt handler

- Interrupt handler:
   1. I/O device sends a signal to a pin of the processor and places the exception number on the system bus to trigger interrupt;
   2. After the current instruction is executed, the processor reads the exception number from system bus, saves context, and switches to **kernel mode**;
   3. The interrupt handler is called. After it is finished, the CPU will begin to execute the next normal instruction in the program.

---
#### Clock Interrupt
- Timer can generate interrupts in a stable and regular manner
   - Prevent applications from occupying the CPU for long time
   - Allow the OS kernel to manage resource periodically

---
### Summary

- Understand the relationship between computer hardware and operating system: interface/boundary
- Understand the relationship between operating system and applications: interfaces/boundaries
- Understand how the operating system isolates and restricts applications