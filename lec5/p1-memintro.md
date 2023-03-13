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

# Lecture 5 Physical memory management
## Section 1 Address Space
<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

<br>
<br>

Spring 2023

---
**Outline**

### 1. Storage Hierarchy of Computers
2. Addresses and Address Space
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
   - Logical Address refers to the address before CPU converts it to segment memory management
   - Linear Address refers to the address before CPU converts it to page memory management

---
#### Relationship between Logical Address and Physical Address

- Conversion from Logical Address to Physical Address
   - Logical Address -> Linear Address (Virtual Address) -> Physical Address

- The logical address is the same as the virtual address when there is no segment memory management
- The logical address, virtual address, and physical address are all the same when there is no page memory management

---
#### Storage Hierarchy of Computers

![w:800](figs/computer.png)



---

#### Multi-layer Storage Structure of Computers
![w:950](figs/mem-layers.png)


---
#### Abstraction of Memory Resources by an OS
![w:850](figs/os-mem-mgr.png)



---

#### Memory Management

- **Memory management method** in the OS
   - Relocation
   - Segmentation
   - Paging
   - Virtual memory/storage
- **The memory management of OS is highly dependent on the hardware**
   - Tightly coupled with computer's storage architecture
   - MMU (Memory Management Unit): hardware that handles storage access requests from the CPU


---
**Outline**

1. Storage Hierarchy of Computers
### 2. Addresses and Address Space
3. The Function of Virtual Memory

---

#### Definition of Address Space


- Physical address space: the address space of physical memory
   - It starts from address $0$ and goes up to $MAX_{phy}$
- Virtual address space: the address space of virtual memory
   - It starts from address $0$ and goes up to $MAX_{virt}$
- Logical address space: the address space used by a program during execution
   - It starts from address $0$ and goes up to $MAX_{prog}$

The **perspectives of these three address spaces are different**

---

#### Generation of Logical Address 
![w:950](figs/create-logic-addr.png)



---
<style scoped>
{
  font-size: 30px
}
</style>

#### Timing of Address Generation

- At compile time
   - Assuming the starting address is **known**
   - If the starting address changes, the program must be recompiled
- At load time
   - If the starting address is **unknown** during compilation, the compiler generates relocatable code
   - At load time, the position may not be fixed, and absolute (virtual) addresses are generated
- At execution time
   - The code cannot be modified at execution time
   - Requires **address translation (mapping) hardware** support


---

#### Address Generation Process
- CPU
   - ALU: memory content that requires **logical address**
   - MMU: performs the **conversion** between logical and physical addresses
   - CPU control logic: sends **Physical Address** request to the bus
- Memory
   - Sendd the content of **Physical Address** to the CPU
   - Receives CPU data to the physical address
- OS
   - Establishes the mapping between logical address LA and physical address PA


---

#### Address Checking
![bg w:850](figs/addr-check-exp.png)


---
**Outline**

1. Storage Hierarchy of Computers
2. Addresses and Address Space
### 3. The Function of Virtual Memory

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Cache of External Storage

Virtual memory can be used as a cache for external storage

- **Frequently used data** is stored in physical memory
- **Infrequently used data** is stored in external storage
- Running programs **use virtual memory address** without needing to be concerned with whether they are stored in physical memory or external storage

![bg right:50% 95%](figs/os-mem-mgr.png)

---
<style scoped>
{
  font-size: 25px
}
</style>

#### Simplify Application Compilation and Loading

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

#### Protect Data

Virtual memory can protect data
- The **independent address space** makes it easy to distinguish the memory of different processes
- The address translation mechanism can perform checks for read/write/execute permissions
- The address translation mechanism can perform privilege level checks
![bg right:50% 95%](figs/os-mem-mgr.png)