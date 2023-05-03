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

# Lecture 11 Threads and coroutines

## Section 1 Thread


<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023

---

**Outline**

### 1. Why do we need threads?
2. The concept of thread
3. The use of threads
4. The design and implementation of thread 

---
<style scoped>
{
  font-size: 32px
}
</style>

#### Shortcomings of process

   - Difficulty with parallel/concurrent processing
   - Address space isolation between processes
   - Inconvenience to share/exchange data via IPC
   - High overhead to manage process 
      - create/delete/switch
![bg right:50% 100%](figs/ps-issue1.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Deficiencies in the process

   - Difficulty with parallel/concurrent processing
   - Address space isolation between processes
   - Inconvenience to share/exchange data via IPC
   - High overhead to manage process 
      - create/delete/switch
![bg right:50% 100%](figs/ps-issue2.png)


---
#### Why do we need threads?

Multiple activities may occur simultaneously in an application, and some activities may be blocked. **Decomposing a program into multiple sequential control flows that can run in parallel** can improve execution **efficiency**, and the program design model will become **simpler**.
![bg right:50% 95%](figs/thread-process.png)

<!--
![bg right:48% 95%](figs/thread.png)
-->

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Why do we need threads?
The ever-present user demand - **Performance**!
- Parallel entities (multiple sequential control flows) share the same address space and all available data
   - Easy to access data and share resources
   -  lightweight switch between control flow
   - Easy to manage different control flows

![bg right:50% 100%](figs/thread-process.png)

---

#### Threads vs Processes
- A process is a unit for resource allocation (including memory, open files, etc.), and a thread is a unit for CPU scheduling;
- The process has a complete resource platform, while the thread only has the essential resources, such as registers and stacks;
- A thread also has the same three basic states as a process - ready, blocked, and running - with transitions between the states
- Threads can reduce the time and space overhead of concurrent execution;

---
#### Threads vs Processes
- Multiple threads can exist in one process at the same time;
- Each thread can be executed concurrently;
- Resources such as address space and files can be shared between threads;
- When a thread in the process crashes, it will cause all threads of the process it belongs to to crash (this is for the C/C++ language, and the thread crash in the Java language will not cause the process to crash).

---

**Outline**

1. Why do we need threads?
### 2. The concept of thread
3. The use of threads
4. The design and implementation of thread 

---
#### Thread definition

A thread is a **part** of a process that describes the **execution state** of a series  of instructions. It is the basic unit of the instruction execution flow in the process and the **basic unit** of CPU scheduling.

![bg right:50% 90%](figs/thread-define.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### The role of processes and threads

- The **resource allocation** role of the process
   - A process consists of a set of related resources, including address space (code segment, data segment), open files, and other resources

- The **Handler Scheduling** role of threads
   - The thread describes the execution state of the instruction stream in the process resource environment

![bg right:40% 95%](figs/thread-define.png)



---
#### Support for threads in different operating systems

![w:950](figs/thread-with-os.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### The relationship between processes and threads

**thread = process - shared resource**
- Multiple threads can exist in a process
- Threads share the address space of the process
- Threads share process resources
- A thread crash will cause the process to crash

A thread is a scheduling entity
User-SE v.s. Kernel-SE 
(SE: scheduling entity)

![bg right:40% 100%](figs/thread-process.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Comparison of threads and processes
- A process is a unit for resource allocation (including memory, open files, etc.), and a thread is a unit for CPU scheduling;
- The process has a complete resource platform, while the thread only has the essential resources, such as registers and stacks;
- A thread also has the same three basic states as a process - ready, blocked, and running - with transitions between the states
- Threads can reduce the time and space overhead of concurrent execution
   - Thread creation/termination/switching time is shorter than process
   - Threads of the same process share memory and file resources, they communicate directly without going through the kernel


---

#### Threads managed by user mode and threads managed by kernel mode

![bg 85%](figs/threadvsprocess0.png)
![bg 80%](figs/threadvsprocess1.png)

---

#### Thread Control Block (TCB, Thread Control Block)
```c
typedef struct
{
        int detachstate; // thread detach state
        int schedpolicy; // thread scheduling policy
        structsched_param schedparam; // thread scheduling parameters
        int inherited; // thread inheritance
        int scope; // the scope of the thread
        size_t guardsize; // size of the guard buffer at the end of the thread stack
        int stackaddr_set; // Thread stack settings
        void* stackaddr; // thread stack location
        size_t stacksize; // thread stack size
} pthread_attr_t;
```
---
<style scoped>
{
  font-size: 30px
}
</style>

#### Create thread API
Create thread: returns zero on success, non-zero otherwise
```c
#include <pthread.h>
int pthread_create( pthread_t * thread,
                const pthread_attr_t * attr,
                      void * (*start_routine)(void*),
                      void * arg);
```
- thread: a pointer to the pthread_t structure type
- attr: specify any attributes this thread may have
- start_routine: function pointer for the thread to start running
- arg: the argument to be passed to the function the thread starts executing


---

#### Waiting Thread API

Waiting thread: Block the thread that calls it until the execution of the target thread ends
```c
#include <pthread.h>
int pthread_join(pthread_t thread, void **retval);
```
- thread: a pointer to the pthread_t structure type
- retval: a pointer to the return value

---

**Outline**

1. Why do we need threads?
2. The concept of thread
### 3. The use of threads
4. The design and implementation of thread 

---

#### Thread Example

```c
1 void *mythread(void *arg) {
2 printf("%s\n", (char *) arg);
3 return NULL;
4}
5 int main(int argc, char *argv[]) {
6 pthread_t p1, p2;
7 int rc;
8 printf("main: begin\n");
9 rc = pthread_create(&p1, NULL, mythread, "A"); assert(rc == 0);
10 rc = pthread_create(&p2, NULL, mythread, "B"); assert(rc == 0);
11 // join waits for the threads to finish
12 rc = pthread_join(p1, NULL); assert(rc == 0);
13 rc = pthread_join(p2, NULL); assert(rc == 0);
14 printf("main: end\n");
15 return 0;
16}
```

---
#### Thread example output

A program that creates two threads, each of which does some independent work, in this case printing "A" or "B".

```
❯ ./t0
main: begin
A
B
main: end
```

---

**Outline**

1. Why do we need threads?
2. The concept of thread
3. The use of threads
### 4. The design and implementation of thread 
- User threads (threads managed by user mode)
- Kernel threads (threads managed by kernel mode)
- Light-Weight process


---
<style scoped>
{
  font-size: 28px
}
</style>

### The design and implementation of thread 
- Several implementations of threads
   - Threads managed by user mode and run in user mode (user threads invisible to the kernel)
     - Thread managed&running in User-Mode
   - Threads managed by the kernel and run in user mode (user threads visible to the kernel)
     - Thread managed in Kernel-Mode&running in User-Mode
   - Threads managed by kernel mode and running in kernel mode (kernel thread)
     - Thread managed&running in Kernel-Mode
   - mixed managed and running threads (lightweight processes, mixed threads)
     - Thread managed&running in Mixed-Mode
---
<style scoped>
{
  font-size: 30px
}
</style>

### The design and implementation of thread 
- Thread managed&running in User-Mode
   - Managing and running threads in user mode, the operating system is not aware of the existence of these threads.
      - POSIX Pthreads, Mach C-threads, Solaris threads
      - Aliases: User-level Thread, Green Thread, Stackful Coroutine, Fiber

![bg right:40% 100%](figs/usr-thread.png)


---
### The design and implementation of thread 

- Threads managed by user mode and run in user mode
    - Thread management is accomplished by a set of user-level thread library functions, including thread creation, termination, synchronization and scheduling, etc.
![bg right:40% 100%](figs/usr-thread.png)

---
### The design and implementation of thread 
- Disadvantages of threads managed by user mode and run in user mode
    - When a thread initiates a system call and blocks, the entire process enters the wait
    - Does not support thread-based processor preemption
    - Can only allocate CPU time by process
![bg right:40% 100%](figs/usr-thread.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### The design and implementation of thread 
- Thread managed in Kernel-Mode&running in User-Mode
   - The thread mechanism implemented by the kernel through system calls, and the kernel completes the creation, termination and management of threads
   - The thread control block TCB is maintained by the kernel, implemented in the kernel
   - Threads executing system calls are blocked without affecting other threads

![bg right:35% 100%](figs/kernel-thread.png)


---
<style scoped>
{
  font-size: 32px
}
</style>

### The design and implementation of thread 
- Thread managed in Kernel-Mode&running in User-Mode
   - A process can include multiple threads
      - Windows kernel design
      - Design of rCore/uCore core
   - Only one thread is included in a process
     - Design of the Linux kernel


![bg right:35% 100%](figs/kernel-thread.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### The design and implementation of thread 
- Disadvantages of managed in Kernel-Mode&running in User-Mode
   - In general, the thread switching overhead is not much different from the process switching overhead, which is greater than the thread switching overhead managed by user mode and allowed by user mode
   - This creates some conflicts with traditional process management mechanisms, and the implementation of some system calls may be inconsistent with their functionality or semantics.
     - fork(), signal()...

![bg right:30% 100%](figs/kernel-thread.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### The design and implementation of thread 
- Thread managed in Kernel-Mode&running in Kernel-Mode(abbreviation: kernel thread)
   - The thread mechanism is implemented by the kernel, and the creation, termination and management of threads are completed by the kernel
   - TCB is maintained by the kernel and implemented in the kernel
   - Threads execute in the kernel
      - Such as: Linux kernel thread

![bg right:40% 100%](figs/pure-kernel-thread.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### The design and implementation of thread 
- Thread managed in Kernel-Mode&running in Kernel-Mode(abbreviation: kernel thread)
   - Kernel threads are the incarnations of the kernel, and each incarnation can handle a specific task through time-sharing/parallel processing
   -  The scheduling of kernel threads is managed by the kernel itself. When a kernel thread is in a blocking state, it does not affect other kernel threads because it is the basic scheduling unit.

![bg right:40% 100%](figs/pure-kernel-thread.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### The design and implementation of thread 
- The role of kernel thread
   - Execute periodic tasks
     - Write the Buffer-Cache back to the storage device regularly
     - Perform virtual memory swap operations when few physical memory pages are available
     - Implements a transaction log for the file system
   
![bg right:45% 100%](figs/pure-kernel-thread.png)


---
### The design and implementation of thread 
- Two-state managed threads

A Light-Weight Process(LWP) is a user thread supported by the kernel. A process can have one or more LWPs. Each LWP is mapped one-to-one with a kernel thread. LWPs are created by a kernel thread.  User threads are also available on top of the LWP.

---
<style scoped>
{
  font-size: 28px
}
</style>

### The design and implementation of thread 
- Two-state managed threads

There are three types of correspondence between LWP and user threads:

- 1 : 1,  LWP corresponds to one user thread: Linux, JVM
   - Cancellation of user mode management, kernel management thread
- N : 1, one LWP corresponds to multiple user threads: OS-independent Green Thread
   - Kernel mode only manages processes that contain multiple threads, and user mode threads manage threads when they run
- M : N,  multiple LWPs correspond to multiple user threads: Solaris OS, Go runtime
   - When user-level threads are running, they are managed in coordination with the kernel.

---

### The design and implementation of thread 
- Two-state managed threads
   - M : N threading model
   - Solaris operating system + C thread runtime library
   - Go language + Go runtime library + OS

![bg right:50% 100%](figs/lwp2.png)

---
<style scoped>
{
  font-size:28px
}
</style>

### The design and implementation of thread 
- Two-state managed threads
   - Programmers decide the multiplexing relationship between kernel threads and user-level threads
   - User-level threads are managed by the user thread management library
   - The kernel only recognizes kernel-level threads/processes and schedules them
   - The kernel interacts with the user-mode thread management library
   - with maximum flexibility and implementation complexity

![bg right:40% 100%](figs/lwp.png)


---
### Thread context switching

Thread is the basic unit of scheduling, and process is the basic unit of resource ownership.

- Thread switching in different processes: process context switching
- Thread switching in the same process:   shared resources such as virtual memory will remain unchanged, and only private data, registers and other data that are not shared by threads need to be switched

---

### Summary

1. Why do we need threads?
2. The concept of thread
3. The use of threads
4. The design and implementation of thread 