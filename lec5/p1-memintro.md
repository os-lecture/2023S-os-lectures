---
noteId: "db462fe1d86511edb7d82f06d610f713"
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

# Lecture 5 Address Space - Physical Memory Management
## Section 1 Address Space
<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

<br>
<br>

Spring 2023

---
**Outline**

### 1. Storage Hierarchy of Computer
2. Address and Address Space
3. The Function of Virtual Memory

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Physical address and logical address

- Physical Address (PA): Used for addressing units at the level of memory chips, corresponding to the **address bus** connected to the processor and CPU
- Logical address (LA): Used to specify the address of an operand or an instruction when the **CPU executes machine instructions**. It is also the address used in user programming
- Linear Address (or Virtual Address): Similar to a logical address, it is also an unreal address
   - Logical Address refers to the address before CPU converts it to segmented memory management
   - Linear Address refers to the address before CPU converts it to paged memory management

---
#### Relationship between logical address and physical address

- Conversion from Logical Address to Physical Address
   - Logical Address -> Linear Address (Virtual Address) -> Physical Address

- The logical address is the same as the virtual address when there is no segment memory management
- The logical address, virtual address, and physical address are all the same when there is no page memory management

---
#### Storage hierarchy of computer

![w:800](figs/computer.png)



---

#### Multi-layer storage structure of computer
![w:950](figs/mem-layers.png)


---
#### Abstraction of memory resource by an OS
![w:850](figs/os-mem-mgr.png)



---

#### Memory management

- **Memory management method** in the OS
   - Relocation
   - Segmentation
   - Paging
   - Virtual memory/storage
- **The memory management of OS highly depends on the hardware**
   - Tightly coupled with computer's storage architecture
   - MMU (Memory Management Unit): the hardware that handles storage access requests of the CPU


---
**Outline**

1. Storage Hierarchy of Computers
### 2. Address and Address Space
3. The Function of Virtual Memory

---

#### Definition of address space


- Physical address space: the address space of physical memory
   - It starts from address $0$ and goes up to $MAX_{phy}$
- Virtual address space: the address space of virtual memory
   - It starts from address $0$ and goes up to $MAX_{virt}$
- Logical address space: the address space used by a program during execution
   - It starts from address $0$ and goes up to $MAX_{prog}$

The **perspectives of these three address spaces are different**

---

#### Generation of logical address 
![w:950](figs/create-logic-addr.png)



---
<style scoped>
{
  font-size: 30px
}
</style>

#### Timing of address generation

- At the compiling time
   - Assuming the starting address is **known**
   - If the starting address changes, the program must be recompiled
- At the loading time
   - If the starting address is **unknown** in compilation, the compiler should generate relocatable code
   - At the loading time, the position may not be fixed, and absolute (virtual) addresses are generated
- At the execution time
   - The code cannot be modified during execution
   - Requires **address translation (mapping) hardware** support


---

#### Address generation process
- CPU
   - ALU: needs the content in **logical address**
   - MMU: translates logical address to physical address
   - CPU control logic: sends **Physical Address** request to the bus
- Memory
   - Send the content of **Physical Address** to the CPU
   - Receives CPU data to the physical address
- OS
   - Build the mapping between logical address and physical address


---

#### Address checking
![bg w:850](figs/addr-check-exp.png)


---
**Outline**

1. Storage Hierarchy of Computers
2. Address and Address Space
### 3. The Function of Virtual Memory

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Cache of external storage

Virtual memory can be used as a cache for external storage

- **Frequently used data** is stored in physical memory
- **Infrequently used data** is stored in external storage
- Running programs **use virtual memory address**, no need to care about whether they are stored in physical memory or external storage

![bg right:50% 95%](figs/os-mem-mgr.png)

---
<style scoped>
{
  font-size: 25px
}
</style>

#### Simplify the compilation and loading of App

Each running program has its own **independent address space**, regardless of where the code and data are actually stored in physical memory. This simplifies:
- The process of compiling and linking executable programs
- The process of loading executable programs by the OS
- Sharing: dynamic link libraries, shared memory
- Memory allocation: physical memory is not contiguous, but appears contiguous in virtual memory
![bg right:50% 95%](figs/os-mem-mgr.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Protect data

Virtual memory can protect data
- The **independent address space** makes it easy to distinguish the memory of different processes
- The address translation mechanism can perform check for read/write/execute permissions
- The address translation mechanism can perform privilege level check
![bg right:50% 95%](figs/os-mem-mgr.png)
