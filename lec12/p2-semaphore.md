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

## Section 2 Semaphore

---
<style scoped>
{
  font-size: 30px
}
</style>

### Semaphore
- Semaphore is a method provided by the operating system to coordinate access to shared resources
- It was introduced by Dijkstra in the 1960s
- Semaphore was one of the primary synchronization mechanisms in early OS
![w:600](figs/basic-syncmutex.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### Semaphore
- Semaphore is an abstract data type consisting of an integer variable (sem) and two atomic operations
    - P(): Prolaag in Dutch, meaning "try to decrease"
       - $sem = sem - 1$
       - If sem < 0, enter waiting; otherwise, continue
    - V(): Verhoog in Dutch, meaning "increase"
       - $sem = sem + 1$
       - If $sem \le 0$, wake up one waiting
![bg right:40% 100%](figs/sema-train.png)

---
<style scoped>
{
  font-size: 28px
}
</style>

### Semaphore
- Semaphore is protected integer variable
    - Once initialized, it can only be modified through P() and V() operations
    - The **OS** ensures that PV operations are atomic
- P() may block, while V() does not
- Semaphore is typically assumed to be "fair"
    - Threads are not indefinitely blocked by P() operations
    - Semaphore waits are assumed to be first-in, first-out

Can spinlocks implement first-in-first-out?

![bg right:30% 100%](figs/sema-train.png)


---
### Semaphore
Conceptual implementation of semaphore
![w:1200](figs/semaphore-impl.png)


---
### Semaphore
There are two types of semaphores
- Binary semaphore: Resource count of 0 or 1
- Counting semaphore: Resource count of any non-negative value
- The two types are equivalent and one can be implemented using the other

Uses of semaphore
- **Mutual exclusion** and **conditional synchronization**

![bg right:40% 100%](figs/sema-train.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### Semaphore
Example of mutual exclusion
- Set a semaphore with an initial value of 1 for each critical section
- Use P() and V() operations in pairs
    - P() operation ensures mutually exclusive access to resources
    - V() operation releases resources after use
    - The sequence of PV operations must be correct, non-repetitive, and not missed

![bg right:40% 100%](figs/semaphore-use-1.png)
 


---
### Semaphore
Example of conditional synchronization
- Set a semaphore with an initial value of 0 for each condition

![bg right:50% 100%](figs/semaphore-use-2.png)



---
### Semaphore
Example of producer-consumer problem
- Description of the bounded buffer producer-consumer problem
    - One or more producers place data in a buffer
    - A single consumer retrieves data from the buffer
    - At any time, only one producer or consumer can access the buffer
![w:700](figs/semaphore-use-3.png)


---
<style scoped>
{
  font-size: 28px
}
</style>

### Semaphore
Example of producer-consumer problem
- Problem analysis
    - Only one thread can operate on the buffer at a time (mutual exclusion)
    - When the buffer is empty, the consumer must wait for the producer (conditional synchronization)
    - When the buffer is full, the producer must wait for the consumer (conditional synchronization)
- Use semaphores to describe each constraint
    - Binary semaphore mutex
    - Count semaphore fullBuffers
    - Count semaphore emptyBuffers

---
### Semaphore
Examples of producer-consumer problems: Does the order of P() and V() operations matter?
![w:900](figs/semaphore-use-4.png)


---
### Semaphore
- Reading/developing the code can be challenging
- It is prone to errors
    - Using occupied semaphore
    - Forgetting to release semaphore
    - It cannot prevent deadlocks
    - It requires a higher level of proficiency from programmers
![bg right:40% 100%](figs/semaphore-use-4.png)