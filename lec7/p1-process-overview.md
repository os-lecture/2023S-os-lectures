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

# Lecture 7 Process Management and Single Processor Scheduling
## Section 1 Process Management

<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

Spring 2023

---

**Outline**

### 1. Basic concept of process
- Background of demand for process management
- Concept of process
- Process and task
2. Process management
3. Thoughts on fork()

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Background of demand for process management

- Background
  - **Hardware** is becoming increasingly powerful
    - Faster CPU with MMU/TLB/Cache
    - Larger memory and external storage
    - More diverse peripherals
  - Developers hope to have more **dynamic interaction and control capabilities** on computers
  - Users need more **convenient** computer interaction capabilities
  
- Objective
  - **Improve development and execution efficiency**

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Background of demand for process management

- The interface between the **OS and the user for interaction** 
- **Command line** interface (CLI)
- Users directly enters commands directly through the keyboard
-Shell
- **Graphical** user interface (GUI)
- Users input commands through the mouse/window and other means

![bg right:50% 90%](figs/cli-gui.png)


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Background of demand for process management

- User needs to **dynamically manage and control the execution of applications**
- During the application execution process, the user actively sends **requests** to the OS through the interface to **create and execute** new applications, **pause or stop** the execution of applications, and so on

![bg right:53% 90%](figs/cli-gui.png)

---

#### Purpose of introducing the concept of process
- Clearly **depict** the dynamic internal laws of program execution in the OS
- Effectively **manage and schedule** the execution of multiple programs and the use of resources

![bg right:47% 90%](figs/cli-gui.png)

---
<style scoped>
{
  font-size: 29px
}
</style>
#### The abstraction provided by the process to the application program

- The **key abstraction** provided by the process to the application program from the application view
  - Independent logical **control flow**:  It appears as if the program has exclusive use of the processor
  - Private **address space**: It appears as if the program has exclusive use of the memory system
  
![bg right:47% 90%](figs/cli-gui.png)

---
<style scoped>
{
  font-size: 32px
}
</style>
#### Looking at the process from an implementation perspective

From an implementation perspective, a process is a **data structure** related to process management established by the OS during program execution, as well as the **dynamic operating** process on the data structure

![bg right:55% 90%](figs/cli-gui.png)


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Looking at the process from a resource perspective

- From a resource perspective, a process is a collection of **resources used** during program execution
  - Shared resources vs exclusive resources
  - Processor, time
  - memory, address space
  - File, I/O, ...
  
![bg right:52% 90%](figs/cli-gui.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### What is a process?

- Simple definition
  - The execution **process** of a program
  - An **instance** of a program being executed
- Detailed definition: A dynamic process of **executation and resource usage** of a program with certain **independent functions** on a set of **data set**
  - Executes program logic and reads/writes data
  - Creates and executes new processes
  - Uses shared resources such as files

![bg right:40% 90%](figs/cli-gui.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Task and process

Analysis from two aspects of **resource usage** and **execution process**

Similarities:
- From the user's perspective, tasks and processes both represent running programs
- From the OS's perspective, tasks and processes both represent the execution process of a program
- From the perspective of resource usage
  - Both can be interrupted by the OS and time-shareing occupy CPU resources by switching
  - Both require address space to place code and data

---
<style scoped>
{
  font-size: 32px
}
</style>
#### Task and Process

Analysis from two aspects of **resource usage** and **execution process**

Similarities:
- From the perspective of execution process, both have a lifecycle that runs from start to finish
  - Task lifecycle --> Process lifecycle
  - Task's three-state model --> Process's three-state model
  - Task switching --> Process switching
  - Task context --> Process context


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Task and Process

Analysis from two aspects of **resource usage** and **execution process**

Differences:
- Tasks are the **primary stage** of processes mentioned here and do not have the following functionalities:
  - Processes can create child processes and overwrite existing program content with new program content during their execution
  - Processes become the carriers for dynamically requesting, using, and releasing various resources during program execution

The dynamic functionality of processes allows for more flexible program execution

---

#### Process is an important concept in computer science

**Process** is one of the most profound and successful concepts in computer science (from CSAPP)
![bg right:60% 90%](figs/cli-gui.png)

---

**Outline**

1.  Basic concept of process
### 2. Process management
- System calls for process management
- Process control block (PCB)
- Process creation and program loading
- Process wait and exit
3. Thoughts on fork()

---

#### Background of system calls for process management

- How can applications **easily and dynamically execute other applications**?
  - process_id = execute(app_name)?
- How can applications **know if the other application** they launched has ended?
  - The launched applications exit(status)?
  - The initiating main application wait(process_id)?

Therefore, various OSs (UNIX/Windows, etc.) have designed various system calls for process management similar to the ones mentioned above

---

#### System calls for process management

| system call name| meaning|
| -------------------------- | ------ |
| ``int fork()`` | **Create** a process and return the PID of the child process. |
| ``int exec(char *file)`` | **Load** a file and execute it; return only on error. |
| ``int exit(int status)`` | **Terminate** itself; report `status` to the parent process executing the waitpid() syscall. |
| ``int waitpid(int pid, int *status)`` | **Wait for **`pid` child process to exit, get its ``*status`` exit status. |
| ``int getpid()`` | **Get** the PID of the current process. |


---

#### Example of process management applications: ``getpid()``

```rust
//usr/src/bin/hello_world.rs
pub fn main() -> i32 {
// show own PID
println!("pid {}: Hello world from user mode program!", getpid());
0 // exit code returned
}
```

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Example of process management applications: ``fork()`` and ``exec()``

```rust
//usr/src/bin/forkexec.rs
pub fn main() -> i32 {
println!("pid {}: parent start forking ...", getpid());
let pid = fork(); // create child process
if pid == 0 {
// child process
println!("pid {}: forked child start executing hello_world app ... ", getpid());
exec("hello_world"); // execute hello_world program
100
} else {
// parent process
let mut exit_code: i32 = 0;
println!("pid {}: ready waiting child ...", getpid());
assert_eq!(pid, wait(&mut exit_code)); //Confirm the waiting child process PID
assert_eq!(exit_code, 0); //Confirm that the exit code is 0
        println!("pid {}: got child info:: pid {}, exit code: {}", getpid() , pid, exit_code);
        0
    }
}
```

---

#### Example of process management applications: ``fork()`` and ``exec()``

Execution result
```
Rust user shell
>> forkexec
pid 2: parent start forking ...
pid 2: ready waiting child ...
pid 3: forked child start execing hello_world app ...
pid 3: Hello world from user mode program!
pid 2:  got child info:: pid 3, exit code: 0
Shell: Process 2 exited with code 0
>> QEMU: Terminated

```

---

**Outline**

1. Basic concept of process
2. Process management
- System calls for process management 
- ### Process control block (PCB)
- Process creation and program loading
- Process wait and exit
1. Thoughts on fork()

---

#### Process control block (PCB)

![bg right:70% 95%](figs/process-os-key-structures.png)


---

#### Shell executes user input commands

![w:1200](figs/forkexec-app.png)


---

#### Process control block in shell execution

![w:550](figs/process-os-key-structures.png)
![bg right:50% 100%](figs/forkexec-app.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Process switching

- The process of process switching
  - **Pause** the currently running process and change its state from running to another state
  - **Schedule** another process  and change its state from ready to running

- The requirements for process switching:
  - **Save** the process context before switching
  - **Restore** the process context after switching

![bg right:40% 100%](figs/process-os-key-structures.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Process lifecycle

- Process switching
  - Pause the currently running process and change its state from running to another state
  - Schedule another process  and change its state from ready to running
- Information about the process lifecycle
  - **Registers** (PC, SP, …)
  - **CPU status**
  - **Memory address space**
![bg right:40% 90%](figs/process-os-key-structures.png)
 
---

**Outline**

1. Basic concept of process
2. Process management
- System calls for process management 
- Process control block (PCB)
- ### Process creation and program loading
- Process wait and exit
1. Thoughts on fork()

---

#### API of windows process creation : ``CreateProcess(filename)``

- Close all file descriptors in the child process during creation
- ``CreateProcess(filename, CLOSE_FD)``
- Change the environment of the child process during creation
- ``CreateProcess(filename, CLOSE_FD, new_envp)``
 
---

#### Process creation/loading

- System calls for Unix process creation/loading: fork/exec
- fork() creates a new process by duplicating the calling process
- parent (old PID), child (new PID)
- exec() overwrites the current process with a new program
- PID did not change

![bg right:45% 90%](figs/fork-exec.png)

---

#### Example of creating a process with fork and exec

```C
int pid = fork(); 		// create child process
if(pid == 0) { 			// child process continues here
// Do anything (unmap memory, close net connections…)
exec("program", argc, argv0, argv1, ...);
}
```
- fork() creates an inherited child process
- **Copy** all variables and memory of the parent process
- **Copy** all CPU registers of the parent process (**except** one register )
   
 
---
<style scoped>
{
  font-size: 30px
}
</style>
#### Example of creating a process with fork and exec

```C
int pid = fork(); 		// create child process
if(pid == 0) { 			// child process continues here
// Do anything (unmap memory, close net connections…)
exec("program", argc, argv0, argv1, ...);
}
```
- Return values of fork()
- fork() returns 0 in the child process
- fork() returns the process ID (PID) of the child process in the parent process
- The return value of fork() can be conveniently used for subsequent operations. The child process can use getpid() function to get its own PID

---

#### Execution process of process creation ``fork()`` 

- For the child process, fork() is a copy process of the parent process's address space
![w:900](figs/fork.png)

---

#### Example of program loading and execution

- The system call exec() loads a new program to replace the currently running process (Is there a problem with the code???)
```C
main()
…
int pid = fork(); 			// create child process
if (pid == 0) { 			// child process continues here
exec_status = exec("calc", argc, argv0, argv1, ...);
printf("Why would I execute?"); // Can this line of code be executed???
} else { 				// parent process continues here
printf("Whose your daddy?");
…
child_status = wait(pid);
}

```

 
---

#### Example of program loading and execution

- The system call exec( ) loads a new program to replace the currently running process
```C
main()
…
int pid = fork(); 			// create child process
if (pid == 0) { 			// child process continues here
exec_status = exec("calc", argc, argv0, argv1, ...);
printf("Why would I execute?");
} else { 				// parent process continues here
printf("Whose your daddy?");
…
child_status = wait(pid);
}
if (pid < 0) { /* error occurred */
```


 
---

#### Process of program loading and execution

Load the calculator after calling fork() in the shell

![w:900](figs/exec-1.png)

 
 
---

#### Process of program loading and execution

Load the calculator after calling fork() in the shell

![w:900](figs/exec-2.png)

 
 
---

#### Process of program loading and execution

Load the calculator after calling fork() in the shell

![w:900](figs/exec-3.png)

  
---

#### Process of program loading and execution

Load the calculator after calling fork() in the shell

![w:800](figs/exec-4.png)

 
 
---

#### Process of program loading and execution

Load the calculator after calling fork() in the shell

![w:800](figs/exec-5.png)

 
 
---

#### Process of program loading and execution

Load the calculator after calling fork() in the shell

![w:800](figs/exec-6.png)


---

#### Example of process management application：``fork()``

```C
int  main()
{
     pid_t  pid;
      int  i;

      for  (i=0;  i<LOOP;  i++)
      {
           /* fork  another  process  */
           pid = fork();
           if  (pid < 0) { /*error  occurred  */
                fprintf(stderr, “Fork Failed”);
                exit(-1);
           }
           else if (pid == 0) { /* child process */
                fprintf(stdout,  “i=%d,  pid=%d,  parent  pid=%d\n”,I,      
                             getpid() ,getppid());
           }   
}
wait(NULL);
exit(0);
}

```



---

#### Example of process management application: ``fork()``

![w:1000](figs/fork-example.png)



---

**Outline**

1. Basic concept of process
2. Process management
- System calls for process management 
- Process control block (PCB)
- Process creation and program loading
- ### Process wait and exit
3. Thoughts on fork()

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Parent process waits for child process

- The system call wait() is used by the parent process to wait for the termination of the child process
  - When the child process exits, it returns a value to the parent process through the exit() system call
  - The parent process accepts and processes the return value using wait()
- Function of the wait() system call
  - When the child process is alive, the parent process enters the waiting state and waits for the return result of the child process
  - When the child process calls exit(), wake up the parent process and use the return value of exit() as the return value of wait in the parent process

---

#### Zombie process and orphan process

- Zombie process: A child process whose process control block **has not** been reclaimed by the parent process through the sys_wait system call, even though it has already executed the sys_exit system call
  - When waiting for a zombie child process, the wait() system call immediately returns one of its values
- Orphan process: A child process **whose parent process has exited before it**
  - The root process is responsible for waiting and reclaiming orphan processes

---
<style scoped>
{
  font-size: 29px
}
</style>

#### Process exit ``exit()``

- When a process ends, it calls exit() to complete process resource reclamation
  - Function of the exit() system call
  - Use the call argument as the "result" of the process
  - Close all open files and other resource occupations
  - Release memory
  - Release most of the kernel data structures associated with the process
  - Preserve the value of the result and check whether the parent process is alive
    - If the parent process is not alive, set the parent process to the root process
  - Enter the zombie/defunct state, waiting for the parent process to reclaim it

---
<style scoped>
{
  font-size: 32px
}
</style>

#### Other related system calls for process management

- Priority control
  - The nice() system call is used to specify the initial priority of a process
  - In Unix systems, the priority of a process will decay over time
- Process debugging
  - The ptrace() system call allows one process to control the execution of another process
  - Sets breakpoints, views registers, etc
- Timing
  - The sleep() system call allows a process to wait in the timer's waiting queue for a specified amount of time



---

#### Relationship between process management and process status

System calls related to process management may affect the state of the process
![w:500](figs/process-control-and-life.png)

 
---

#### Relationship between process management and process status

![w:700](figs/process-control-and-life-2.png)


 
---

**Outline**

1. Basic concept of process
2. Process management
### 3. Thoughts on fork()
- Overhead of fork()?
- Rethinking fork

---

#### Overhead of fork()?

- Implementation overhead of fork()
  - Allocate memory for the child process
  - Copy the memory and CPU registers of the parent process to the child process
  - The overhead is expensive!!

![bg right:50% 100%](figs/fork-exec.png)

---

#### Overhead of fork()?

- In 99% of cases, we call exec() after fork()
  - Memory copying during the fork() operation is unnecessary --why?
  - The child process may close opened files and network connections --why?

![bg right:50% 100%](figs/fork-exec.png)

---

#### Overhead of fork()?

- When vfork() creates a process, it no longer creates the same memory image
  - Lightweight fork()
  - The child process should call exec() almost immediately
  - Now using Copy on Write (COW) technology
![bg right:50% 100%](figs/cow.png)
---

#### Rethinking fork

[Andrew Baumann, etc., A fork() in the road, HotOS 2019](https://www.microsoft.com/en-us/research/publication/a-fork-in-the-road/)

![w:800](figs/fork-in-the-road.png)

---

#### Rethinking fork

The fork system call is one of Unix's great ideas.
-- https://cs61.seas.harvard.edu/site/2018/WeensyOS/

- It's simple: no parameters!
- It's elegant: fork is orthogonal to exec
- It eased concurrency


---

#### Rethinking fork

But!
- Fork is no longer simple
  - Fork encourages memory overcommit
  - Fork is incompatible with a single address space
  - Fork is incompatible with heterogeneous hardware
  - Fork infects an entire system


---

#### Rethinking fork

But!
![w:1100](figs/fork-slow.png)



---

#### Rethinking fork

![w:1100](figs/unix-ken-why-fork.png)


---

#### Rethinking fork

![w:1100](figs/origins-of-fork.png)



---

#### Rethinking fork

For implementation expedience [Ritchie, 1979]
- fork was 27 lines of PDP-7 assembly
   - One process resident at a time
   - Copy parent’s memory out to swap
   - Continue running child
-  exec didn’t exist – it was part of the shell
   - Would have been more work to combine them  

<!--
https://www.infoq.cn/article/BYGiWI-fxHTNvSohEUNW
When Unix was rewritten for the PDP-11 computer (which had memory translation hardware that allowed multiple processes to remain resident), it was already inefficient to copy a process's entire memory just to drop a process in exec. We suspect that in the early days of Unix, fork survived primarily because programs and memory were small (only eight 8 KiB pages on the PDP-11), memory access was fast relative to instruction execution, And it provides a reasonable abstraction. There are two important points here: -->

---

#### Rethinking fork

Conclusions:
- Fork is not an inspired design, but an accident of history
- Only Unix implemented it this way
- We may be stuck with fork for a long time to co me
- But, let's not pretend that it's still a good idea today!

**Please, stop teaching students that fork is good design**
- Begin with spawn
- Teach fork, but include historical context

---

### Summary

1. Basic concept of process
2. Process management
3. Thoughts on Fork()
- Overhead of fork()?
- Rethinking fork