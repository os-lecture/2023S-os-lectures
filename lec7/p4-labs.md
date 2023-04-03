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
### Section 4 Practice: Operating System Supporting Processes

Process OS (POS)

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023

---

**Outline**

### 1. Lab Objectives and Steps
- Lab Objectives
- Practical Steps
2. Code Structure
3. Application Design
4. Kernel Programming

![bg right:57% 100%](figs/process-os-detail.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Previous Goals

Improve performance, simplify development, strengthen security
- Address Space OS
     - APP does not need to consider the starting  execution address at runtime, and isolates the address space accessed by APP
- Multiprog & time-sharing
     - effectively sharing of the CPU among APPs, improving the overall performance and efficiency of the system
- BatchOS: isolate APP from OS, strengthen system security, and improve execution efficiency
- LibOS: isolate APP from HW, simplify the difficulty and complexity of applications accessing hardware

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Lab Objectives

Enhance process management and resource management, improve performance, simplify development, and strengthen security.

- Integrate privilege level, address space, and tasks to form a process
- The process becomes the owner of the resource
- Extended process dynamic features, which can issue the following system call requests at the application level:
    - Dynamically create child processes
    - Dynamically construct new processes
    - child process exits / parent process waits for child process to exit

---

#### Lab Requirements

- Understand the concept of processes
- Understand the design and implementation of dynamic process management mechanisms
- Basic understanding of process scheduling
- Mastering writing and using shell applications
- Able to write an OS that supports processes

<!-- The most intelligent Cretaceous "Troodon" operating system troodon -->

![bg right:30% 100%](figs/troodon.png)



---

#### General Idea

![bg right:70% 90%](figs/process-os-detail.png)


---

#### General Idea

![bg right:70% 85%](figs/process-os-key-structures.png)

---

#### General Idea

- Compilation: The application program and the kernel are compiled independently and merged into one image
- Compilation: Different applications can use a unified starting address
- Construction: system call service, **process management and initialization**
- Construction: Establish a virtual memory space based on the page table mechanism
- Execution: Privilege level switching between processes and the OS
- Switching address spaces and accessing data across address spaces

---
<style scoped>
{
  font-size: 30px
}
</style>

#### History background
- 1965: Description of the Future MULTICS Operating System
   - Led by Prof. Fernando J. Corbató at MIT
   - Participating units: MIT, GE (General Electric Company), AT&T Bell Labs
   - Proposed **dynamic process management**, inspiring the creation of UNIX
- 1971: Thompson shell
   - The first **UNIX Shell** written by Ken Thompson
   - Designed with minimalism in mind, its syntax is very simple and is a simple command line interpreter 
   - Many of its features influenced the development of command-line interfaces for later operating systems
---

#### Practical steps
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch5
cd os
make run
```

---
#### Practical steps
```
[RustSBI output]
...
yield
***************/
Rust user shell
>>
```
After the operating system starts the ``shell``, the user can execute the application by typing the application name in the ``shell``.


---

#### Software Architecture

- Manage processes
     - create
     - reclaim
     - fork
     - exec
  
![bg right:55% 100%](figs/process-os-detail.png)

---

**Outline**

1. Lab Objectives and Steps
### 2. Code Structure
3. Application Design
4. Kernel Programming

![bg right:55% 100%](figs/process-os-detail.png)

---

#### Improve OS
```
├── os
     ├── build.rs (modified: application builder based on application name)
     └── src
     ├── loader.rs (modified: application loader based on application name)
   ├── main.rs (modified)
          ├── mm (modified: In order to support the system calls in this chapter, 
                  some enhancements are made to this module)
```

---

#### Improve OS
```
├── os
     └── src
   ├── syscall
   ├──fs.rs (modified: add sys_read)
              ├── mod.rs (modified: new system call distribution processing)
              └── process.rs (modified: add sys_getpid/fork/exec/waitpid)
   ├── task
              ├── manager.rs (modified: task manager, which is part of the task manager function 
                             in the previous chapter)
   ├── mod.rs (modified: adjust the original interface implementation to support process)
              ├── pid.rs (modified: Rust abstraction of process identifiers and kernel stack)
              ├── processor.rs (modified: processor management structure ``Processor``, 
                                which is part of the task manager function in the previous chapter)
             └── task.rs (modified: task control block supporting process management mechanism)
          └── trap
               ├── mod.rs (modified: modify the implementation of system calls to support process 
                           system calls)
```


---

**Outline**

1. Lab Objectives and Steps
2. Code Structure
### 3. Application Design
4. Kernel Programming

![bg right:55% 90%](figs/process-os-detail.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Understanding Processes

- Application Perspective
     - **Process** is the running application
  
- OS Perspective
     - **Process** is an execution process applied on its address space
        - A process owns resources,  OS manages process's resources according to the execution state of the process
![bg right:45% 100%](figs/seg-addr-space.png)

---

#### Process management system calls

```
/// Function: The current process forks a child process.
/// Return value: return 0 for the child process, and return 
the PID of the child process for the current process
/// syscall ID: 220
pub fn sys_fork() -> isize;
```
```
/// Function: Clear the address space of the current process and load a specific executable file, 
and start its execution after returning to the user state.
/// args: path gives the name of the executable file to be loaded;
/// Return value: return -1 if errors (such as no executable file with the same name), 
otherwise it should not return.
/// syscall ID: 221
pub fn sys_exec(path: &str) -> isize;
```
<!-- ![bg right:50% 100%](figs/app-as-full.png) -->



---

#### Process management system calls

```
/// Function: The current process waits for a child process to become a zombie process, 
reclaims all its resources and collects its return value.
/// args: pid represents  the process ID of the child process to wait for, 
if it is -1, it means waiting for any child process;
/// exit_code represents  the address of saving the return value of the child process, 
if this address is 0, it means that it does not need to be saved.
/// Return value: If the child process to be waited for does not exist, return -1; 
otherwise, if the child process to be waited for has not ended, return -2;
/// Otherwise returns the process ID of the terminated child process.
/// syscall ID: 260
pub fn sys_waitpid(pid: isize, exit_code: *mut i32) -> isize;
```


---

#### Application ``shell`` execution flow
1. Get the string (file name) through ``sys_read``
2. Create a child process through ``sys_fork``
3. Create a new application process through ``sys_exec`` in the child process
4. Wait for the end of the child process through ``sys_waitpid`` in the parent process
5. Jump to the first step for loop execution

---
<style scoped>
{
  font-size: 29px
}
</style>

**Outline**

1. Lab Objectives and Steps
2. Code Structure
3. Application Design
### 4. Kernel Programming
- Linking and Loading Apps
- Core Data Structures
- Implementation of Process Management Mechanism

![bg right:55% 90%](figs/process-os-detail.png)

---

#### Linking and Loading Apps

During compiling the operating system, the following link_app.S file will be generated
```
  3_num_app:
  4 .quad 15    # Number of applications
  7  …
  9 _app_names: # name of app0 
10.string "exit"
12  …
17 app_0_start: # start of app0 
18.incbin "../user/target/riscv64gc-unknown-none-elf/release/exit"
19 app_0_end:   # end of app0
```


---

#### App loading based on app name

In the loader loader.rs, analyze the content in link_app.S, and use a globally visible **read-only** vector ``APP_NAMES`` to store the names of all applications in memory in order, for the purpose of passing through exec() to create a new process.


---
<style scoped>
{
  font-size: 30px
}
</style>
**Outline**

1. Experimental Objectives and Steps
2. Code Structure
3. Application Design
4. Kernel Programming
- Linking and Loading Apps
### Core Data Structures
- Implementation of Process Management Mechanism

![bg right:55% 100%](figs/process-os-detail.png)

---

#### Relationship between core data structures

![bg right:60% 100%](figs/process-os-key-structures.png)

---

#### Process Control Block TCB

The corresponding implementation of the process abstraction is the process control block -- TCB ``TaskControlBlock``
```rust
pub struct TaskControlBlock {
     // immutable
     pub pid: PidHandle, // process id
     pub kernel_stack: KernelStack, // process kernel stack
     //mutable
     inner: UPSafeCell<TaskControlBlockInner>,//process internal management information
}
```



---

#### Process Control Block TCB

The corresponding implementation of the process abstraction is the process control block -- TCB ``TaskControlBlock``
```rust
pub struct TaskControlBlockInner {
     pub trap_cx_ppn: PhysPageNum, // The physical page number of the context page trapped
     pub base_size: usize, // the user stack top of the process
     pub task_cx: TaskContext, // process context
     pub task_status: TaskStatus, // process execution status
     pub memory_set: MemorySet, // process address space
     pub parent: Option<Weak<TaskControlBlock>>, // parent process control block
     pub children: Vec<Arc<TaskControlBlock>>, // child process task control block group
     pub exit_code: i32, // exit code
}
```

---

#### Process Manager ``TaskManager``

- The task manager itself is only responsible for managing all ready processes

```rust
pub struct TaskManager {
     ready_queue: VecDeque<Arc<TaskControlBlock>>, // linked list of ready state task control blocks
}
```

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Structure of Processor Management Structure

Processor management structure ``Processor`` describes the CPU execution state
```rust
pub struct Processor {
     current: Option<Arc<TaskControlBlock>>, // the task being executed on the current processor
     idle_task_cx: TaskContext, // idle task
}
```
- Responsibility for maintaining the CPU state that is delegated from the Task `TaskManager`
- Maintain tasks running on a processor, accessing their information or replacing them if necessary
- `Processor` has an idle control flow that attempts to select a task from the Task Manager for executing on the current CPU core and has its own CPU startup kernel stack.


---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

1. Experimental Objectives and Steps
2. Code Structure
3. Application Design
4. Kernel Programming
- Linking and Loading Apps
- Core data structures
### Implementation of process management mechanism

![bg right:50% 100%](figs/process-os-detail.png)

---
<style scoped>
{
  font-size: 32px
}
</style>

#### Overview if Process Management Mechanism Implementation 

1. Create an initial process: create the first user mode process `initproc`
2. Process generation mechanism: introduce process-related system calls `sys_fork`/`sys_exec`
3. Process scheduling mechanism: process active/passive switching
4. Process resource reclaiming mechanism: calling `sys_exit` to exit or or terminate a process and saving its exit code
5. Process resource reclaiming mechanism: the parent process collects the information of the process through `sys_waitpid` and reclaims its resources
6. Character input mechanism: get character input through `sys_read` system call


---

#### Create the initial process

```rust
lazy_static! {
     pub static ref INITPROC: Arc<TaskControlBlock> = Arc::new(
         TaskControlBlock::new(get_app_data_by_name("initproc").unwrap()));
}
pub fn add_initproc() {
     add_task(INITPROC. clone());
}
```
- `TaskControlBlock::new` parse the ELF execution file format of `initproc`, and establish the application address space, kernel stack, etc. form a ready TCB
- `add_task` add the TCB to the ready queue



---
<style scoped>
{
  font-size: 30px
}
</style>

#### Create a new process `fork()`

Copy the contents of the parent process and construct a new TCB

```rust
pub fn fork(self: &Arc<TaskControlBlock>) -> Arc<TaskControlBlock> {...}
```
- Create a new page table and copy the content of the parent process address space
- create new trap context
- Create a new application kernel stack
- Create task context
- Establish parent-child relationship
- set `0` as `fork` return code

---

#### Load new application `exec()`

Replaces the contents of the original application address space with code and data from the new application's ELF executable

```rust
pub fn exec(&self, elf_data: &[u8]) {...}
```
- Reclaim the existing application address space, and directly replace it with a new address space based on the ELF file
- Modify the Trap context of the TCB , and initialize the parsed application entry point, user stack position and some kernel information


---
<style scoped>
{
  font-size: 32px
}
</style>

#### Process Scheduling Mechanism

Pause the current task and switch to the next one
- Timing
    - when uses the `sys_yield` system call
    - when the time slice of the process runs out
- Operation
    - Execute `suspend_current_and_run_next`  
       - Take out the currently executing task, modify its status, and put it at the end of the ready queue
       - Then call the scheduling function to trigger scheduling and switch tasks

---

#### Process Resource Reclaiming Mechanism

Process exits `exit_current_and_run_next`
- Take out current TCB from ``PROCESSOR``, modifying it as a zombie process
- Write the  `exit_code` to TCB
- Hang all child processes into the set of child processes in `initproc`
- Release application address space
- Call the schedule function to trigger scheduling and switch tasks


---
<style scoped>
{
  font-size: 32px
}
</style>

#### Process Resource Reclaiming Mechanism

Waiting for child process to exit `sys_waitpid`

- returns -1, if there is no child process (pid==-1 or > 0)
- When there is a zombie child process whose process ID is pid, the child process is reclaimed normally, the child process’s pid is returned, and the exit code  is updated to exit_code
- returns -2 if  the child process has not exited . After the user library sees that it is -2, it further calls the sys_yield system call to make the parent process enter the waiting state
- Before returning, release the TCB  of the child process

---

#### Character Input Mechanism

```rust
pub fn sys_read(fd: usize, buf: *const u8, len: usize) -> isize {
    c=sbi::console_getchar(); ...}
  
```
- Currently only supports reading one character at a time
- Call the interface `console_getchar` provided by the sbi submodule to get input from the keyboard

---
#### POS that supports processes
- The relationship between process concept and process realization
- Process management mechanism
- Basic scheduling mechanism
- Can write Troodon OS
![bg right:30% 100%](figs/troodon.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### Summary

**Outline**

1. Lab Objectives and Steps
2. Code Structure
3. Application Design
4. Kernel Programming
- Linking and Loading Apps
- Core data structures
- Implementation of process management mechanism

![bg right:45% 100%](figs/process-os-detail.png)