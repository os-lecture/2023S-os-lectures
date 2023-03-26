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

# Lecture 6 Virtual Memory Management
## Section 1. Concepts of Virtual Memory

<br>
<br>
<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023

---

**Outline**

### 1. The demand for Virtual Memory Technology
2. Memory Overlay
3. Memory Swapping
4. Basic Concepts of Virtual Memory
5. Page Fault

---

#### Background: Why we need Virtual Memory Technology
The growth rate of the program size is much faster than that of the memory capacity
![w:700](figs/game-mem.png)
Ideal Memory:  **large capacity**, **fast**, **cheap**, **Non-Volatile**


---
<style scoped>
{
  font-size: 28px
}
</style>

#### Basic idea of Virtual Memory
**Challenge**: The computer system often suffers from **lacking memory**
**Idea**: Use external storage
- Function Overlay
   - The application is **manually** swapped into and out from memory in units of **function/module**
-Program Swapping
   - The OS automatically swaps content into and out from memory in units of **programs**
- Virtual Memory
   - The OS automatically swaps content in and out of memory in units of **page**

Virtual Memory = Memory + External Storage

---
#### Address space
<!--The computer system often has insufficient memory space
- Module overlay (overlay)
   - The application **manually** saves the required commands and data in memory
- Task exchange (swapping)
   - The operating system **automatically** saves temporarily unexecutable programs to external memory
- virtual storage
   - Automatically load more and larger programs in units of **pages in limited memory
--->
The `Address Space` is the **abstraction** of virtual memory by the OS.
![w:800](figs/os-abstract-address-space.png)



---

**Outline**

1. The demand for Virtual Memory Technology
### 2. Memory Overlay
3. Memory Swapping
4. Basic Concepts of Virtual Memory
5. Page Fault

---

#### Memory Overlay
- Goal
   - Programmers **manually** manage memory to run **large programs** in **relatively small memory**
- Basic idea
   - **Functions or modules** executed at **different time periods** share a limited memory space


![bg right:48% 75%](figs/overlay-simple.png)

---
<style scoped>
{
  font-size: 32px
}
</style>

#### Fundamentals of overlay
   Overlay refers to dividing a program into some segments with relatively independent functions. Segments that are not required to be loaded into memory at the same time can form a group (named an overlay segment), which will **share** same area of main memory.
   - **Necessary** parts of (Frequently used) code and data will be resident in memory
   - **Optional** parts (not frequently used) are placed in other program modules, and only loaded into memory when **needed**
   - If there is **no caller & callee relationship** between two modules, they can share the same memory area

---

#### Example of Memory Overlay

![w:900](figs/overlay.png)
 


---

#### Shortages of Memory Overlay

- Increase **Programming Difficulty**
   - Programmers are required to divide functional modules and determine the overlay relationship between modules
   - Increase programming complexity;
- Increase **Execution Time**
   - Load overlay module from external storage
   - Sacrifice execution time to save space

  The overlay system of Turbo Pascal supports programmer-controlled overlay technology


---

**Outline**

1. The demand for Virtual Memory Technology
2. Memory Overlay
### 3. Memory Swapping
4. Basic Concepts of Virtual Memory
5. Page Fault

---
<style scoped>
{
  font-size: 32px
}
</style>

#### Memory Swapping 
- Basic idea
   - The OS automatically swaps contents into and out from of memory in units of **programs**
- Method
   - **Swap out**: Save the content of an executing program's address space to the external storage
   - **Swap in**: Load the content of a program's address space from the external storage into the memory

![bg right:30% 100%](figs/swapping.png)

---

#### Problems in Memory Swapping

- Swapping **time**: When to swap?
   - Swapping happens only  when memory space is already or possibly not enough
- Swap space **size**: Be able to store the copy of the memory image of all user processes 
- **Program Relocation**: Should it be placed in the same place when swapping happens?
   - Not necessary in the same place, but we need a mapping mechanism to address & execute of the program correctly 


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Comparison of Memory Overlay and Swapping
- Program Overlay
   - Occurs between **modules/functions** that are not on the same control flow during a certain period of time
   - In units of modules/functions
   - Programmers must provide the **logical overlay structure** between modules/functions manually
- Memory Swapping
   - Occurs between running **programs**
   - In units of running programs
   - No need for logical overlay structures between modules
 
---

**Outline**

1. The demand for Virtual Memory Technology
2. Memory Overlay
3. Memory Swapping
### 4. Basic Concepts of Virtual Memory
5. Page Fault

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Definition of Virtual Memory
- Definition
   - Virtual Memory = **Main Memory** + **External Storage**
- Ideas
   - The OS moves infrequently-used parts of the memory to the external storage **temporarily**, and loads the data needed by the processor from external storage into memory
- **Prerequisites**
   - Programs follow the principle of **locality**

![bg right:40% 95%](figs/virtual-memory.png)

---
<style scoped>
{
  font-size: 28px
}
</style>

#### Principle of Locality

Locality : During a **short period** of the program's execution process, the address and **operands** of the instruction executed are respectively limited to a small area
- **Temporal** locality: If at one point a particular memory location is visited, then it is likely that the same location will be visited again in the near future (This applies to both instructions and data).
- **Spatial** locality: If a particular memory location is visited at a particular time, then it is likely that nearby memory locations will be visited in the near future (This applies to both instructions and data).
- **Branch** locality: If we execute a **jump instruction** twice, the program is likely to jump to the same memory address.

Significance of locality: If most programs have significant locality, virtual memory can be realized and achieve satisfactory results
 


---
<style scoped>
{
  font-size: 28px
}
</style>

#### Ideas and Rules of Virtual Memory 
- Idea: Temporarily move some infrequently-used memory blocks to external storage
- Rule:
   - When **loading** the program: only load the **pages or segments required by the** current instruction execution into the memory
   - When the instruction or data **needed by the instruction execution is not in the memory** (page fault / segment fault): the processor notifies the OS to load the corresponding page or segment into the memory
   - OS saves the **temporarily unused** pages or segments  to external storage
- Implementation:
   - Paging
   - Segmentation


---
#### Basic Features of Virtual Memory
- Discontinuity
   - Physical memory allocation is discontiguous
   - The use of virtual address space is discontiguous
- Large user address space
   - The virtual memory provided to the user can be larger than the actual physical memory
- Partial exchange
   - Virtual memory only swaps some parts of the virtual address space into / out from memory
 

---
#### Underlying Support of Virtual Memory
- Hardware (MMU/TLB/PageTable)
   - Hardware **address translation** mechanism, hardware **exception** in paging or segmentation memory management
- Software (OS)
   - Create **page table or segment table** in memory
   - Manage **swap-in and swap-out** of pages or segments between main memory and external storage
 

---
<style scoped>
{
  font-size: 28px
}
</style>

#### Virtual Paging Management
On the basis of paging management, add **demand paging** and **page replacement**

- **Basic idea**
   - When a user program is loaded into memory, only a portion of its pages are loaded for program execution.
   - As the user program runs, if it needs code or data that is not in memory, it generates a **page fault exception** and send it to the OS.
   - When the OS handles a page fault exception, it loads the corresponding page from external storage into memory, allowing the user program to continue running.
   - When memory becomes scarce, the OS swaps out some pages from memory to external storage.


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Virtual Paging Memory Management
On the basis of paging management, add **demand paging** and **page replacement**
- Demand paging: The data will be transferred from external storage to memory when the processor needs to access it.
- Page replacement: Swap out infrequently-used pages and swap in frequently-used pages
- Page fault handling: **Software and hardware cooperation**
 
![bg right:40% 100%](figs/vm-work.png)


---

**Outline**

1. The demand for Virtual Memory Technology
2. Memory Overlay
3. Memory Swapping
4. Basic Concepts of Virtual Memory
### 5. Page Fault

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Workflow of page fault handling
1. The CPU issues a memory access request, then matches the physical address in the TLB according to its virtual address.  It will get a miss and **read the page table**;
2. Since the presence bit of the page table entry is 0, the CPU generates a **page fault**;
3. The OS **finds** the page content stored in the external storage;

![bg left:40% 100%](figs/page-fault-handler.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Workflow of page fault handling

4-1. If there is a free physical page frame, swap the page content **in the external memory into an idle physical page frame**;
4-2. If there is no free physical page frame, release/swap out a physical page frame to the external storage through the page replacement algorithm, and then swap the page content from the external storage into the previous page frame;

![bg left:40% 100%](figs/page-fault-handler.png)

---

#### Workflow of page fault handling

5. **Modify the page table entry**, establish the mapping from the virtual page to the physical page, and set presence bit to 1;
6. The OS returns to the application and asks the processor to **re-execute** the instruction that caused the page fault.

![bg left:40% 100%](figs/page-fault-handler.png)
* Where are unmapped pages kept? How to find this page? *

---
#### Where to store unmapped pages?
   - Swap space (disk/file)
     - Store unmapped pages in a special format
   - Files on disk(code or data)
   ![bg right:50% 100%](figs/page-fault-handler.png)
---
<style scoped>
{
  font-size: 30px
}
</style>

#### Swap Space of External Storage
Where to save the addresses of the pages stored in external storage?

- Swap space
     - Disk partition: typically sector address
     - Save the page address of external storage in the page table entry whose presence bit is 0
     

![bg right:50% 100%](figs/page-fault-handler.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Disk Files of External Storage 

Where to save the addresses of the pages stored in external storage?

- Files on disk (code or data)
   - The logical segment representation in the address space has a corresponding file position
     - e.g., `MemorySet::MapArea`
   - Code Segment: executable binaries
   - Dynamically loaded program segments of shared libraries: Library files that are dynamically called 

![bg right:40% 100%](figs/page-fault-handler.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Performance of Virtual Memory

Effective memory access time(EAT)
- EAT = Memory access time $*$ (1-p) + Page fault handling time
- Page fault handling time = Disk access time*p(1+q)
   - p: page fault rate;
   - q: writeback probability
* Example
   - Memory access time: 10 ns; Disk access time: 5 ms
   - EAT = 10(1–p) + 5,000,000p(1+q)

---

### Summary


1. The demand for Virtual Memory Technology
2. Memory Overlay
3. Memory Swapping
4. Basic Concepts of Virtual Memory
5. Page Fault
