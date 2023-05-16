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

## Section 3 Monitor condition variable

  
 
<!--
Gregory Kesden, Monitors and Condition Variables https://cseweb.ucsd.edu/classes/sp16/cse120-a/applications/ln/lecture9.html
Mark Spruiell, The C++ Monitor Class. Apr. 2011 https://doc.zeroc.com/pages/viewpage.action?pageId=5048235
wikipedia, Monitor (synchronization) https://en.wikipedia.org/wiki/Monitor_(synchronization)
Mike Vine, Making a C++ class a Monitor (in the concurrent sense) https://stackoverflow.com/a/48408987
David Rodríguez - dribeas, How arrow-> operator overloading works internally in c++? https://stackoverflow.com/a/10678920
Fruit_初, Monitors, March, 2017. https://www.jianshu.com/p/8b3ed769bc9f
C++ Concurrency Pattern #6: Monitor - monitor http://dengzuoheng.github.io/cpp-concurency-pattern-6-monitor
-->
---
<style scoped>
{
  font-size: 28px
}
</style>

### Monitor
- Motivation: Why is there a monitor? The traditional PV and lock mechanism have the following problems:
   - Poor program readability: To understand if the operations on a set of shared variables and semaphores are correct, one must read the entire system or concurrent program
   - Difficult program modification and maintenance: The program lacks locality, so modifying any group of variables or a code segment can affect the entire system
   - Difficult to ensure correctness: Operating systems or concurrent programs are often complex, making it challenging to guarantee a complex system has no logical errors
   - Prone to deadlock: If the P and V operations are not used correctly, logical errors can lead to deadlock

---
<style scoped>
{
  font-size: 30px
}
</style>

### Monitor
- A monitor is a **program structure** used for mutually exclusive access to shared resources in multithreading
- It adopts an **object-oriented method** approach, which simplifies the synchronization control between threads
- At any one time, only one thread executes the monitor code
- A thread inside the monitor can temporarily relinquish exclusive access to the monitor and wait for an event to occur before resuming
![bg right:40% 100%](figs/basic-monitor.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### Monitor
- Modularity: A monitor is a basic program unit that can be compiled independently

- Abstract data type: A monitor is a special type of data structure that includes both data and code for manipulating the data

- Information hiding: Monitors are semi-transparent. The procedures (functions) inside the monitor implement certain functionality and are not visible externally

![bg right:40% 100%](figs/moniter.jpg)

---
<style scoped>
{
  font-size: 24px
}
</style>

### Monitor -- Condition variable
- Shared variables inside a monitor are not visible outside; external access to the shared variables can only be done indirectly through the external procedures defined within the monitor
   - Mutual exclusion: At any given time, only one active process can be inside the monitor, competing for entry with locks
   - Waiting: Threads entering the monitor may enter a waiting state if a **resource is already occupied**
     - Each condition variable represents a specific reason for waiting and corresponds to a waiting queue
     - Entry queues manage threads/processes that have not entered the monitor
   - Wakeup: Threads waiting inside the monitor can be awakened (by other threads releasing resources)
   - Synchronization: Waiting and wakeup operations
   - Monitor operations: Enter (entering the monitor), Leave (leaving the monitor), Wait (waiting), Signal (wakeup)

<!--![bg right:40% 100%](figs/basic-monitor-2.png)
![bg right:40% 100%](figs/basic-monitor-2.png)


---
### monitor -- condition variable

-->




<!-- https://blog.csdn.net/Carson_Chu/article/details/104223122 [Operating System] Synchronization Mutual Exclusion Mechanism (2): Monitor and Inter-Process Communication Mechanism (IPC)

https://zhuanlan.zhihu.com/p/465751547 This article is enough for learning process mutual exclusion and process synchronization!
https://www.cnblogs.com/uestcliming666/p/13224545.html "Modern Operating System" - Chapter 6 Synchronization Mutual Exclusion Mechanism (2), Inter-process Communication Mechanism

https://yangzhaoyunfei.github.io/monitors/ Monitors -->



---
<style scoped>
{
  font-size: 28px
}
</style>

### Monitor -- Condition variable
- Composition of a monitor: A collection consisting of processes (functions), variables, data structures, etc.
    - A lock: Controls the mutual exclusion access to the monitor code
    - 0 or more condition variables: Manage the concurrent access to shared data, each condition variable having a waiting (urgent) queue
    - Entry waiting queue
    - Urgent waiting queue
    - Condition variable queue
![bg right:40% 100%](figs/moniter.jpg)


---
<style scoped>
{
  font-size: 26px
}
</style>

### Monitor -- Condition variable
- Entry waiting queue: The waiting queue at the entrance of the monitor
- Condition waiting queue: Waiting queue for a specific condition variable (waiting for a resource occupation)
- Urgent waiting queue: Wake-up queue for urgent usage
    - When thread T1 performs a wake-up operation to awaken T2, if T1 transfers the access permission to T2, T1 is suspended. T1 is placed in the urgent waiting queue
    - The priority of the urgent waiting queue is higher than the condition variable waiting queue
    - Queue waiting for mutual exclusion

![bg right:40% 100%](figs/moniter.jpg)

---
<style scoped>
{
  font-size: 25px
}
</style>

### Monitor -- Flow (T can be a thread or a process)
- Procedure T.enter: Thread T needs to acquire mutual exclusion access (lock) before entering the monitor
- Procedure T.leave: When thread T leaves the monitor, if the urgent queue **is not empty**, it wakes up the threads in the urgent queue and transfers the lock held by T to the awakened thread. If the **urgent queue is empty**, it releases the lock and wakes up a thread from the entry waiting queue
- Procedure T.wait(c): 1) Blocks thread T itself and puts T in the waiting queue of condition variable c;
2) Releases the lock it holds; 3) Wakes up one or more threads from the entry waiting queue
<!--Release the power of the monitor, enter the conditional waiting queue of c; wake up the first thread in the emergency waiting queue // allocate some type of resource for the process entering the monitor, if this resource is available at this time, then the process Use, otherwise the process is blocked and enters the conditional waiting queue -->
- Procedure T.signal(c): 1) Wakes up a thread from the waiting queue of condition variable c
   - 2) Gives the lock held by thread T to the awakened thread
   - 3) Puts thread T itself in the urgent waiting queue
<!--Wake up the process that enters the conditional waiting queue due to waiting for this resource (c’s conditional waiting queue) The first thread enters the process of the monitor to release a certain resource. At this time, the process will wake up and enter the conditional waiting queue due to waiting for this resource Waiting for the first process in the queue -->

<!--
---
### monitor -- condition variable
- Synchronization: Condition Variables (Condition Variables) and related two operations: wait and signal, handle the waiting mechanism.
- Wait() operation
    - Block yourself in the waiting queue
    - Release the mutex access of the monitor
    - Call in a process waiting outside the monitor
- Signal() operation
    - Wake up a thread in the waiting queue
    - Equivalent to a no-op if the wait queue is empty

![bg right:40% 100%](figs/basic-monitor-2.png)

-->




---
<style scoped>
{
  font-size: 30px
}
</style>

### Monitor -- Implementation method
<!-- https://blog.csdn.net/qq_34666857/article/details/103189107 Java concurrent programming simulation tube (Hoare tube, Hansan tube, MESA tube) -->
Release handling of condition variables in a monitor

- If thread T1 is in a blocked state due to unsatisfied condition A, and then thread T2 satisfies condition A and performs a signal operation to awaken T1, it is not allowed for both T1 and T2 to be in the monitor simultaneously. So, how do we determine which one executes and which one waits?
- One of the following approaches can be used:

   - 1: (Hoare): T1 executes/T2 waits until T1 leaves the monitor, then T2 continues execution
   - 2: (MESA/Hansen): T2 executes/T1 waits until T2 leaves the monitor, then T1 may continue execution


---
<style scoped>
{
  font-size: 30px
}
</style>

### Monitor - Implementation method
<!-- https://blog.csdn.net/qq_34666857/article/details/103189107 Java concurrent programming simulation tube (Hoare tube, Hansan tube, MESA tube) -->
Release handling of condition variables in a monitor
- When thread T2's signal satisfies the condition that thread T1 is waiting for:
   - Hoare: After T2 notifies T1, T2 blocks, and T1 immediately executes. After T1 completes execution, T2 is awakened to execute
   - Hansen: After T2 notifies T1, T2 continues execution. After T2 completes execution (with the last operation being signal), then T1 executes (directly transferring the lock to T1)
   - MESA: After T2 notifies T1, T2 continues execution. T1 does not execute immediately but competes for access rights again

---
### Monitor
Release handling of condition variables in a monitor
<!-- https://cseweb.ucsd.edu/classes/sp17/cse120-a/applications/ln/lecture8.html -->
<!-- https://juejin.cn/post/6925331537365843981 Synchronized principle analysis -->
![w:1000](figs/cond-releases.png)


---
### Monitor
<!-- The release processing method of the condition variable in the monitor -->
![w:900](figs/cond-releases-2.png)




---
### Monitor
<!-- The release processing method of the condition variable in the monitor -->
Two options for waking up a thread: **Directly assigning the lock** vs. **fair competition for the lock**

![w:1000](figs/cond-releases-3.png)



---
### Monitor - Hoare
<!-- https://www.cnblogs.com/upnote/p/13030741.html Theoretical Basis of Java Synchronized - Monitor -->
```
- 1. T1 enters the monitor
- 2. T1 waits for resources (enters the wait queue)
- 3. T2 enters the monitor
- 4. T2 resources are available, notify T1 to resume execution,
      and transfer yourself to the urgent waiting queue
- 5. T1 re-enters the monitor and executes
- 6. T1 leaves the monitor
- 7. T2 re-enters the monitor and executes
- 8. T2 leaves the monitor
- 9. Other threads in the entry queue pass the competition
      Enter the monitor monitor
```
![bg right:35% 100%](figs/hoare.png)

---
### Monitor
<!-- The release processing method of the condition variable in the monitor -->
Two options for waking up a thread: **Directly granting the lock** vs **Re-fairly competing for the lock**

![w:1000](figs/cond-releases-3.png)



---
### Monitor - Hoare
<!-- https://www.cnblogs.com/upnote/p/13030741.html Theoretical Basis of Java Synchronized - Monitor -->
```
- 1. T1 enters the monitor
- 2. T1 waits for resources (enters the wait queue)
- 3. T2 enters the monitor
- 4. T2 resources are available, notify T1 to resume execution,
      and transfer yourself to the urgent waiting queue
- 5. T1 re-enters the monitor and executes
- 6. T1 leaves the monitor
- 7. T2 re-enters the monitor and executes
- 8. T2 leaves the monitor
- 9. Other threads in the entry queue pass the competition
      Enter the monitor monitor
```
![bg right:35% 100%](figs/hoare.png)

---
### Monitor - Mesa
<!-- https://www.cnblogs.com/upnote/p/13030741.html Theoretical Basis of Java Synchronized - Monitor -->
```
- 1. T1 enters the monitor
- 2. T1 waits for resources
     (Enter the wait queue and release the monitor)
- 3. T2 enters the monitor
- 4. T2 resources are available, notify T1
     (T1 is transferred to the entey queue and competes on an equal footing again)
- 5. T2 continues execution
- 6. T2 leaves the monitor
- 7. T1 obtains the execution opportunity from the entry queue
      Dequeue, resume execution
- 8. T1 leaves the monitor
- 9. Other threads in the entry queue pass the competition
      enter monitor
```
![bg right:35% 100%](figs/mesa.png)

---
### Monitor - Hansen
<!-- https://www.cnblogs.com/upnote/p/13030741.html Theoretical Basis of Java Synchronized - Monitor -->
```
- 1. T1 enters the monitor
- 2. T1 waits for resource c
- 3. T2 enters the monitor
- 4. T2 leaves Monitor and waits for notification
      The thread of resource c, the resource is available
- 5. T1 re-enters the monitor
- 6. T1 leaves the monitor
- 7. Other threads pass the competition from the entry queue
      enter monitor
```
![bg right:35% 100%](figs/hansen.png)

---
### Monitor -- Implementation of condition variables
![w:1000](figs/cond-1.png)

---
### Monitor -- Implementation of condition variables
![w:1000](figs/cond-2.png)

---
### Monitor -- Implementation of condition variables
![w:1000](figs/cond-3.png)

---
### Monitor -- Implementation of condition variables
![w:1000](figs/cond-4.png)

---
### Monitor -- Implementation of condition variables
![w:1000](figs/cond-5.png)

---
### Monitor -- Implementation of condition variables
![w:1000](figs/cond-6.png)

---
### Monitor -- Implementation of condition variables
![w:1000](figs/cond-7.png)

---
### Monitor -- Implementation of condition variables
![w:1000](figs/cond-8.png)


---
### Monitor -- Implementation of condition variables
![w:1000](figs/cond-9.png)



---
### Monitor -- Producer-consumer problem
![w:1000](figs/monitor-pc-1.png)


---
### Monitor -- Producer-consumer problem
![w:1000](figs/monitor-pc-2.png)


---
### Monitor -- Producer-consumer problem
![w:1000](figs/monitor-pc-3.png)


---
### Monitor -- Producer-consumer problem
![w:1000](figs/monitor-pc-4.png)