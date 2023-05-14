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

# Lecture 12 Synchronization and mutual exclusion

## Section 5 Deadlock



---
<style scoped>
{
  font-size: 30px
}
</style>

### Deadlock problem
![w:700](fig/../figs/deadlock-bridge.png)
- Bridges are one-way traffic only
- Each part of the bridge can be considered as a resource
- Possible deadlock
   - Oncoming vehicles meet on a bridge
   - Solution: Vehicles in one direction go backwards (resource preemption and rollback)


---
### Deadlock problem
![w:700](fig/../figs/deadlock-bridge.png)
- Bridges are one-way traffic only
- Each part of the bridge can be considered as a resource
- Starvation may occur
   - Vehicles in the other direction cannot pass the bridge due to continuous traffic in one direction



---
### Deadlock problem
Due to competition for resources or communication dependencies, two or more threads may become deadlocked during execution, forever waiting for events triggered by other processes.

```
Thread 1: Thread 2:
lock(L1); lock(L2);
lock(L2); lock(L1);
```
![bg right:40% 80%](figs/deadlock-thread.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### Deadlock Problem -- Resources
- Resource type $R_1, R_2, . . .,R_m$
    - CPU execution time, memory space, I/O devices, etc.
- Each type of resource $R_i$ has $W_i$ instances
- The process of thread/process accessing resources
    - Request: apply for free resources
    - Use: Occupy resources
    - Release: resource status changes from occupied to idle
![bg right:40% 100%](figs/deadlock-resource.png)


---
<style scoped>
{
  font-size: 26px
}
</style>

### Deadlock Problem -- Resources
**Resource classification**
- Reusable Resource
    - Only one thread/process can use resources at any time
    - After the resource is freed, it can be reused by other threads/processes
    - Examples of reusable resources
       - Hardware: processor, memory, devices, etc.
       - Software: files, databases, semaphores, etc.
    - Deadlock is possible: each process occupies some resources and requests other resources

![bg right:35% 100%](figs/deadlock-resource.png)


---
### Deadlock Problem -- Resources
**Resource classification**
- Consumable resource
    - Resources can be destroyed
    - Example of consumable resources
       - Interrupts, signals, messages, etc. in the I/O buffer
    - There may be a deadlock: the processes wait for each other to receive messages from each other
![bg right:35% 100%](figs/deadlock-resource.png)


---
<style scoped>
{
  font-size: 26px
}
</style>

### Deadlock problem -- Resource Allocation Diagram
A directed graph describing the allocation and occupancy relationship between resources and processes
- Vertices: Processes in the system
    - $P = \{ P_1, P_2, …, P_n \}$
- Vertices: resources in the system
    - $R = \{R_1, R_2, …, P_m\}$
- Edge: resource request
    - Process $P_i$ requests resource $R_j: P_i \rightarrow R_j$
- Edge: resource allocation
    - Resource $R_j$ has been allocated to process $P_i: R_j \rightarrow P_i$
![bg right:30% 100%](figs/deadlock-resource.png)


---
### Deadlock problem -- Resource Allocation Diagram
![w:900](figs/deadlock-resource-problem.png)
Is there a deadlock?


---
<style scoped>
{
  font-size: 30px
}
</style>

### Deadlock problem -- Necessary Conditions
- Mutex
   - Only one thread/thread can use one resource instance at any time
- Hold and wait
   - In/thread holds at least one resource and is waiting to acquire resources held by other processes
- Non-preemptive
   - Resources can only be released voluntarily after the process uses them
- Cyclic wait
    - There is a set of waiting processes $\{P_0, P_1, ..., P_N\}$
    - Processes form a ring waiting for resources


---
<style scoped>
{
  font-size: 30px
}
</style>

### Deadlock problem -- solution
- Deadlock Prevention
    - Make sure the system never enters a deadlock state
- Deadlock Avoidance
    - Judgment before use, only allow processes without deadlock to request resources
- Deadlock Detection & Recovery
    - Recovery after detection of a runtime system entering a deadlock state
- Application process handle Deadlocks 
    - Normally the operating system ignores deadlocks
       - the practice of most operating systems (including UNIX)

---
<style scoped>
{
  font-size: 30px
}
</style>

### Deadlock problem - solution - prevention
prevention adopta strategy to limit the resource requests of concurrent processes, or to destroy the necessary conditions for deadlock.
- Destroy the "mutex"
    - Encapsulate mutually exclusive shared resources into simultaneously accessible ones, such as transforming printers into shared devices using SPOOLing technology.
    - Disadvantage: But many times the mutex cannot be broken.
- Destroy "hold and wait"
    - Allocate only when all required resources are available at the same time
    <!-- Only allow the process to request all needed resources at once when it starts executing
       - When a process requests a resource, it is required that it does not hold any other resources -->
    - Disadvantage: low resource utilization



---
<style scoped>
{
  font-size: 25px
}
</style>

### Deadlock problem - solution - prevention
<!--Prevention is to adopt a certain strategy to limit the resource requests of concurrent processes, or to prevent the system from meeting the necessary conditions for deadlock at any time. -->
prevention adopta strategy to limit the resource requests of concurrent processes, or to destroy the necessary conditions for deadlock.
- Breaking "non-preemptive"
    - If the process requests resources that cannot be allocated immediately, the occupied resources are released
    - When the requested resources are occupied by other processes, the OS assists in depriving them
    - Disadvantage: Repeated application and release of resources will increase system overhead and reduce system throughput.
- Destroyed "loop wait"
    - Sort resources, requiring processes to request resources in order
    - Disadvantages: resources must be applied in the specified order, and user programming is troublesome
    - Disadvantages: Difficult to support resource changes (e.g. new resources)
 
 
<!---
### Deadlock problem - solution - prevention
![w:900](figs/deadlock.png)
-->


---

### Deadlock problem - solution -- avoid
Using additional prior knowledge to determine whether a deadlock will occur when allocating resources, and only allocating resources when there is no deadlock.
- Ask processes to declare the maximum number of resources they need
- Limit the number of resources provided and allocated to ensure that the maximum needs of the process are met
- Dynamically check the resource allocation status to ensure that there will be no circular waiting

 
---
<style scoped>
{
  font-size: 30px
}
</style>

### Deadlock problem - solution -- avoid
During resource allocation, the system is in a safe state
- For all occupied processes, there exists a safe execution sequence $<P_1, P_2, ..., P_N>$
- $P_i$ requested resources $\le$ currently available resources $+$ all $P_j$ holding resources, where $j<i$
- If the resource request of $P_i$ cannot be allocated immediately, then $P_i$ waits for all $P_j (j<i)$ to complete
- After $P_i$ is completed, $P_{i+1}$ can get the required resources, execute and release the allocated resources
- Finally, all  $P_{i}$ in the entire sequence can get the required resources


---
### Deadlock problem - solution -- avoid
Relationship between safe state and deadlock
- The system is in a safe state, there must be no deadlocks
- The system is in an unsafe state and deadlocks may occur
    - To avoid deadlock is to ensure that the system does not enter an unsafe state


![bg right:30% 100%](figs/deadlock-safe.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### Deadlock problem - solution -- avoid
Banker's Algorithm -- Overview
- The banker's algorithm is an algorithm to avoid deadlocks. Based on the bank loan allocation strategy, judge and ensure that the system is in a safe state
    - When the customer applies for the loan for the first time, declare the maximum amount of funds required, and return it in time when all loan requirements are met and the project is completed
    - The banker tries to meet the customer's needs when the customer's loan amount does not exceed the maximum value the bank has

Banker $\leftrightarrow$ operating system; fund $\leftrightarrow$ resource; client $\leftrightarrow$ line/process

---
<style scoped>
{
  font-size: 30px
}
</style>

### Deadlock problem - solution -- avoid
Banker's Algorithm (Banker's Algorithm) - data structure
![w:800](figs/deadlock-banker-data.png)


---
<style scoped>
{
  font-size: 28px
}
</style>

### Deadlock problem - solution -- avoid
Banker's Algorithm (Banker's Algorithm) -- a routine for judging the security state
![w:1000](figs/deadlock-banker-code.png)


---
### Deadlock problem - solution -- avoid
Banker's Algorithm (Banker's Algorithm) - complete algorithm
![w:700](figs/deadlock-banker-algorithm.png)


---
### Deadlock problem - solution -- avoid
Banker's Algorithm -- Example 1
![w:1000](figs/deadlock-banker-ex1.png)

---
### Deadlock problem - solution -- avoid
Banker's Algorithm -- Example 1
![w:1000](figs/deadlock-banker-ex2.png)

---
### Deadlock problem - solution -- avoid
Banker's Algorithm -- Example 1
![w:1000](figs/deadlock-banker-ex3.png)


---
### Deadlock problem - solution -- avoid
Banker's Algorithm -- Example 1
![w:1000](figs/deadlock-banker-ex4.png)

---
### Deadlock problem - solution -- avoid
Banker's Algorithm -- Example 2
![w:1000](figs/deadlock-banker-ex5.png)

---
### Deadlock problem - solution -- avoid
Banker's Algorithm -- Example 2
![w:1000](figs/deadlock-banker-ex6.png)


---
### Deadlock problem - solution -- detection
- Allow the system to enter a deadlock state
- Maintain the resource allocation diagram of the system
- Periodically call the deadlock detection algorithm to search for deadlocks in the graph
- When a deadlock occurs, use the deadlock recovery mechanism to recover
![bg right:30% 100%](figs/deadlock-check.png)


---
### Deadlock problem - solution -- detection
Deadlock Detection Algorithms: Data Structures
- Available: a vector of length m: the number of available resources of each type
- Allocation: an n×m matrix: the number of resources of each type currently allocated to each process
    - Process $P_i$ owns $Allocation[i, j]$ instances of resource $R_j$


---
### Deadlock problem - solution -- detection
Deadlock Detection Algorithm: The Complete Algorithm
![w:1000](figs/deadlock-check-algorithm.png)


---
### Deadlock problem - solution -- detection
Deadlock detection algorithm: -- Example 1
![w:800](figs/deadlock-check-ex1.png)


---
### Deadlock problem - solution -- detection
Deadlock detection algorithm: -- Example 1
![w:800](figs/deadlock-check-ex2.png)


---
### Deadlock problem - solution -- detection
Deadlock detection algorithm: -- Example 1
![w:800](figs/deadlock-check-ex3.png)


---
### Deadlock problem - solution -- detection
Deadlock detection algorithm: -- Example 1
![w:800](figs/deadlock-check-ex4.png)


---
### Deadlock problem - solution -- detection
Deadlock detection algorithm: -- Example 1
![w:800](figs/deadlock-check-ex5.png)


---
### Deadlock problem - solution -- detection
Deadlock detection algorithm: -- Example 1
![w:800](figs/deadlock-check-ex6.png)
The sequence $<T_0, T_2, T_1, T_3, T_4>$ can satisfy Finish[i] = true for all i


---
<style scoped>
{
  font-size: 30px
}
</style>

### Deadlock problem - solution -- detection
Deadlock detection algorithm: -- Example 2
![w:700](figs/deadlock-check-ex7.png)
The resources occupied by thread $T_0$ can be reclaimed, but the resources are not enough to complete other thread requests
Threads $T_1, T_2, T_3, T_4$ form a deadlock


---
### Deadlock problem - solution -- detection
Use deadlock detection algorithm

- Deadlock detection time and cycle selection basis
    - How often deadlocks are likely to occur
    - How many threads/threads need to be rolled back
- Resource graphs may have multiple loops
    - Difficulty distinguishing the critical entry/thread that "caused" the deadlock

When a deadlock is detected, what should be done?

---
<style scoped>
{
  font-size: 30px
}
</style>

### Deadlock problem - solution -- recovery -- process termination

- Terminate all deadlocked processes
- Terminate one process at a time until the deadlock is resolved
- Reference factors for the order in which processes are terminated:
    - The priority of the process
    - How long the process has been running and how long it still needs to run
    - The process is already using resources
    - Resources needed for the process to complete
    - Number of terminated processes
    - Whether the process is interactive or batch

---
### Deadlock problem - solution -- recovery -- resource preemption
- Select preempted process
    - Reference factor: minimum cost target
- Process rollback
    - Return to some safe state, restart the process to a safe state
- possible hunger
    - The same process may always be selected as the preemptee