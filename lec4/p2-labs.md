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

# Lecture 4 Multiprogramming and time-sharing multitasking
## Section 2 Practice: Multiprogramming and time-sharing multitasking operating system
<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

<br>
<br>

Spring 2023

---
**Outline**

### 1. Lab objectives and steps
- Lab objectives
- Lab steps
2. Multiprogramming batch OS design
3. Application Design
4. Sawtooth OS: Support application loading
5. Eoraptor OS: Support multiprogramming with cooperative scheduling  
6. Coelophysis OS: Time-sharing multitask OS
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
- Goal of BatchOS target
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

1. Lab objectives and steps
- Lab objectives
- **Lab steps**
2. Multiprogramming batch OS design
3. Application Design
4. Sawtooth OS: Support application loading
5. Eoraptor OS: Support multiprogramming with cooperative scheduling  
6. Coelophysis OS: Time-sharing multitask OS

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

1. Lab objectives and steps
### 2. Multiprogramming batch OS design
3. Application Design
4. Sawtooth OS: Support application loading
5. Eoraptor OS: Support multiprogramming with cooperative scheduling  
6. Coelophysis OS: Time-sharing multitask OS

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

1. Lab objectives and steps
2. Multiprogramming batch OS design
### 3. Application Design
4. Sawtooth OS: Support application loading
5. Eoraptor OS: Support multiprogramming with cooperative scheduling  
6. Coelophysis OS: Time-sharing multitask OS

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

1. Lab objectives and steps
2. Multiprogramming batch OS design
3. Application Design
### 4. Sawtooth OS: Support application loading
5. Eoraptor OS: Support multiprogramming with cooperative scheduling  
6. Coelophysis OS: Time-sharing multitask OS

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

4. Sawtooth OS: Support application loading
### 5. Eoraptor OS: Support multiprogramming with cooperative scheduling  
* Task switching
* Trap control flow switching
* Cooperative Scheduling
6. Coelophysis OS: Time-sharing multitask OS

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

<!--MZY finishes above -->


---
#### 任务运行状态
  - 在一个时间片内的应用执行情况
    - running
    - ready

```rust
pub enum TaskStatus {
    UnInit,
    Ready,
    Running,
    Exited,
}
```
![bg right:65% 100%](figs/more-task-multiprog-os-detail.png)

---

#### 任务切换
  - 从一个应用的执行过程切换到另外一个应用的执行过程
    - 暂停一个应用的执行过程（当前任务）
    - 继续另一应用的执行过程（下一任务）
![bg right:65% 100%](figs/more-task-multiprog-os-detail.png)


---

####  任务上下文（Task Context）
- 应用运行在某一时刻的**执行状态（上下文）**
  - 应用要暂停时，执行状态（上下文）可以被**保存**
  - 应用要继续时，执行状态（上下文）可以被**恢复** 
```rust
1// os/src/task/context.rs
2 pub struct TaskContext {
3    ra: usize,      //函数返回地址
4    sp: usize,      //用户栈指针
5    s: [usize; 12], //属于Callee函数保存的寄存器集s0~s11
6}
```


---

#### 任务上下文数据结构

```rust
1// os/src/task/context.rs
2 pub struct TaskContext {
3    ra: usize,
4    sp: usize,
5    s: [usize; 12],
6}
```
``` rust
// os/src/trap/context.rs
pub struct TrapContext {
    pub x: [usize; 32],
    pub sstatus: Sstatus,
    pub sepc: usize,
}
```

![bg right:60% 100%](figs/more-task-multiprog-os-detail.png)

---

#### 不同类型上下文
  - 函数调用上下文 
  - Trap上下文 
  - 任务（Task）上下文 

![bg right:60% 90%](figs/contexts-on-stacks.png)

---

#### 任务（Task）上下文 vs 系统调用（Trap）上下文

任务切换是来自两个不同应用在内核中的 Trap 控制流之间的切换
- 任务切换不涉及**特权级**切换；Trap切换涉及特权级切换；
- 任务切换只保存编译器约定的callee 函数应该保存的**部分寄存器**；而Trap切换需要保存所有通用寄存器；
- 任务切换和Trap切换都是**对应用是透明**的

---
#### 控制流
- 程序的控制流 (Flow of Control or Control Flow) --编译原理
    - 以一个程序的指令、语句或基本块为单位的**执行序列**。
- 处理器的控制流 --计算机组成原理
    - 处理器中程序计数器的**控制转移序列**。
---
#### 普通控制流：从应用程序员的角度来看控制流

- 控制流是应用程序员编写的应用程序的**执行序列**，这些序列是程序员预设好的。
- 称为 **普通控制流** (CCF，Common Control Flow)  

---
#### 异常控制流：从操作系统程序员的角度来看控制流

- 应用程序在执行过程中，如果发出系统调用请求，或出现外设中断、CPU 异常等情况，会出现前一条指令还在应用程序的代码段中，后一条指令就跑到操作系统的代码段中去了。
- 这是一种控制流的“**突变**”，即控制流脱离了其所在的执行环境，并产生**执行环境的切换**。 
- 这种“突变”的控制流称为 **异常控制流** (ECF, Exceptional Control Flow) 。


---

#### 控制流上下文（执行环境的状态）

从硬件的角度来看普通控制流或异常控制流的执行过程
* 从控制流起始的某条指令执行开始，指令可访问的所有物理资源的内容，包括自带的所有通用寄存器、特权级相关特殊寄存器、以及指令访问的内存等，会随着指令的执行而逐渐发生变化。

- 把控制流在执行完某指令时的物理资源内容，即确保下一时刻能继续正确执行控制流指令的物理/虚拟资源内容称为***控制流上下文 (Context)*** ，也可称为控制流所在执行环境的状态。

对于当前实践的OS，没有虚拟资源，而物理资源内容就是***通用寄存器/CSR寄存器***

---

#### 控制流上下文（执行环境的状态）

- 函数调用上下文
    - 函数调用（执行函数切换）过程中的控制流上下文
- 中断/异常/陷入上下文
    - 操作系统中处理中断/异常/陷入的切换代码时的控制流的上下文
- 任务（进程）上下文
    - 操作系统中任务（进程）执行相关切换代码时的控制流的上下文

---
**提纲**

... ...

4. 锯齿螈OS：支持应用程序加载
5. 始初龙OS：支持多道程序协作调度
* 任务切换
### Trap控制流切换
* 协作式调度
6. 腔骨龙OS：分时多任务OS

---

#### OS面临的挑战：任务切换
在分属不同任务的两个Trap控制流之间进行hacker级操作，即进行**Trap上下文切换**，从而实现任务切换。

- Trap上下文在哪？
- 任务上下文在哪？
- 如何切换任务？
- 任务切换应该发生在哪？
- 任务切换后还能切换回吗？

![bg right 95%](figs/task-context.png)

---
####  Trap控制流切换：暂停运行
- 一个特殊的函数`__switch()`
- 调用 `__switch()` 之后直到它返回前的这段时间，原 Trap 控制流 A 会先被暂停并被切换出去， CPU 转而运行另一个应用在内核中的 Trap 控制流 B 。
![bg right 95%](figs/task-context.png)


---
####  Trap控制流切换：恢复运行
- 一个特殊的函数` __switch()`
- 然后在某个合适的时机，原 Trap 控制流 A 才会从某一条 Trap 控制流 C （很有可能不是它之前切换到的 B ）切换回来继续执行并最终返回。

从实现的角度讲， `__switch()` 函数和一个普通的函数之间的核心差别仅仅是它会**换栈** 。
![bg right 95%](figs/task-context.png)


---
#### Trap控制流切换函数`__switch()`
![w:800](figs/switch.png)


---
#### Trap控制流切换过程：切换前的状态
阶段[1]：在 Trap 控制流 A 调用`__switch()`之前，A 的**内核栈**上只有 Trap 上下文和 Trap 处理函数的调用栈信息，而 B 是之前被切换出去的；
![bg right:55% 100%](figs/switch.png)


---
#### Trap控制流切换过程：保存A任务上下文
阶段 [2]：A 在 A 任务上下文空间在里面保存 **CPU 当前的寄存器快照**；
![bg right:55% 100%](figs/switch.png)

---
#### Trap控制流切换过程：恢复B任务上下文

阶段 [3]：读取 next_task_cx_ptr 指向的 B 任务上下文，恢复 ra 寄存器、s0~s11 寄存器以及 sp 寄存器。
* 这一步做完后， `__switch()` 才能做到一个函数跨两条控制流执行，即 通过**换栈**也就实现了控制流的切换 。
![bg right:55% 100%](figs/switch.png)


---
#### Trap控制流切换过程：执行B任务代码
阶段 [4]：当 CPU 执行 ret 汇编伪指令完成 `__switch()` 函数返回后，任务 B 可以从调用 `__switch()` 的位置继续向下执行。
* `__switch()`通过恢复 sp 寄存器换到了任务 B 的内核栈上，实现了控制流的切换，从而做到一个函数跨两条控制流执行。
![bg right:53% 100%](figs/switch.png)



---
#### `__switch()`的接口
```
 1 // os/src/task/switch.rs
 2 
 3 global_asm!(include_str!("switch.S"));
 4 
 5 use super::TaskContext;
 6 
 7 extern "C" {
 8     pub fn __switch(
 9         current_task_cx_ptr: *mut TaskContext,
10         next_task_cx_ptr: *const TaskContext
11     );
12 }
```


---
#### `__switch()`的实现

```
12 __switch:
13    # 阶段 [1]
14    # __switch(
15    #     current_task_cx_ptr: *mut TaskContext,
16    #     next_task_cx_ptr: *const TaskContext
17    # )
18    # 阶段 [2]
19    # save kernel stack of current task
20    sd sp, 8(a0)
21    # save ra & s0~s11 of current execution
22    sd ra, 0(a0)
23    .set n, 0
24    .rept 12
25        SAVE_SN %n
26        .set n, n + 1
27    .endr

```


---
#### `__switch()`的实现

```
28    # 阶段 [3]
29    # restore ra & s0~s11 of next execution
30    ld ra, 0(a1)
31    .set n, 0
32    .rept 12
33        LOAD_SN %n
34        .set n, n + 1
35    .endr
36    # restore kernel stack of next task
37    ld sp, 8(a1)
38    # 阶段 [4]
39    ret
```



---
**提纲**

... ...

4. 锯齿螈OS：支持应用程序加载
5. 始初龙OS：支持多道程序协作调度
* 任务切换
* Trap控制流切换
### 协作式调度
6. 腔骨龙OS：分时多任务OS

---

#### 任务控制块
操作系统管理控制进程运行所用的信息集合
```Rust
pub struct TaskControlBlock {
    pub task_status: TaskStatus,
    pub task_cx: TaskContext,
}
```
- 任务管理模块
```Rust
struct TaskManagerInner {
    tasks: [TaskControlBlock; MAX_APP_NUM],
    current_task: usize,
}
```
![bg right:50% 100%](figs/more-task-multiprog-os-detail.png)


---
#### 协作式调度
- `sys_yield`和`sys_exit`系统调用
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
#### 协作式调度

- `sys_yield`和`sys_exit`系统调用

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
#### 协作式调度

- `sys_yield`和`sys_exit`系统调用
```Rust
 fn run_next_task(&self) {
    ......
    unsafe {
        __switch(
            current_task_cx_ptr, //当前任务上下文
            next_task_cx_ptr,    //下个任务上下文
        );
    }
```

---
#### 第一次进入用户态
**Q:如何实现？**

如果能搞定，我们就实现了支持多道程序协作调度的始初龙操作系统

---
**提纲**

1. 实验目标和步骤
2. 多道批处理操作系统设计
3. 应用程序设计
4. 锯齿螈OS：支持应用程序加载
5. 始初龙OS：支持多道程序协作调度
### 6. 腔骨龙OS：分时多任务OS

---

#### 腔骨龙OS：分时多任务OS

三叠纪“腔骨龙”操作系统 – Timesharing OS 则可以**抢占**应用的执行，从而可以公平和高效地分时执行多个应用，提高系统的整体效率。

![bg right:52% 100%](figs/time-task-multiprog-os-detail.png)

---

#### 分时多任务操作系统的基本思路
- 设置时钟中断
- 在收到时钟中断后统计任务的使用时间片
- 在时间片用完后，切换任务
![bg right:58% 100%](figs/time-task-multiprog-os-detail.png)
---
#### 时钟中断与计时器
- 设置时钟中断
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
#### 抢占式调度

```rust
// os/src/trap/mod.rs trap_handler函数
......
match scause.cause() {
    Trap::Interrupt(Interrupt::SupervisorTimer) => {
        set_next_trigger();
        suspend_current_and_run_next();
    }
}
```

这样我们就实现了分时多任务的腔骨龙操作系统

---
### 小结
- 多道程序&分时共享多任务
- 协作式调度&抢占式调度
- 任务与任务切换
- 中断机制

---

### 课程实验一

* 创建实验提交仓库
    * 清华git访问入口：[UniLab Platform](https://lab.cs.tsinghua.edu.cn/unilab/home)
    * 学堂在线同学的[访问入口](https://www.yuque.com/xyong-9fuoz/qczol5/opl4y4#DiUQ0)(待补充)
        * rCore、uCore-RV、uCore-x86
* 实验任务
    * 第三章：多道程序与分时多任务 -> chapter3练习 -> 获取任务信息 -> 增加一个系统调用`sys_task_info()`
* 实验提交要求
    * 任务布置后的第11天（2022年10月16日）；
