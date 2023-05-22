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

#### Deficiencies of process

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

#### Deficiencies of process

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

#### Why we need threads?
**Performance**!
- Parallel entities (multiple sequential control flows) share the same address space and all available data
   - Easy to access data and share resources
   - Low-cost switching between control flows
   - Easy to manage different control flows

![bg right:50% 100%](figs/thread-process.png)

---

#### Thread vs Process
- A process is an unit for resource allocation (including memory, open files, etc.), and a thread is an unit for CPU scheduling;
- A process has complete resources, while the thread only has essential resources, such as registers and stacks;
- A thread also has the same three basic states as a process - ready, blocked, and running - with transitions among the states
- Threads can reduce the time and space overhead of concurrent execution;

---
#### Thread vs Process
- Multiple threads can exist in one process at the same time;
- Multiple threads can be executed concurrently;
- Resources such as address space and open files can be shared among threads;
- When a thread crashes, it will cause all threads in the corresponding process crash (only for C/C++, and thread crash will not cause process crash in Java).

---

**Outline**

1. Why do we need threads?
### 2. The concept of thread
3. The use of threads
4. The design and implementation of thread 

---
#### Definition of Thread 

A thread is a **part** of a process that describes the **execution status** of a series  of instructions. It is the basic unit of the instruction execution flow in a process and the **basic unit** of CPU scheduling.

![bg right:50% 90%](figs/thread-define.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### The Roles of Process and Thread

- Process: A role of **Resource Allocation** 
   - A process consists of a set of related resources, including address space (code segment, data segment), open files, and other resources

- Thread: a role of **CPU Scheduling**
   - The thread describes the execution status of the instruction stream in a process resource environment

![bg right:40% 95%](figs/thread-define.png)



---
#### Thread in Different OS

![w:950](figs/thread-with-os.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Relationship between process and thread

**thread = process - shared resource**
- A process can have multiple threads
- Threads share the address space and the resources of the process
- Thread crash will cause process crash

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

#### Comparison of thread and process
- A process is a unit for resource allocation (including memory, open files, etc.), and a thread is a unit for CPU scheduling;
- A process has complete resources, while a thread only has essential resources, such as registers and stacks;
- A thread also has the same three basic states as a process - ready, blocked, and running - with transitions among the states
- Threads can reduce the time and space overhead of concurrent execution
   - The creation/termination/switching time of thread is shorter than that of process
   - Threads of the same process share memory and file resources, they communicate directly without going through the kernel


---

#### User-mode Thread and Kernel-mode Thread

![bg 85%](figs/threadvsprocess0.png)
![bg 80%](figs/threadvsprocess1.png)

---

#### Thread Control Block (TCB, Thread Control Block)
```c
typedef struct
{
        int detachstate; // 线程的分离状态
        int schedpolicy; // 线程调度策略 FIFO、RR等
        structsched_param schedparam; // 线程的调度参数 优先级
        int inherited; // 线程的继承性
        int scope; // 线程的作用域进程级、系统级
        size_t guardsize; // 线程栈末尾的警戒缓冲区大小
        int stackaddr_set; // 线程的栈设置
        void* stackaddr; // 线程栈的位置，起始地址
        size_t stacksize; // 线程栈的大小
} pthread_attr_t;
```
---
<style scoped>
{
  font-size: 30px
}
</style>

#### Create Thread API
Create thread: returns zero on success, non-zero otherwise
```c
#include <pthread.h>
int pthread_create( pthread_t * thread,
                const pthread_attr_t * attr,
                      void * (*start_routine)(void*),
                      void * arg);
```
- thread: a pointer to the structure type pthread_t
- attr: specify any attributes this thread may have
- start_routine: function pointer for the thread to start running
- arg: the arguments passed to the function when the thread starts execution


---

#### Waiting Thread API

Waiting thread: Block the thread that calls it until the execution of the target thread ends
```c
#include <pthread.h>
int pthread_join(pthread_t thread, void **retval);
```
- thread: a pointer to the structure type  pthread_t
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

A program that creates two threads, where one prints "A" and the other prints "B".

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

### The design and implementation of thread 
- Several ways to implement threads
  - Thread managed and running in User-Mode (user thread invisible to the kernel)
  - Thread managed in Kernel-Mode and running in User-Mode (user thread visible to the kernel)
  - Thread managed and running in Kernel-Mode (kernel thread)
  - Thread managed&running in Mixed-Mode (lightweight process, mixed thread)
---
<style scoped>
{
  font-size: 32px
}
</style>

### Thread managed&running in User-Mode 
   - Managing and running threads in user mode, OS is not aware of the existence of these threads.
      - POSIX Pthreads, Mach C-threads, Solaris threads
      - Aliases: User-level Thread, Green Thread, Stackful Coroutine, Fiber

![bg right:40% 100%](figs/usr-thread.png)


---
### Thread managed&running in User-Mode 
  - Threads are managed by a set of user-level thread library functions, including thread creation, termination, synchronization and scheduling, etc.
![bg right:40% 100%](figs/usr-thread.png)

---
### 用户线程的优点
- 线程的调度不需要内核直接参与，控制简单。
- 可以在不支持线程的操作系统中实现。
- 创建和销毁线程、线程切换等线程管理的代价比内核线程少得多。
- 允许每个进程定制自己的调度算法，线程管理比较灵活。
- 线程能够利用的表空间和堆栈空间比内核级线程多。
- 同一进程中只能同时有一个线程在运行，如果有一个线程使用了系统调用而阻塞，那么整个进程都会被挂起。


---
<style scoped>
{
  font-size: 32px
}
</style>
### Disadvantages of User-mode Thread
  - When a thread initiates a system call and blocks, the entire process should wait
  - Does not support thread-based processor preemption
  - Can only allocate CPU time by process
  - Threads of a single process can only be allocated to the same CPU core
![bg right:40% 100%](figs/usr-thread.png)

---
<style scoped>
{
  font-size: 32px
}
</style>

### Thread managed in Kernel-Mode&running in User-Mode
  - Thread implemented by the kernel through system calls, and the kernel handles thread creation, termination and management
  - The thread control block (TCB) is implemented and maintained by the kernel
  - It will not affect other threads when a thread is blocked by executing system calls

![bg right:35% 100%](figs/kernel-thread.png)


---
<style scoped>
{
  font-size: 32px
}
</style>

### Thread managed in Kernel-Mode&running in User-Mode
   - A process can have multiple threads
      - Design of Windows kernel
      - Design of rCore/uCore core
   - Only one thread is included in a process
     - Design of Earlier Linux kernels


![bg right:35% 100%](figs/kernel-thread.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### Thread managed in Kernel-Mode&running in User-Mode
- Disadvantages 
   - Thread switching overhead is not much different from process switching overhead, but is greater than the thread switching overhead managed in user mode.
   - It has conflicts with traditional process management mechanisms, and the implementation of some system calls may be inconsistent with their functionalities or semantics.
     - fork(), signal()...

![bg right:30% 100%](figs/kernel-thread.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### Thread managed in Kernel-Mode&running in Kernel-Mode (kernel thread)
   - Thread is implemented by the kernel. Thread creation, termination and management are completed by the kernel.
   - TCB is implemented and maintained by the kernel.
   - Threads execute in the kernel
      - Such as: Linux kernel thread

![bg right:40% 100%](figs/pure-kernel-thread.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### Kernel Thread 
   - Kernel threads are the incarnations of the kernel, and each incarnation can handle a specific task through time-sharing/parallel processing
   -  The scheduling of kernel threads is managed by the kernel itself. When a kernel thread is blocked, it does not affect other kernel threads because it is the basic scheduling unit.

![bg right:40% 100%](figs/pure-kernel-thread.png)


---
<style scoped>
{
  font-size: 32px
}
</style>

### The Role of Kernel Thread
   - Execute periodic tasks
     - Write the Buffer-Cache back to the storage device regularly
     - Perform virtual memory swapping when few physical memory pages are available
     - Implements a transaction log for the file system
   
![bg right:45% 100%](figs/pure-kernel-thread.png)


---
### Dual-mode Managed Thread

A Light-Weight Process (LWP) is a user thread supported by the kernel. A process can have one or more LWPs. Each LWP is one-to-one  mapped to a kernel thread. Each LWP is supported by a kernel thread. User threads can also be used in an LWP.

---
<style scoped>
{
  font-size: 28px
}
</style>

### Dual-mode Managed Thread

There are three types of relationships between LWP and user threads:

- 1 : 1, an LWP corresponds to one user thread: Linux, JVM
   - User-mode management is cancelled, kernel manages the thread
- N : 1, an LWP corresponds to multiple user threads: OS-independent Green Thread
   - Kernel only manages the processes that contain multiple threads, and the runtime of user-mode threads manage the threads when they run
- M : N,  multiple LWPs correspond to multiple user threads: Solaris OS, Go runtime
   - When user-mode threads are running, they are managed by the user-mode runtime and the kernel coordinately.

---

### Dual-mode Managed Thread
   - M : N threading model
   - Solaris OS + C thread runtime library
   - Go language + Go runtime library + OS

![bg right:50% 100%](figs/lwp2.png)

---
<style scoped>
{
  font-size:28px
}
</style>

###  Dual-mode Managed Thread
   - Programmers decide the multiplexing relationship between kernel threads and user-mode threads
   - User-mode threads are managed by the user-mode thread management library
   - The kernel only recognizes kernel-level threads/processes and schedules them
   - The kernel interacts with the user-mode thread management library
   - It has high flexibility but also high implementation complexity

![bg right:40% 100%](figs/lwp.png)


---
### Thread context switching

Thread is the basic unit of scheduling, and process is the basic unit of resource ownership.

- Thread switching in different processes: process context switching
- Thread switching in the same process:  shared resources such as virtual memory will remain unchanged, and only private data, registers and other data that are not shared by threads need to be switched

---

### Summary

1. Why do we need threads?
2. The concept of thread
3. The use of threads
4. The design and implementation of thread 
