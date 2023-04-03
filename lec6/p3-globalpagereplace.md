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
## Section 3 Global Page Replacement Algorithms

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023
<!--- refer: https://pages.cs.wisc.edu/~remzi/OSTEP/vm-complete.pdf --->
---

**Outline**

### 1. Definition of Global Page Replacement Algorithm
2. Working Set Page Replacement Algorithm
3. Page Fault Frequency Algorithm 
<!---Note this term is from NYU's Slide  https://cs.nyu.edu/~gottlieb/courses/2010s/2010-11-spring/os2250/lectures/lecture-10.html#local-global-replacement --->
---
#### Local Replacement Algorithm ignores memory access pattern differences of different processes
FIFO: Assume initial order is a->b->c
Number of physical pages: 3 -> Number of page faults: 9
![w:1100](figs/local-rp-1.png)

---
#### Local Replacement Algorithm ignores memory access pattern differences of different processes
FIFO page replacement algorithm: assume initial order a->b->c
Number of physical pages: 4 -> Number of page faults: 1
![w:1100](figs/local-rp-2.png)


---
#### How Global Replacement Algorithms work
- Ideas
   - **Allocate a variable number of physical pages for a process**
- Problems in Global Replacement Algorithms
   - Memory demand of a process varies at **different stages**
   - The **memory** allocated to the process should adapt
    at **different stages**
   - The global replacement algorithm should **determine the number of physical pages allocated to the process**

---
#### CPU Utilization with different number of running programs

![bg right:40% 100%](figs/cpu-usage-relation.png)
<!-- ![w:450](figs/cpu-usage-relation.png) -->
  - There is a mutual promotion and restriction relationship between CPU utilization and the number of programs running
    - When there are few running programs,  we can increase CPU utilization by increasing the number of running programs
    - Large number of running programs will increase memory access frequency and reduces the locality of memory access
    -Lower locality leads to higher page fault rate and lower CPU utilization


---

**Outline**

1. Definition of Global Page Replacement Algorithm
### 2. Working Set Page Replacement Algorithm
3. Page Fault Frequency Algorithm 

---
<style scoped>
{
  font-size: 28px
}
</style>
#### Working Set
The set of logical pages currently used by a process, it can be expressed as a binary function W(t, $\Delta$)
- Current execution time $t$
- Working-set window $\Delta$: A fixed-length page access time window
- working set window $\Delta$ size $\tau$
   - The length of the time window, represented by the **memory accesses** before the current time $t$
- working set W(t, $\Delta$)
   - The set of all pages accessed within the time window $\Delta$ before the current time $t$
- working set size | W(t, $\Delta$) |: Number of pages

---
#### Working set example of a process
Page access record:
W(t, $\Delta$) ={1,2,5,6,7} , working set window size $\tau=10$, current time $t=t_1$
![w:1100](figs/working-set-window-1.png)

---
#### Working set example of a process

Page access record:
W(t, $\Delta$) ={1,2,3,4,5,6,7} , working set window size $\tau=10$, current time $t=t_1$

![w:1100](figs/working-set-window-2.png)


---
#### Working set example of a process

Page access record:
W(t, $\Delta$) ={3,4}, working set window size $\tau=10$, current time $t=t_2$

![w:1100](figs/working-set-window-3.png)



---
#### Changes of Working Set
<style scoped>
{
  font-size: 27px
}
</style>
![w:600](figs/working-set-change.png)
- **After a process starts to execute**, its working set is gradually established by accessing new pages
- When the **locality area** of memory accesses is roughly stable, the working set size is also roughly stable
- When the locality area changes, the working set quickly expands and contracts until it transitions to the next stable value.


---
#### Resident Set
<style scoped>
{
  font-size: 28px
}
</style>
   The set of pages that are **actually present** in the memory of a process at the current time.
- Relationship between working set and resident set
   - The working set is an **inherent property** of a process during its execution.
   - The resident set is determined by the number of physical pages allocated to the process and the page replacement algorithm, which **depends on the system**
- Relationship between page fault rate and resident set
   - When the resident set $\supseteq$ working set, the page fault rate is relatively low.
   - When the working set changes dramatically (transition), the page fault rate is relatively high.
   - After the process resident set size reaches a certain number, the page fault rate will not decrease significantly


---
#### Working Set Page Replacement Algorithm
<style scoped>
{
  font-size: 28px
}
</style>
- Idea
    - Swap out pages **not in the working set**
- Working set window size $\tau$
    - The set of pages accessed in the previous $\tau$ memory accesses before the current time form the **working set**

- Implementation
   - Memory Access Record linkedlist: Maintains a list of accessed pages within the window
   - When a page is accessed, swap out pages not in the working set and update the Memory Access Record list
   - When a page fault occurs, swap in the page and update the list.


---

#### Example of Working Set Replacement Algorithm 

$\tau=4$

![w:1100](figs/ws-1.png)


---

#### Example of Working Set Replacement Algorithm 

$\tau=4$

![w:1100](figs/ws-2.png)



---

#### Example of Working Set Replacement Algorithm 

$\tau=4$

![w:1100](figs/ws-3.png)



---

#### Example of Working Set Replacement Algorithm 

$\tau=4$

![w:1100](figs/ws-4.png)


---

#### Example of Working Set Replacement Algorithm 

$\tau=4$

![w:1100](figs/ws-5.png)



---

#### Example of Working Set Replacement Algorithm 

$\tau=4$

![w:1100](figs/ws-6.png)



---

#### Example of Working Set Replacement Algorithm 

$\tau=4$

![w:1100](figs/ws-7.png)


---

#### Example of Working Set Replacement Algorithm 

$\tau=4$

![w:1100](figs/ws-8.png)



---

#### Example of Working Set Replacement Algorithm 

$\tau=4$

![w:1100](figs/ws-9.png)

---

**Outline**

1. Definition of Global Page Replacement Algorithm
2. Working Set Page Replacement Algorithm
### 3. Page Fault Frequency Algorithm 

---

#### Page-Fault-Frequency (Page Fault Rate)

**The number of page faults / the number of memory accesses** or the **reciprocal** of the average time interval between page faults.

- Factors Affecting Page Fault Rate
   - Page replacement algorithm
   - Number of physical pages allocated to the process
   - Page size
   - The coding method of a program


---
#### Page Fault Frequency Replacement Algorithm
<style scoped>
{
  font-size: 28px
}
</style>
![bg right:50% 100%](figs/page-fault-relation.png)

By adjusting the **resident set size**, the **page fault frequency** of each process keeps in a reasonable range.
- If the process's page fault frequency is too high, increase the resident set to allocate more physical pages.
- If the process's page fault frequency is too low, decrease the resident set to reduce its number of physical pages.

---
#### Page Fault Frequency Replacement Algorithm
- When accessing memory, **set** the reference bit flag
- When there is a page fault, **calculate** the **time interval** from the last page fault time $t_{last}$ to the current time $t_{current}$
   - If $t_{current} – t_{last}>T$ (tolerated page fault window), then **replace** all pages that have not been visited during the time interval $[t_{last} , t_{current} ]$
   - If $t_{current} – t_{last} \le T$, then **add** missing pages to the resident set

---

#### Example of Page Fault Frequency Algorithm

Assuming the window size is 2
![w:1100](figs/ppf-1.png)


---

#### Example of Page Fault Frequency Algorithm

Assuming the window size is 2
![w:1100](figs/ppf-2.png)



---

#### Example of Page Fault Frequency Algorithm

Assuming the window size is 2
![w:1100](figs/ppf-3.png)



---

#### Example of Page Fault Frequency Algorithm

Assuming the window size is 2
![w:1100](figs/ppf-4.png)



---

#### Example of Page Fault Frequency Algorithm

Assuming the window size is 2
![w:1100](figs/ppf-5.png)


---

#### Example of Page Fault Frequency Algorithm

Assuming the window size is 2
![w:1100](figs/ppf-6.png)


---

#### Example of Page Fault Frequency Algorithm

Assuming the window size is 2
![w:1100](figs/ppf-7.png)


---

#### Example of Page Fault Frequency Algorithm

Assuming the window size is 2
![w:1100](figs/ppf-8.png)



---

#### Example of Page Fault Frequency Algorithm

Assuming the window size is 2
![w:1100](figs/ppf-9.png)



---

#### Example of Page Fault Frequency Algorithm

Assuming the window size is 2
![w:1100](figs/ppf-a.png)


---
#### Thrashing
<style scoped>
{
  font-size: 28px
}
</style>
- Thrashing
   - **Physical pages** are not enough to contain all working sets
   - Lead to **a large number of page faults**, frequent page replacement
   - Lead to **slow execution of the process**

- Cause of thrashing
    - As the number of **processes** residing in memory increases, the number of physical pages allocated to each process decreases, and the page fault frequency keeps increasing
- The OS needs to balance the concurrency level and **page fault frequency**
    - Select an appropriate number of processes and the demanding number of physical pages for each process

---

### Course Lab 2

* Chapter 4: Address Space -> Chapter4 Exercises ->
     * [rCore](https://learningos.github.io/rCore-Tutorial-Guide-2022A/chapter4/7exercise.html)
     * [uCore](https://learningos.github.io/uCore-Tutorial-Guide-2022A/chapter4/7exercise.html)
* Lab tasks
     * Rewrite the kernel function for obtaining system time and process control block
     * Implement system calls for mapping and unmapping virtual memory
* Submission requirements
     * April 9, 2023

---

### Lecture 5 Summary of Virtual Memory

* Section 1 Concepts of Virtual Memory
     * Demanding Paging, Overlay, Swapping, Concept of Virtual Memory, Page Fault
* Section 2 Local Page Replacement Algorithms
     * Concepts of page replacement algorithm, OPT, FIFO, LRU, Clock, Improved Clock Page Replacement Algorithm, LFU, Belady Phenomenon
* Section 3 Global Page Replacement Algorithms
     * Global page replacement algorithm, Working Set Replacement Algorithm, Page Fault Frequency Algorithm
     
