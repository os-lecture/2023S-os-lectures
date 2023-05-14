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

## Section 4 Synchronization Mutex Instance Problem

---
<style scoped>
{
  font-size: 28px
}
</style>

### Dining philosophers problem
- 5 philosophers sit around a round table
- There are 5 forks on the table
- Put a fork between every two philosophers
- Philosopher actions include thinking and eating
- You need to use the left and right forks at the same time when eating
- Put the two forks back in place while thinking

How to ensure the orderly progression of actions among philosophers?
Such as: no one will never get a fork

![bg right:40% 100%](figs/philosopher.png)

---
### Dining Philosophers Problem -- Option 1
![w:900](figs/philo-1.png)

 may cause deadlock if incorrect

---
### Dining Philosophers Problem -- Solution 2
![w:900](figs/philo-2-1.png)

---
### Dining Philosophers Problem -- Solution 2
![w:900](figs/philo-2-2.png)

---
### Dining Philosophers Problem -- Solution 2
![w:900](figs/philo-2-3.png)

---
### Dining Philosophers Problem -- Solution 2
![w:900](figs/philo-2-4.png)


---
### Dining Philosophers Problem -- Solution 2
![w:800](figs/philo-2-5.png)
<style scoped>
{
  font-size: 28px
}
</style>

Mutual exclusive access is correct, but only one person is allowed to eat at a time


---
### Dining Philosophers Problem -- Scheme 3
![w:800](figs/philo-3-1.png)


---
### Dining Philosophers Problem -- Scheme 3
![w:800](figs/philo-3-2.png)


---
### Dining Philosophers Problem -- Scheme 3
![w:800](figs/philo-3-3.png)


---
### Dining Philosophers Problem -- Scheme 3
![w:800](figs/philo-3-4.png)


---
### Dining Philosophers Problem -- Scheme 3
![w:800](figs/philo-3-5.png)


---
### Dining Philosophers Problem -- Scheme 3
![w:750](figs/philo-3-6.png)
There is no deadlock, and multiple people can eat at the same time

---
### Dining Philosophers Problem -- Scheme 4

![w:900](figs/philo-4-1.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### Dining Philosophers Problem -- Scheme 4
<!-- https://blog.csdn.net/weixin_43237362/article/details/104712647 AND type semaphore -->

The AND-type semaphore set refers to the semaphore operation when multiple resources are required at the same time and each resource occupies one resource.

When a piece of code needs to acquire two or more critical resources simultaneously, it may result in a situation where each thread acquires some critical resources and waits for the rest. Each thread refuses to give up its resources, leading to a deadlock.

A basic idea to solve this problem is: apply for multiple critical resources required by the entire code in a primitive, or allocate all of them to it, or allocate none of them to it. This is the basic idea of AND-type semaphore sets.

---
### Dining Philosophers Problem -- Solution 4
<!-- https://blog.csdn.net/weixin_43237362/article/details/104712647 AND type semaphore -->
AND type semaphore set
```
P(S1, S2, ..., Sn)
{
     While(TRUE)
     {
         if (S1 >=1 and … and Sn>=1 ){
             for( i=1 ;i<=n; i++) Si--;
         break;
         }
         else {
              Place the thread in the waiting queue associated with the first Si
              found with Si < 1
         }
     }
}
```

---
### Dining Philosophers Problem -- Solution 4
<!-- https://blog.csdn.net/weixin_43237362/article/details/104712647 AND type semaphore -->
AND type semaphore set
```
V(S1, S2, ..., Sn){
     for (i=1; i<=n; i++) {
             Si++;
             Remove all the thread waiting in the queue associated with Si into
             the ready queue
      }
}
```
---
### Dining Philosophers Problem -- Scheme 5

![w:1000](figs/philo-5-1.png)


---
### Dining Philosophers Problem -- Scheme 5

![w:1000](figs/philo-5-2.png)


---
### Dining Philosophers Problem -- Scheme 5

![w:1000](figs/philo-5-3.png)

---
<style scoped>
{
  font-size: 28px
}
</style>

### Dining Philosophers Problem -- Scheme 5
Scheme 5 not only has no deadlock, but also can obtain the maximum degree of parallelism for any philosopher. 
The algorithm uses an array `state` to keep track of whether each philosopher is eating, thinking, or hungry (trying to pick up a fork). A philosopher is only allowed to enter the eating state if both neighbors are not eating.

Each thread runs the function `philosopher` as the main code, while the other functions `take_forks`, `put_forks` and `test` are just normal functions, not separate threads.

![bg right:40% 100%](figs/philo-5.png)

---
### The Reader-Writer Problem
- Two types of users sharing data
   - Reader: Read only, does not modify data
   - Writer: read, modify data
- Read and write to shared data
   - multiple: "read-read" -- allow
   - Single: "read-write" -- mutual exclusion
   - Single: "write-write" -- mutual exclusion

![bg right:40% 100%](figs/reader-writer.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### The Reader-Writer Problem
- Reader first strategy
    - As long as a reader is reading the state, subsequent readers can directly enter
    - If readers keep coming in, writers are starving
  - Writer first policy
    - Writers should perform write operations as soon as there are writers ready
    - Readers are starving if writers are continuously ready

![bg right:40% 100%](figs/reader-writer.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### Reader-Writer Problem -- Scenario 1
Describe each constraint with a semaphore
- WriteMutex: Controls the mutual exclusion of read and write operations, initialized to 1
- Rcount: the number of readers who are reading, initialized to 0
-  CountMutex: Controls the mutually exclusive modification of the reader count, initialized to 1
![bg right:40% 100%](figs/reader-writer.png)

---
### Reader-Writer Problem -- Solution 1 (Semaphore)
![w:800](figs/wr-1.png)

---
### Reader-Writer Problem -- Solution 1 (Semaphore)
![w:800](figs/wr-2.png)

---
### Reader-Writer Problem -- Solution 1 (Semaphore)
![w:800](figs/wr-3.png)

---
### Reader-Writer Problem -- Solution 1 (Semaphore)
![w:800](figs/wr-4.png)

---
### Reader-Writer Problem -- Solution 1 (Semaphore)
![w:800](figs/wr-5.png)

---
### Reader-Writer Problem -- Solution 1 (Semaphore)
![w:700](figs/wr-6.png)
In this implementation, readers take precedence


---
### Reader-Writer Problem -- Solution 2 (Monitor)
![w:900](figs/mwr-1.png)


---
### Reader-Writer Problem -- Solution 2 (Monitor)
![w:900](figs/mwr-2.png)



---
### Reader-writer problem -- Option 2 (monitor) --reader
![w:900](figs/mwr-3.png)


---
### Reader-writer problem -- Option 2 (monitor) --reader
![w:900](figs/mwr-4.png)



---
### Reader-writer problem -- Option 2 (monitor) --reader
![w:900](figs/mwr-5.png)


---
### Reader-writer problem -- Option 2 (monitor) --reader
![w:900](figs/mwr-6.png)


---
### Reader-writer problem -- Option 2 (monitor) --reader
![w:900](figs/mwr-7.png)



---
### Reader-writer problem -- Option 2 (monitor) --reader
![w:900](figs/mwr-8.png)


---
### Reader-writer problem -- Option 2 (monitor) --reader
![w:900](figs/mwr-9.png)



---
### Reader-writer problem -- Option 2 (monitor) --reader
![w:900](figs/mwr-10.png)


---
### Reader-Writer Problem -- Solution 2 (Monitor) -- Writer
![w:900](figs/mwr-11.png)



---
### Reader-Writer Problem -- Solution 2 (Monitor) -- Writer
![w:900](figs/mwr-12.png)



---
### Reader-Writer Problem -- Solution 2 (Monitor) -- Writer
![w:900](figs/mwr-13.png)


---
### Reader-Writer Problem -- Solution 2 (Monitor) -- Writer
![w:900](figs/mwr-14.png)



---
### Reader-Writer Problem -- Solution 2 (Monitor) -- Writer
![w:900](figs/mwr-15.png)



---
### Reader-Writer Problem -- Solution 2 (Monitor) -- Writer
![w:900](figs/mwr-16.png)