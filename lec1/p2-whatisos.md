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

## Lecture 1 Operating System Overview
### Section 2 The Definition of Operating System

<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

<br>
<br>

Spring 2023

---

## The Definition of OS 

No common definition

   OS is a kind of system **software** that **manages hardware resources**, controls the operation of programs, improves the computer-human interface, and **provides support for application software** [from Computer Encyclopaedia]
 
<!--bg right 100%--> 
![bg right:35% w:450](./figs/os-position.png)

A bridge between user/app and machine

---

## OS is a control program
- A system software
- Execute the program, **provide services to the program**
- Control the program execution process for **error prevention**
- Provide **friendly human-computer interface**

![bg right:35% w:450](./figs/os-position.png)

---

## OS is a resource management program
- **Middle layer** between application and hardware
- **Manage** various hardware and software resources
- **Service** for accessing hardware and software resources
- **Resolve resource contention** to ensure fair usage

![bg right:25% w:380](./figs/os-position.png)

---

## Software classification in OS

- Shell - Command-line interface
- GUI – Graphical User Interface
- Kernel – The key components of OS

![bg right:45% w:600](./figs/sort-of-os.png)

---
## uCore/rCore: a simple OS kernel for teaching

![w:700](./figs/ucorearch.png)


---
## Abstraction of OS kernel

![w:800](./figs/os-abstract.png)


---
## Abstraction of OS kernel

![w:700](./figs/run-app.png)

---
## Characteristics of OS kernel

- **Concurrency**: multiple running programs in the computer system at the same time
- **Sharing**: "simultaneous" accesses to various shared resources that are mutually exclusive
- **Virtualization**: each program seems to "own" the entire computer
- **Asynchronization**: the completion time of a service is uncertain, sometimes the execution of the service may fail
- **Persistency**: read/store data from/to I/O devices (such as disk) to/from memory via file system
---
## Your understanding of OS kernel

### User/application requirements on OS?

---
## Your understanding of OS kernel

### User/application requirements on OS?
- Efficient -- Easy to use ?
- Powerful OS services -- Simple interface?
- Flexibility -- Security ?


---
## Why study this course?

- Understand how the software and hardware work inside the computer 
- Learn the infrastructures of software and hardware 
- Find and fix challenging bugs

<!-- 如果你花费大量时间来开发，维护并调试应用程序，你最终还是要知道大量操作系统的知识 -->
