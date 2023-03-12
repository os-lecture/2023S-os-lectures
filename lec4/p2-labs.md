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

# Lecture 4 Multiprogramming and Time-sharing Multi-task
## Section 2 Practice: Multiprogramming and Time-sharing Multi-task OS
<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

<br>
<br>

Spring 2023

---
**Outline**

### 1. Lab Objectives and Steps
- Lab objectives
- Lab steps
2. Multiprogramming Batch OS design
3. Application Design
4. Sawtooth OS: Support Application Loading
5. Eoraptor OS: Support Multiprogramming and Cooperative Scheduling
6. Coelophysis OS: Time-sharing Multi-task OS
<!--get some concept from https://www.tutorialandexample.com/types-of-operating-system  -->
---
<style scoped>
{
  font-size: 30px
}
</style>

#### Lab objectives

![bg right:43% 90%](figs/multiprog-os-detail.png)

- Goal of MultiprogOS
     - Further improve the overall performance and efficiency of multiple applications in the system
- Goal of BatchOS 
     - Isolate APP from OS to improve system security and efficiency
- Goal of LibOS
     - Isolate applications from hardwares, simplify the difficulty and complexity to access hardware from application side

---

#### Lab Requirements
- Understand
     - Cooperative scheduling and preemptive scheduling
     - Tasks and task switching
- Be able to finish
     - Multiprogramming OS
     - Time-sharing multitask OS

<!-- Sawtooth Archosaurus Coelophysis -->

![bg right:45% 70%](figs/ch3-oses.png)

---
#### Structure of Multiprogramming systems

![bg 55%](figs/multiprog-os-detail.png)

---
#### General idea
- Compilation: The application and kernel will be compiled independently and merged into one image
- Compilation: Different applications require different start addresses
- Construction: System call service request interface, process management and initialization
- Construction: Process Control Block, process context/state management
- Run: Privilege switching, process and OS switching
- Run: Switch the process actively/passively through system calls/interrupts

---
#### History & Background

- 1961 British Leo III computer
- Supports **loading** multiple different programs into memory and running **sequentially** from the first one
<!-- - J. Lyons & Co. for commercial transactions

J. Lyons & Co. is a British restaurant chain, food manufacturing and hotel group founded in 1884. -->

<!-- https://baike.baidu.com/item/EDSAC/7639053
Electronic Delay Storage Automatic Calculator (English: Electronic Delay Storage Automatic Calculator, EDSAC) is an early British computer. In 1946, Professor Maurice Wilkes of the Mathematics Laboratory of the University of Cambridge and his team were inspired by von Neumann's First Draft of a Report on the EDVAC, and designed and built EDSAC based on EDVAC. It was officially put into operation on May 6, 1949. It is the world's first stored-program electronic computer that actually runs.
It was EDSAC that also encountered difficulties in project implementation: not technology, but lack of funds. At the crucial moment, Wilkes successfully persuaded J. Lyons & Co. ． The boss invested in the project and finally brought the plan back to life. On May 6, 1949, EDSAC was successfully tested for the first time. It read a program to generate a square table from the tape and executed it, and printed the result correctly. In return for the investment, LyOHS obtained the right to mass-produce EDSAC, which is the LEO computer (Lyons Electronic Office) that was officially put on the market in 1951, which is generally considered to be the first commercialized computer model in the world, so This has also become an interesting fact in the history of computer development: the first manufacturer to produce a commercial computer turned out to be a bakery. Lyons company later became part of the famous "International Computer Co., Ltd.", or ICL, in the UK.
-->

![bg right 100%](figs/multiprog-os.png)

---
**Outline**

1. Lab Objectives and Steps
- Lab objectives
- **Lab steps**
2. Multiprogramming Batch OS design
3. Application Design
4. Sawtooth OS: Support Application Loading
5. Eoraptor OS: Support Multiprogramming and Cooperative Scheduling
6. Coelophysis OS: Time-sharing Multi-task OS
---

#### Lab steps (based on BatchOS)
- Modify the link script of APPs (custom start address)
- Load & execute applications
- Switch tasks

![bg right 100%](figs/multiprog-os.png)

---

#### Three applications are executed alternately
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch3-coop
```
Contains three applications, everyone humbly **executes alternately**
```
user/src/bin/
├── 00write_a.rs # Display AAAAAAAAAA string 5 times
├── 01write_b.rs # Display BBBBBBBBBB string twice
└── 02write_c.rs # Display CCCCCCCCCC string 3 times
```

---

#### Execution result
```
[RustSBI output]
[kernel] Hello, world!
AAAAAAAAAAA [1/5]
BBBBBBBBBB [1/2]
....
CCCCCCCCCC [2/3]
AAAAAAAAAAA [3/5]
Test write_b OK!
[kernel] Application exited with code 0
CCCCCCCCCC [3/3]
...
[kernel] Application exited with code 0
[kernel] Panicked at src/task/mod.rs:106 All applications completed!
```


---
**Outline**

1. Lab Objectives and Steps
### 2. Multiprogramming Batch OS design
3. Application Design
4. Sawtooth OS: Support Application Loading
5. Eoraptor OS: Support Multiprogramming and Cooperative Scheduling
6. Coelophysis OS: Time-sharing Multi-task OS

---

#### Software Architecture

![bg 55%](figs/multiprog-os-detail.png)

---

#### Code Structure: Application
Build application
```
└── user
     ├── build.py (Add: use build.py to build applications so that the physical address space they occupy are disjoint)
     ├── Makefile (Modify: use build.py to build applications)
     └── src (various applications)
```


---

#### Code structure: Complete task management

Improve OS: ``Loader`` module loads and executes programs
```
├── os
│ └── src
│ ├── batch.rs (Delete: This is divided into two submodules, loader and task)
│ ├── config.rs (Add: Save some kernel configurations)
│ ├── loader.rs (Add: Load the application into memory and manage it)
│ ├── main.rs (Modify: Modify the main function)
│ ├── syscall (Modify: Add some syscalls)
```

---

#### Code structure: Process switching

Improve OS: `TaskManager` module manages/switches execution of programs
```
├── os
│ └── src
│ ├── task (Add: task sub-module is mainly responsible for task management)
│ │ ├── context.rs (Introduce Task context  `TaskContext` )
│ │ ├── mod.rs (Global task manager and provides some interfaces to other modules)
│ │ ├── switch.rs (Interprets the assembly code of task switching as the Rust interface __switch)
│ │ ├── switch.S (Assembly code for task switching)
│ │ └── task.rs (Definition of task control block `TaskControlBlock` and task status `TaskStatus`)
```

---

**Outline**

1. Lab Objectives and Steps
2. Multiprogramming Batch OS design
### 3. Application Design
4. Sawtooth OS: Support Application Loading
5. Eoraptor OS: Support Multiprogramming and Cooperative Scheduling
6. Coelophysis OS: Time-sharing Multi-task OS
---

#### Application Project Structure

No updates.  There are id numbers in App names.

```
user/src/bin/
├── 00write_a.rs # Display AAAAAAAAAA string 5 times
├── 01write_b.rs # Display BBBBBBBBBB string twice
└── 02write_c.rs # Display CCCCCCCCCC string 3 times
```
---

#### Memory layout of applications

- Since different applications will be loaded to different locations, the **``BASE_ADDRESS``** in their linker script linker.ld are all different.
- Write a script customization tool `build.py`, which customizes its own link script for each application
    - **``Application start address = base address + number * 0x20000``**

---

#### Yield system call

```rust
//00write_a.rs
fn main() -> i32 {
     for i in 0..HEIGHT {
         for _ in 0..WIDTH {
             print!("A");
         }
         println!(" [{}/{}]", i + 1, HEIGHT);
         yield_(); //yield the CPU
     }
     println!("Test write_a OK!");
     0
}
```

---

#### Yield system call

- Apps are **mutually unaware**
- Application needs to **actively yield** the processor
- Needs to be implemented via a **new syscall**
   - **``const SYSCALL_YIELD: usize = 124;``**



---

#### Yield system call

``` Rust
const SYSCALL_YIELD: usize = 124;
pub fn sys_yield() -> isize {
     syscall(SYSCALL_YIELD, [0, 0, 0])
}
pub fn yield_() -> isize {
     sys_yield()
}
```


---
**Outline**

1. Lab Objectives and Steps
2. Multiprogramming Batch OS design
3. Application Design
### 4. Sawtooth OS: Support Application Loading
5. Eoraptor OS: Support Multiprogramming and Cooperative Scheduling
6. Coelophysis OS: Time-sharing Multi-task OS
---
#### Sawtooth OS: Support application loading

The Permian "sawtooth OS" supports multiple applications residing in memory to form a multiprogramming operating system – Multiprog OS;
  
![bg right:50% 100%](figs/multiprog-os-detail.png)

---

#### MultiProgramming Loading
- Apps are loaded in different ways.
- All applications are loaded into memory when kernel is being initialized
- To avoid overwriting, they need to be **loaded to different physical addresses**


---

#### MultiProgramming Loading

```Rust
fn get_base_i(app_id: usize) -> usize {
     APP_BASE_ADDRESS + app_id * APP_SIZE_LIMIT
}

let base_i = get_base_i(i);
// load app from data section to memory
let src = (app_start[i]..app_start[i + 1]);
let dst = (base_i.. base_i+src. len());
dst.copy_from_slice(src);
```


---

#### Program execution

- Execution timing
   - When the initialization of the multiprogramming is complete
   - When an application ends or an error occurs

- How to switch?
   - Call the run_next_app function to **switch** to the first/next application
  

---

#### Switch to the next program

   - From kernel mode to user mode
   - From user mode to kernel mode


---

#### Switch to the next program

   - Jump to application i (The entry point of application i is `entry(i)`)
   - Switch to the user stack stack(i)

![bg right:55% 90%](figs/kernel-stack.png)


---

#### Program Execution

Now we complete "Sawtooth OS", which supports **loading applications into memory**
![bg right:57% 95%](figs/jcy-multiprog-os-detail.png)

---
**Outline**

...

4. Sawtooth OS: Support Application Loading
### 5. Eoraptor OS: Support Multiprogramming and Cooperative Scheduling 
* Task switching
* Trap control flow switching
* Cooperative Scheduling
6. Coelophysis OS: Time-sharing Multi-task OS

---

#### Support multiprogramming cooperative scheduling

Cooperative multi-programming: One application **actively yields** the CPU and **switches** to another application to continue execution, thereby improving the overall efficiency of the system;

![bg right:50% 90%](figs/more-task-multiprog-os-detail.png)

---

#### Task switching

![bg 60%](figs/more-task-multiprog-os-detail.png)

---

#### Process

- **Process**: A **dynamic execution** process on a **data collection** of a program with certain **independent functions**. Also known as **Task**.
![bg right 100%](figs/task-multiprog-os-detail.png)


---

#### Time slice

- The execution/idle slice in a time slice of application execution is called "computing task slice" or "idle task slice", collectively called **task slice**
![bg right 100%](figs/task-multiprog-os-detail.png)



---
#### Task Execution Status
   - Application execution within a time slice
     - Running
     - Ready

```rust
pub enum TaskStatus {
     UnInit,
     Ready,
     Running,
     Exited,
}
```
![bg right:50% 100%](figs/more-task-multiprog-os-detail.png)

---

#### Task Switching
   - Switch from the execution process of one application to the execution process of another application
     - Pause the execution of an application (current task)
     - Continue the execution of another application (next task)
![bg right:50% 100%](figs/more-task-multiprog-os-detail.png)


---

#### Task Context
- The **execution state (context)** of the application at a certain moment
   - Execution state (context) can be **saved** when the application is to pause
   - Execution state (context) can be **restored** when the application is to continue
```rust
1//os/src/task/context.rs
2 pub struct TaskContext {
3 ra: usize, //function return address
4 sp: usize, //user stack pointer
5 s: [usize; 12], //belongs to the register set s0~s11 saved by the Callee function
6}
```


---

#### Data Structure of the Task Context 

```rust
1//os/src/task/context.rs
2 pub struct TaskContext {
3 ra: usize,
4 sp: usize,
5 s: [usize; 12],
6}
```
```rust
// os/src/trap/context.rs
pub struct TrapContext {
     pub x: [usize; 32],
     pub sstatus: Sstatus,
     pub sepc: usize,
}
```

![bg right:60% 100%](figs/more-task-multiprog-os-detail.png)

---

#### Different Types of Context
   - Function call context
   - Trap context
   - Task context

![bg right:60% 90%](figs/contexts-on-stacks.png)

---

#### Task Context vs Trap Context

A task switch represents the switch between Trap control flows in the kernel from two different applications
- Task switching does not involve **privilege level** switching; while Trap switching involves privilege level switching
- Task switching only saves the **partial registers** that the compiler agrees that callee function should save; while Trap switching needs to save all general-purpose registers
- Both Task switching and Trap switching are **transparent to the applications**

---
#### Control Flow
- Control flow of the program -- compilation principle
     - The **execution sequence** in units of instructions, statements or basic blocks of a program
- Control flow of the processor -- principle of computer composition
     - The **control transfer sequence** of the program counter in the processor
---
#### Common Control Flow: control flow from the perspective of application programmer

- Control flow is the **execution sequence** of an application program written by the application programmer, and these sequences are preset by the programmer
- Called **Common control flow** (CCF)

---
#### Exceptional Control Flow: control flow from the perspective of OS programmer

- During the execution of the application program, if a system call request is issued, a peripheral interrupt or CPU exception occurs, the previous instruction is still in the code segment of the application program, and the latter instruction will run into the code segment of the OS
- This is a "**mutation**" of the control flow, that is, the control flow breaks away from its execution environment and produces a **switch of the execution environment**
- This "mutant" control flow is called **Exceptional Control Flow** (ECF)


---
<style scoped>
{
  font-size: 27px
}
</style>

#### Control Flow Context (the State of the Execution Environment)

The execution process of common control flow or exceptional control flow, from the perspective of hardware
* Starting from the execution of an instruction at the beginning of the control flow, the contents of all physical resources accessible by the instruction, including all general-purpose registers, privilege-related special registers, and memory accessed by the instruction, will gradually change with the execution of the instruction

- The physical resource content of the control flow after executing a certain instruction, that is, the physical/virtual resource content that ensures that the control flow instruction can continue to be executed correctly at the next moment is called **Control Flow Context (Context)**, it can also be referred to as the state of the execution environment in which the control flow occurs

For the currently practiced OS, there is no virtual resource, and the content of the physical resource is **general register/CSR register**

---
<style scoped>
{
  font-size: 33px
}
</style>

#### Control Flow Context (the State of the Execution Environment)

- Function call context
     - The control flow context during a function call (executing a function switch)
- Interrupt/exception/trap context
     - The control flow context when handling interrupt/exception/trap switching code in the OS
- Task (process) context
     - The control flow context when tasks (processes) in the OS execute related switching codes

---
<style scoped>
{
  font-size: 30px
}
</style>
**Outline**

...

4. Sawtooth OS: Support Application Loading
5. Eoraptor OS: Support Multiprogramming and Cooperative Scheduling  
* Task Switching
### Trap control flow switching
* Cooperative Scheduling 
6. Coelophysis OS: Time-sharing  Multi-task OS

---
<style scoped>
{
  font-size: 30px
}
</style>

#### The challenge of OS: task switching
Perform hacker-level operations between two Trap control flows belonging to different tasks, that is, perform **Trap context switching** to achieve task switching.

- Where is the Trap context?
- Where is the Task context?
- How to switch tasks?
- Where should task switching occur?
- Can the task switch back after switching?

![bg right:45% 95%](figs/task-context.png)

---
<style scoped>
{
  font-size: 32px
}
</style>
#### Trap control flow switching: pause operation
- a special function `__switch()`
- During the period after calling `__switch()` until it returns, the original Trap control flow A will be suspended and switched out, and the CPU will run another Trap control flow applied in the kernel
![bg right 95%](figs/task-context.png)


---
<style scoped>
{
  font-size: 29px
}
</style>

#### Trap control flow switching: resume operation
- A special function `__switch()`
- Then at an appropriate moment, the original Trap control flow A will switch back from a certain Trap control flow C (probably not the B it switched to before) to continue execution and finally return

From the  perspective of implementation, the core difference between a `__switch()` function and a normal function is that it **switches the stack**
![bg right:45% 95%](figs/task-context.png)


---
#### Trap control flow switching function `__switch()`
![w:800](figs/switch.png)


---
#### Trap control flow switching process: state before switching
Stage [1]: Before the Trap control flow A calls `__switch()`, A’s **kernel stack** only has the Trap context and the call stack information of the Trap processing function, and B was switched out before
![bg right:55% 100%](figs/switch.png)


---
#### Trap control flow switching process: save task context A
Stage [2]: A saves the **current register snapshot of CPU** in the task context A's space
![bg right:55% 100%](figs/switch.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Trap control flow switching process: restore task context B

Phase [3]: Read the task context B pointed to by next_task_cx_ptr, restore the ra register, s0~s11 register and sp register
* After this step, `__switch()` can execute a function across two control flows, that is, the switching of the control flow is realized by **switching the stack**
![bg right:50% 100%](figs/switch.png)


---
<style scoped>
{
  font-size: 26px
}
</style>
#### Trap control flow switching process: execute task code B 
Stage [4]: After the CPU executes the ret assembly pseudo-instruction and completes the return of the `__switch()` function, task B can continue to execute downwards from the position where `__switch()` was called
* `__switch()` is switched to the kernel stack of task B by restoring the sp register, realizing the switching of control flow, so that a function can be executed across two control flows
![bg right:53% 100%](figs/switch.png)



---
#### Interface of `__switch()`
```
  1 // os/src/task/switch.rs
  2 
  3 global_asm!(include_str!("switch.S"));
  4
  5 use super::TaskContext;
  6
  7 extern "C" {
  8 pub fn __switch(
  9 current_task_cx_ptr: *mut TaskContext,
10 next_task_cx_ptr: *const TaskContext
11);
12}
```


---
#### Implementation of `__switch()`

```
12 __switch:
13 # stage [1]
14 # __switch(
15 # current_task_cx_ptr: *mut TaskContext,
16 # next_task_cx_ptr: *const TaskContext
17#)
18 # stage [2]
19 # save kernel stack of current task
20 sd sp, 8(a0)
21 # save ra & s0~s11 of current execution
22 sd ra, 0(a0)
23.set n, 0
24 .rept 12
25 SAVE_SN %n
26. set n, n + 1
27.endr

```


---
#### Implementation of `__switch()`

```
28 # stage [3]
29 # restore ra & s0~s11 of next execution
30 ld ra, 0(a1)
31. set n, 0
32 .rept 12
33 LOAD_SN %n
34.set n, n + 1
35.endr
36 # restore kernel stack of next task
37 ld sp, 8(a1)
38 # stage [4]
39 ret
```



---
<style scoped>
{
  font-size: 30px
}
</style>
**Outline**

...

4. Sawtooth OS: Support Application Loading
5. Eoraptor OS: Support Multiprogramming and Cooperative Scheduling 
* Task Switching
* Trap Control Flow Switching
### Cooperative Scheduling
6. Coelophysis OS: Time-sharing Multi-task OS

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Task Control Block
The operating system manages the collection of information used to control the running of processes
```Rust
pub struct TaskControlBlock {
     pub task_status: TaskStatus,
     pub task_cx: TaskContext,
}
```
- Task management module
```Rust
struct TaskManagerInner {
     tasks: [TaskControlBlock; MAX_APP_NUM],
     current_task: usize,
}
```
![bg right:50% 100%](figs/more-task-multiprog-os-detail.png)


---
#### Cooperative Scheduling
- `sys_yield` and `sys_exit` system calls
```rust
pub fn sys_yield() -> isize {
     suspend_current_and_run_next();
     0
}
pub fn sys_exit(exit_code: i32) -> ! {
     println!("[kernel] Application exited with code {}", exit_code);
     exit_current_and_run_next();
     panic!("Unreachable in sys_exit!");
}
```

---
#### Cooperative Scheduling

- `sys_yield` and `sys_exit` system calls

```rust
// os/src/task/mod.rs

pub fn suspend_current_and_run_next() {
     mark_current_suspended();
     run_next_task();
}

pub fn exit_current_and_run_next() {
     mark_current_exited();
     run_next_task();
}
```


---
#### Cooperative Scheduling

- `sys_yield` and `sys_exit` system calls
```Rust
  fn run_next_task(&self) {
      …
     unsafe {
         __switch(
             current_task_cx_ptr, //current task context
             next_task_cx_ptr, //Next task context
         );
     }
```

---
#### Enter user mode for the first time
**Q: How to achieve it?**

If it can be done, we will realize the Eoraptor OS that supports multiprogramming and cooperative scheduling

---
**Outline**

1. LAB Objectives and Steps
2. Multiprogramming Batch OS design
3. Application Design
4. Sawtooth OS: Support Application Loading
5. Eoraptor OS: Support Multiprogramming and Cooperative Scheduling  
### 6. Coelophysis OS: Time-sharing Multi-task OS

---

#### Coelophysis OS: time-sharing multi-task OS

The Triassic "Coelophysis" OS – Time-sharing OS can completely preempt the execution of applications, so that multiple applications can be executed in a fair and efficient time-sharing manner, and the overall efficiency of the system can be improved

![bg right:50% 100%](figs/time-task-multiprog-os-detail.png)

---

#### The basic idea of time-sharing multi-task OS
- Set clock interrupt
- Count the time slice used by the task after receiving the clock interrupt
- switch tasks after the time slice is used up
![bg right:50% 100%](figs/time-task-multiprog-os-detail.png)
---
#### Clock Interrupts and Timers
- set clock interrupt
```rust
// os/src/sbi.rs
pub fn set_timer(timer: usize) {
      sbi_call(SBI_SET_TIMER, timer, 0, 0);
  }
// os/src/timer.rs
pub fn set_next_trigger() {
     set_timer(get_time() + CLOCK_FREQ / TICKS_PER_SEC);
}
pub fn rust_main() -> ! {
     trap::enable_timer_interrupt();
     timer::set_next_trigger();
}
```


---
#### Preemptive Scheduling

```rust
// os/src/trap/mod.rs trap_handler function
 …
match cause. cause() {
     Trap::Interrupt(Interrupt::SupervisorTimer) => {
         set_next_trigger();
         suspend_current_and_run_next();
     }
}
```

In this way, we have realized the time-sharing and multi-task Coelophysis OS

---
### Summary
- Multiprogramming & time-sharing multitasking
- Cooperative Scheduling & Preemptive Scheduling
- Task and task switching
- Interrupt mechanism

---
<style scoped>
{
  font-size: 30px
}
</style>

### Course LAB 1

* Create an LAB submission repository
     * Tsinghua git access entrance: [UniLab Platform](https://lab.cs.tsinghua.edu.cn/unilab/home)
     * [Access Entrance] (https://www.yuque.com/xyong-9fuoz/qczol5/opl4y4#DiUQ0) of XuetangX students (to be added)
         * rCore, uCore-RV, uCore-x86
* LAB tasks
     * Chapter 3: Multiprogramming and time-sharing multitasking -> chapter3 practice -> get task information -> add a system call `sys_task_info()`
* LAB submission requirements
     * The 11th day after the task is assigned (October 16, 2022);