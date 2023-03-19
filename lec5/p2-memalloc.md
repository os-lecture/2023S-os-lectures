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

# Lecture 5 Physical Memory Management
## Section 2 Memory Allocation
<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

<br>
<br>

Spring 2023

---
**Outline**

### 1. Memory Allocation
2. Contiguous Memory Allocation
3. Discontiguous Memory Allocation

---
<style scoped>
{
  font-size: 28px
}
</style>

#### Memory Allocation Methods
According to the characteristics of the running application's data, memory occupied by the application will be divided into multiple **Segments** 
* Memory Allocation Methods
   - Static Memory Allocation
   - Dynamic Memory Allocation
       - Contiguous Memory Allocation
       - Discontiguous Memory Allocation
* Purpose of Memory Management
   - Allow applications to use the limited memory conveniently/flexibly/efficiently.

![bg right:40% 140%](figs/app-mem-layout.png)

---

#### Interfaces of Dynamic Memory Allocation

![w:850](figs/malloc-overview.png)

---

#### Static Memory Allocation

Static memory allocation refers to memory allocation during **compile time**
  - Includes global, static variables and code
  - Located in global/static data segment, constant data segment, code segment

![bg right:50% 140%](figs/app-mem-layout.png)

---

#### Dynamic Memory Allocation
Dynamic memory allocation refers to memory allocation at **runtime**. 
- Stack
   - local variables
- Heap
   - Memory allocated by `malloc()` function
   - Memory allocated by `free()` function

![bg right:40% 140%](figs/app-mem-layout.png)

---
#### Reasons for Using Dynamic Memory Allocation
**Unable to determine** the amount of memory required by the program **in advance**.
- Often the size of certain data structures is not known until the program runs
- Hardcoding the data size would be a nightmare in developing large softwares

![bg right:40% 140%](figs/app-mem-layout.png)


---
#### Classification of Dynamic Memory Allocation Methods

- **Explicit Allocation**
   - Applications are required to free any allocated blocks explicitly
- **Implicit Allocation**
   - The compiler/runtime library automatically frees unused allocated blocks
   - Implicit Allocator is called "Garbage Collector", like in Java

![bg right:40% 140%](figs/app-mem-layout.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Memory Allocation of Heap and Stack

Allocation Method: Dynamic Memory Allocation
- The stack is managed by the **compiler**: Implicitly Allocation
- Heap allocation and release is managed by the **programmer**: Explicit Allocation

Allocation Size
- The stack is a data structure that grows from high addresses to low addresses. The stack is a contiguous memory space. The memory allocated from the stack is **small**, and the size is determined during compilation;
- The heap is a data structure that grows from low addresses to high addresses. The heap includes some discontiguous memory regions. The memory allocation can be more flexible and **larger**.


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Dynamic Memory Allocation function `malloc()`

- `malloc()` function: `void * malloc (size_ t size);`
   - Apply for a piece of contiguous heap memory of `size`
   - The function returns a pointer, pointing to the start address of the newly allocated memory
   - If the memory application fails, return a null pointer(the return value is NULL)

- The allocation and release of dynamic memory must be **used in pairs**
   - If `malloc()` is more than `free()`, it will cause **memory leak**
   - If `malloc()` is less than `free()`, it will cause **double free**, destroy memory, and crash the program
---

#### Dynamic Memory Collection function `free()`
  
- `free()` function: ``void free (void *ptr)``
   - Release the heap memory space of the pointer
   - Unable to release the memory space on the stack
   - `free()` should be paired with `malloc()`
---
**Outline**

1. Memory Allocation
### 2. Contiguous Memory Allocation
- Dynamic Partition Allocation
- Buddy System
3. Discontiguous Memory Allocation

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Contiguous Memory Allocation

**Contiguous Memory Allocation** means to allocate a piece of **contiguous** memory area **not smaller than the given size** to the application
- **Memory Fragment**: Free memory that cannot be utilized
   - **External Fragment**: Unused memory between allocation units
   - **Internal Fragment**: Unused memory inside the allocation unit

![bg right:40% 95%](figs/malloc-overview.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Dynamic Partition Allocation

Dynamic partition allocation refers to allocating a process-specific partition (memory block) with a variable size when the program is loaded or stores data during runtime.
- The address of the partitions is contiguous
- **Data structures** that need to be maintained by the user library/OS
   - Allocated partitions: partitions allocated to applications
   - Empty-blocks


![bg right:35% 100%](figs/malloc-overview.png)


---
#### Problems in the design of Dynamic Partition Allocation
- **Free block organization**: How to record and orginize free blocks?
- **Placement**: How to choose a suitable free block to allocate?
- **Split**: How to handle the remaining part of an unallocated free block?
- **Merge**: what to do with a block that has just been freed?

![bg right:42% 100%](figs/malloc-overview.png)

---
#### Dynamic Partition Allocation Strategy
   - First-fit
   - Best-fit
   - Worst-fit


![bg right:42% 100%](figs/malloc-overview.png)

---
#### First Fit Allocation Strategy
- Advantages: simple, **large free partitions** in high address space
- Disadvantages: **External fragment**, very slow when allocating large chunks
- Example: allocate 400 bytes, use the first free block

![bg right:40% 80%](figs/firstfit.png)


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Best Fit Allocation Strategy
**When allocating a partition of size n bytes**, look for and use the smallest free partition that is not less than n.
- **When releasing a partition**, check whether it can be merged with adjacent free partitions

- Example: allocate 400 bytes, use the 3rd free block (smallest)
![bg right:40% 80%](figs/bestfit.png)



---
#### Best Fit Allocation Strategy
- Advantages: Works well when most applications allocate **small** sizes
- Disadvantages: **External Fragments**, the release of partitions is slow, and easy to generate many useless small fragments

![bg right:40% 80%](figs/bestfit.png)

---
#### Worst Fit Allocation Strategy
- **When allocating a partition of size n bytes**, look for and use the **largest** free partition that is not less than n.
- **When releasing a partition**, check whether it can be **merged** with adjacent free partitions**

- Example: allocate 400 bytes, use the 2nd free block (largest)
![bg right:40% 80%](figs/worstfit.png)

---

#### Worst Fit Allocation Strategy

- Advantages: Works best with most **medium-sized** allocations
- Disadvantages: **External fragment**, **slow to release partitions**, easy to destroy large free partitions

![bg right:40% 80%](figs/worstfit.png)




---
**Outline**
1. Memory Allocation
2. Contiguous Memory Allocation
- Dynamic Partition Allocation
### Buddy System
3. Discontiguous Memory Allocation

---

#### Background of Buddy System

- Observation & Analysis
   - The basic allocation strategy is simple and general, but the performance is poor and there are many external fragments
   - **Characteristics** of kernel and application's Memory Allocation Requests
     - The kernel often allocates and frees $2^U$ memory blocks of contiguous 4KB sizes
     - Needs to allocate and release **quickly** without creating **external fragment**
- Requires new contiguous memory allocation strategy

---

#### How Buddy System works


![bg w:850](figs/buddy-system-overview.png)

---
#### Partition size
- The size of the allocatable partition $2^U$
- The size of the partition to be allocated is $2^{(U-1)} < s ≤ 2^U$
   - allocate the entire block to the application;
- The size of the partition to be allocated is $s ≤2^{(i－1)}$
   - Divide the current free partition of size $2^i$ **into two free partitions** of size $2^{(i－1)}$
   - **Repeat the division** process until $2^{(i-1)} < s ≤ 2^i$, allocate a free partition

<!--
![bg right:35% 100%](figs/buddy-system-overview.png)
-->

---
#### Allocation Process
- Data structure
   - Free blocks are organized into two-dimensional arrays by size and start addresses
   - Initial state: There is only one free block of size $2^U$
- Allocation process
   - Find the smallest available block in the free-block list from small to large
   - If the free block is too large, divide the available free block into two same subblocks until a suitable available free block is obtained

<!--
![bg right:40% 100%](figs/buddy-system-overview.png)
-->

---
#### Release process

- release process
   - Put the block into the free-block array
   - Merge some suitable free blocks
- Merge conditions
   - Same size : $2^i$
   - Adjacent addresses 
   - The starting address of the low-address free block is a multiple of $2^{(i+1)}$

<!--
![bg right:50% 100%](figs/buddy-system-overview.png)

http://en.wikipedia.org/wiki/Buddy_memory_allocation -->

---

#### Buddy System workflow example
![w:1100](figs/buddy-system-1.png)


---
#### Buddy System workflow example
![w:1100](figs/buddy-system-2.png)


---
#### Buddy System workflow example
![w:1100](figs/buddy-system-3.png)


---
#### Buddy System workflow example
![w:1100](figs/buddy-system-4.png)


---
#### Buddy System workflow example
![w:1100](figs/buddy-system-5.png)

---
#### Reference implementation of Buddy System

* [buddy_system_allocator](https://crates.io/crates/buddy_system_allocator): Buddy system algorithm written in Rust
* [buddy-system-in-ucore-test](https://github.com/ucore-test/buddy-system-in-ucore-test#buddy-system-in-ucore-test): Interface for calling Buddy System in C

---
**Outline**
1. Memory Allocation
2. Contiguous Memory Allocation
### 3. Discontiguous Memory Allocation
- The concept of Discontiguous Memory Allocation
- Memory Management: Paging 
- Memory Allocation Example

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Background for Discontiguous Memory Allocation: Fragment
- Through the page table, the kernel can **convert multiple discontiguous physical pages into multiple contiguous virtual pages**
- Provide applications and the kernel with contiguous virtual memory blocks, which can easily solve the fragment problem of **memory allocation**


![bg right:40% 100%](figs/os-mem-mgr.png)


---
<style scoped>
{
  font-size: 28px
}
</style>

#### Background for Discontiguous Memory Allocation: large memory allocation
- When creating a process, we need to allocate **a relatively large memory space** for normal execution
- When the program is running, it will dynamically apply for and release a relatively large memory space
    - Typically make the request through the user library
    - Reduce the number of system calls
    - Apply for memory of $2^U$MB size (eg: 64MB) at one time

![bg right:40% 100%](figs/malloc-detail.png)

---

#### Design goals for Discontiguous Memory Allocation

Improve memory utilization efficiency and management flexibility
  - Allows a program to use a **discontiguous** physical address space
  - Allow **sharing** code and data
  - Support **dynamic** loading and linking


![bg right:40% 100%](figs/malloc-detail.png)

---
<style scoped>
{
  font-size: 27px
}
</style>

#### Problems in Discontiguous Allocation
- **Address translation** from virtual address to physical address
     - Software implementation (flexible, but expensive)
     - Hardware implementation (enough, and low overhead)
- **Hardware-assisted** mechanism for Discontiguous Allocation
   - How to choose the memory block size in Discontiguous Allocation
     - Segmentation
     - Paging

![bg right:40% 100%](figs/malloc-detail.png)

---

#### Memory Management: Segmentation

The segment address space of the running program consists of multiple segments
- Main code segment, submodule code segment, public library code segment, stack segment, heap data...

![bg right:40% 100%](figs/seg-addr-space.png)

---

#### Segment table

- located in memory
- Managed by the kernel
- Correspond to tasks/processes
![bg right:65% 100%](figs/seg-hw-os.png)


---

**Outline**

1. Memory Allocation
2. Contiguous Memory Allocation
3. Discontiguous Memory Allocation
- The concept of Discontiguous Memory Allocation
### Memory Management: Paging 
- Memory Allocation Example

---
<style scoped>
{
  font-size: 32px
}
</style>

#### Physical pages and Virtual pages
- Physical pages (Page Frame)
   - Divide the physical address space into basic allocation units of the same size (2^n)
- Virtual page (Page)
   - Divide the virtual address space into basic allocation units of the same size
   - **The basic allocation unit size of physical page and virtual page is the same**
- Mapping from virtual pages to physical pages
   - Address translation from virtual address to physical address
   - Hardware mechanism: page table/MMU/TLB

---

#### Page table
<style scoped>
{
  font-size: 30px
}
</style>
- located in memory
- Managed by the kernel
- Correspond to tasks/processes
![bg right:65% 80%](figs/page-overview.png)


---

#### Performance Challenges faced by Paging

- Performace of Memory Access
   - Accessing a memory location requires 2 memory accesses
     - First access: Get page table entry
     - Second access: Access data
- Page Table Size
   - Page tables can be very large
   - In a 64-bit computer system, if the size of each page is 1024 bytes, what is the size of a page table?



---

#### Methods to improve the paging performance

- Caching
- Indirect access
![w:900](figs/tlb-cache.png)

---

#### Multi-level Page Table

![bg w:1000](figs/multi-level-page.png)


---

#### Address Translation for multi-level page tables

![w:1000](figs/two-level-page-example.png)


---

#### Reverse Page Table

Find the corresponding physical page number in the page table entry based on the Hash value of task/process ids and virtual page numbers
- There may be *conflicts* between hash values
- Page table entries include Protection bits, Modified bits, Referenced bits, and Present bits
<!-- see https://www.geeksforgeeks.org/page-table-entries-in-page-table/ -->
---

#### Reverse Page Table

![w:1100](figs/revert-page.png)

---

#### Hash Conflicts of Reverse Page Table 
![w:1100](figs/revert-page-hash-1.png)


---

#### Hash Conflicts of Reverse Page Table
![w:1100](figs/revert-page-hash-2.png)


---

#### Hash Conflicts of Reverse Page Table
![w:1000](figs/revert-page-hash-3.png)



---

#### Memory Management:  Segmentation with Paging
- Segmentation has advantages in memory protection, and paging has advantages in memory utilization and optimizing the process of swapping data to lower-level storage(like disk).
- Can we combine Segmentation with Paging?


---

#### Memory Management:  Segmentation with Paging

![w:1000](figs/seg-page.png)

<!-- 2017ppt -->

---
**Outline**

1. Memory Allocation
2. Contiguous Memory Allocation
3. Discontiguous Memory Allocation
- The concept of Discontiguous Memory Allocation
- Memory Management: Paging 
### Memory Allocation Example

---

#### An example of an app calling `malloc`
```C
#include <stdlib.h>
int main(){
int *ptr;
ptr = malloc(15 * sizeof(*ptr)); /* a block of 15 integers */
     if (ptr != NULL) {
       *(ptr + 5) = 480; /* assign 480 to sixth integer */
       printf("Value of the 6th integer is %d",*(ptr + 5));
     }
}
```


---

#### Address space of an app
![bg right:50% 120%](figs/app-mem-layout.png)


---
#### Program Loading

Step 1: OS loads the program to run
![w:1000](figs/malloc-detail-ex-1.png)

---

#### Malloc function call

Step 2: The program makes a `malloc` function call, and the Lib library **has free blocks**
![w:700](figs/malloc-detail-ex-2.png)


---
#### The kernel allocates memory
Step 2: The program makes a `malloc` function call, and the Lib library **has free blocks**
![w:700](figs/malloc-detail-ex-3.png)
