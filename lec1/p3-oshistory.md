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
<!-- page_number: true -->
<!-- _class: lead -->

## Lecture 1 Operating System Overview

### Section 3 The Evolution History of OS 

<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

<br>
<br>

Spring 2023

---

## Single User System

Single User System (1945-1955)

- Manual wire/using tape for program input
- Machine cost is much greater than the labor cost
- **OS = loader + libraries**
- Problem: **Low utilization** of the expensive components

![bg right:40% width:520](./figs/history-single-user-system.png)

---

## Batch processing system

Batch processing system (1955-1965)

- Using **TAPE/DISK** for program input
- Machine cost is greater than labor cost
- OS = loader + **program controller + output processor**
- Evolution: Increased utilization than before

![bg right:35% 100%](./figs/history-batch-processing.png)

---

## Batch processing system

Batch processing system (1955-1965)

- Using **TAPE/DISK** for program input
- Machine cost is greater than labor cost
- OS = loader + **program controller + output processor**
- Evolution: Increased utilization than before

![bg right:35% 100%](./figs/history-batch-process-graph.png)

---

## Multiprogramming system

Multiprogramming system (1955-1980)

- multiple programs reside in **memory**
- Multiple programs take **CPU** in turn
- OS = loader + **program scheduler + memory management** + output management
- Evolution: Further increased utilization

![bg right:35% 100%](./figs/history-multiprogramming.png)

---

## Time Sharing System

Time Sharing System (1970-present)
- Multiple programs reside in memory
- Multiple programs use the CPU in a time-sharing way
- OS = loader + program scheduler + memory management + **interrupt handling** +...
- Evolution: high utilization, reduced interaction delay 

![bg right:35% 100%](./figs/history-timesharing.png)

---
## MULTICS

![](./figs/history-multics.png)

---
## MULTICS

-The project of MULTICS started in 1964, led by MIT, along with General Electric and Bell Labs.

-It is an influential early time-sharing OS built on GE 645 computer, and a multi-user and multi-task OS based on the concept of a single-level memory.

-Innovations: PL/I, time-sharing scheduling, virtual memory, segemented memory, shared memory multiprocessor

-Although it failed, it had significant influence on modern OSes, especially on Unix.

---
## UNIX

- Developed by Ken Thompson and Dennis Ritchie (AT&T's Bell Labs) in 1969. 

- Orginally in  assembly language, rewritten in C by Dennis in 1973.

- AT&T licensed Unix to outside parties in the late 1970s, bringing many Unix variants from vendors.

![bg right:40% 100%](./figs/unix-family.png)


---
## Linux

- Developed by Linus Torvalds and first released on 1991

- Open-source and free

- inspried by MINIX, a teaching OS kernel developed by Andrew S. Tanenbaum in the University of Helsinki

![bg right:45% 100%](./figs/linux-family.png)

---
## Personal computer

Personal computer (1981- )
- Single user
- **The computer cost drops** so that CPU utilization is no longer a key concern
- Focus on **user interface and multimedia supports**
- OS = loader + program scheduler + memory management + interrupt handling +...
- Evolution: **to the public**, more security issues

![bg right:20% 100%](./figs/history-pc.png)

---
## MacOS Family

![bg 55%](./figs/macos-family.png)

---
## MacOS Family

![bg 55%](./figs/macos-family-history.png)

---
## Windows family

![bg 70%](./figs/windows-family.png)

---
## Distributed System

Distributed System (1990- )
- Distributed multiple users
- Resource utilization is a key concern
- The focus is on the efficiency of network/storage/computation
- OS = distributed (loader + program/OS scheduler + memory management)
- Evolution: to the public, towards **Internet**, new challenges (unreliable/uncertain)

![bg right:30% 100%](./figs/history-ds.png)

---
## Android OS
- Cross-platform: support Java applications
- Runtime: Android virtual machine
- Application Framework: Simplified application development framework


![bg right 80%](./figs/android-system-architecture.png)

---
## AIoT operating system

AIoT system (2000- )
- Distributed multiple devices
- Utilization/availability is a key concern
- The focus is on the efficiency of network/storage/compute 
- OS = distributed (program/OS scheduler + memory management + security/updates)
- Evolution: towards device, towards network, new challenges (unreliable/big data)


![bg right:28% 100%](./figs/history-aiot.png)

---
## Fuchsia OS

![bg 65%](./figs/fuchsia-os-intro.png)