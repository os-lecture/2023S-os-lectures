---
noteId: "daa02be0d86511edb7d82f06d610f713"
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
<!-- page_number: true -->
<!-- _class: lead -->

## Lecture 1 Operating System Overview

### Section 4 Architecture of OS

<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

<br>
<br>

Spring 2024

---
## Simple Architecture
MS-DOS: Application and OS mixed together (1981–1994)
- **Not split into modules**
- Written in assembly
- No security

![bg right 100%](./figs/msdos.png)


---
## Monolithic and Hierarchical Architecture
Divide the Monolithic OS into **multi-layers** (levels)
- Each layer built on top of the lower
- The bottom layer is hardware drivers
- The top layer is user interface
- Each layer only uses the functions and services of the lower layer

![bg right:38% 100%](./figs/multi-level-os-arch.png)


---
## Micro-kernel
- **Move kernel functions to user space as much as possible**
- User modules uses message passing for communication
- **Pros:** flexible/safe...
- **Cons:** performance
- LPC: Local Procedure Call
- HAL: Hardware Abstraction Layer 

![bg right:40% 100%](./figs/microkernel-arch.png)

---
## Exokernel
- The kernel allocates physical resources to applications and each application decides how to deal with the resources
- Programs link to the OS library (libOS) that implements the  abstractions of OS
- **Separation of protection and control**
- Distributed Shared Memory (DSM)

![bg right:40% 100%](./figs/exokernel-arch.png)


---
## Virtual Machine
- The virtual machine manager converts a single machine interface into many virtual machines
- Each virtual machine seems a copy of the original computer system and can execute all processor instructions

![bg right:45% 100%](./figs/vmm-arch.png)

---
## Virtual Machine 


![bg 65%](./figs/vmm-arch-view2.png)

---
## OS Abstraction + Architecture

![bg 75%](./figs/os-env.png)
