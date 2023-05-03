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
## Section 3 Support thread/coroutine OS (TCOS)

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023
<!-- https://blog.aloni.org/posts/a-stack-less-rust-coroutine-100-loc/

https://stanford-cs242.github.io/f17/assets/projects/2017/kedero.pdf


https://cfsamson.gitbook.io/green-threads-explained-in-200-lines-of-rust/


https://tian-deng.github.io/posts/translation/rust/green_threads_explained_in_200_lines_of_rust/ -->
---
### Practice: TCOS
- **Evolution Goal**
- General idea
- History background
- Practical steps
- Software Architecture
- Related hardware
- Programming

![bg right:50% 100%](figs/thread-coroutine-os-detail.png)


---
<style scoped>
{
  font-size: 28px
}
</style>

### Practice: TCOS -- past goals
Improve performance, simplify development, enhance security, support data persistence, support application flexibility, and support inter-process interaction
- IPC OS: interprocess communication
- Filesystem OS: support for persistent data storage
- Process OS: Enhanced process management and resource management
- Address Space OS: Isolate the memory address space accessed by APP
- multiprog & time-sharing OS: Let APP share CPU resources
- BatchOS: isolate APP from OS, strengthen system security, and improve execution efficiency
- LibOS: Isolate APP from HW, simplifying the difficulty and complexity of applications accessing hardware

---
### Practice: TCOS -- Evolution Goals
Improve execution efficiency, support threads and coroutines
- Execution of multiple control flows (threads/coroutines) within a process
- Manage multiple control flows (threads/coroutines) in user mode or kernel mode


---
### Practice: TCOS
###  evolutionary goal
- Understand task-based process/thread/coroutine abstraction
- Understand the implementation and excution mechanism of process/thread/coroutine
- Can write an OS that supports threads/coroutines


<!-- Dakotaraptor is a theropod dromaeosaurid dinosaur that lived in the late Cretaceous period 67-65 million years ago. Own slender hind limbs to improve agility and running speed. It is almost entirely covered with feathers, and it may be gliding or other modes of action close to flight behavior. -->

![bg right:30% 100%](figs/dakotaraptor.png)

---
### Practice: TCOS
- Evolution goals
- **General idea**
     - **User thread managed by user mode**
- History background
- Practical steps
- Software Architecture
- Programming

![bg right:50% 100%](figs/task-abstracts.png)



---

### General idea 
- How to manage coroutines/threads/processes?
    - Task context
    - **User mode management**
    - Kernel mode management
![bg right:65% 100%](figs/task-abstracts.png)


---
### General idea 
- How to manage coroutines/threads/processes?
    - Task context
    - **User mode management**
    - Kernel mode management
![bg right:65% 100%](figs/thread-coroutine-os-detail.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### General idea 
**User thread managed by user state**
- The task control block of the user mode management thread
  - Similar to mission control block in Lec4
  - Managed by the user-mode Runtime
- Task Control Block
```
struct Task {
     id: usize,
     stack: Vec<u8>,
     ctx: TaskContext,
     state: State,
}
```
![bg right:40% 100%](figs/task-abstracts.png)

---
### Practical steps
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch8
```
Contains an application
```
user/src/bin/
├── stackful_coroutine.rs
```
---
### Practical steps
Execute this application
```
Rust user shell
>> stackful_coroutine
stackful_coroutine begin...
TASK 0 (Runtime) STARTING
TASK 1 STARTING
task: 1 counter: 0
TASK 2 STARTING
task: 2 counter: 0
TASK 3 STARTING
task: 3 counter: 0
TASK 4 STARTING
task: 4 counter: 0
...
```

---
<style scoped>
{
  font-size: 30px
}
</style>

### Programming
Simple user mode management multi-threaded application `stackful_coroutine.rs`
```rust
pub fn main() {
     let mut runtime = Runtime::new(); //Create a thread management subsystem
     runtime.init(); // Initialize the thread management subsystem
     runtime.spawn(|| { //Create a user mode thread
         println!("TASK 1 STARTING");
         let id = 1;
         for i in 0..4 {
             println!("task: {} counter: {}", id, i);
             yield_task(); //Actively yield the processor
         }
         println!("TASK 1 FINISHED");
     }); //... continue to create the 2nd~4th user mode thread
     runtime.run(); //Schedule and execute each thread
}
```


---
<style scoped>
{
  font-size: 30px
}
</style>

### Programming
Thread structure and execution state managed by user mode
```rust
struct Task { //thread control block
     id: usize,
     stack: Vec<u8>,
     ctx: TaskContext,
     state: State,
}
```
```rust
pub struct TaskContext { // thread context
     x1: u64, //ra: return address
     x2: u64, //sp
     ..., //s[0..11] registers
     nx1: u64, //new return address
}
```


---
### Programming
Thread structure and execution state managed by user mode
```rust
struct Task { //thread control block
     id: usize,
     stack: Vec<u8>,
     ctx: TaskContext,
     state: State,
}
```
```rust
enum State { //thread state
     Available,
     Running,
     Ready,
}
```


---
<style scoped>
{
  font-size: 30px
}
</style>

### Programming
**User state thread management runtime initialization**
Runtime::new() has three main steps:
- 1. Set the main thread: initialize the application main thread control block (TID is 0), and set its state to Running;
- 2. Set up the scheduling queue: initialize the thread control block vector (thread scheduling queue), add the application main thread control block and idle thread control block, and prepare for the subsequent thread operation;
- 3. Set the current running thread ID: Set the `current` value in the Runtime structure variable to 0, indicating that the current running thread is the application's main thread.

---
<style scoped>
{
  font-size: 30px
}
</style>

### Programming
**User state thread management runtime initialization**
The main job of Runtime::init() is to assign the address of the Rutime structure variable to the global variable `RUNTIME`. In this way, in the subsequent execution of the multi-threaded application, the Runtime structure variable will be found according to `RUNTIME`.

In the main() function of the application, the above two functions (new and init) will be called in sequence first to complete the initialization process of the thread management runtime. In this way, the running main thread with a TID of 0 can perform a series of tasks such as subsequent creation of threads on behalf of the thread runtime.


---
### Programming
**Thread creation for user mode management**
```rust
     pub fn spawn(&mut self, f: fn()) { // f function is thread entry
         let available = self
             .tasks.iter_mut() //traverse the tasks in the queue
             .find(|t| t.state == State::Available) //Find available tasks
             .expect("no available task.");
         let size = available. stack. len();
         unsafe {
             let s_ptr = available.stack.as_mut_ptr().offset(size as isize);
             let s_ptr = (s_ptr as usize & !7) as *mut u8; // The stack is aligned by 8 bytes
             available.ctx.x1 = guard as u64; //ctx.x1 is old return address
             available.ctx.nx1 = f as u64; //ctx.nx2 is new return address
             available.ctx.x2 = s_ptr.offset(-32) as u64; //cxt.x2 is sp
         }
         available.state = State::Ready; //Set the task to ready state
     }
}
```

---
### Programming
**Thread creation for user mode management**

- Find an idle thread control block with status Available in the thread vector
- Initialize the thread context of the idle thread's thread control block
     - `x1` register: old return address -- `guard` function address
     - `nx1` register: new return address -- input parameter `f` function address
     - `x2` register: new stack address -- available.stack+size
---
<style scoped>
{
  font-size: 30px
}
</style>

```rust
fn guard() {
     unsafe {
         let rt_ptr = RUNTIME as *mut Runtime;
         (*rt_ptr).t_return();
     };
}
fn t_return(&mut self) {
     if self. current != 0 {
         self.tasks[self.current].state = State::Available;
         self.t_yield();
     }
}
```
The `guard` function means that the `f` function we passed in (the body of the thread) has returned, which means our thread has finished, so we dereference our runtime and call t_return().

---
### Programming
<style scoped>
{
  font-size: 30px
}
</style>
**Thread switching managed by user mode**
When the application wants to switch threads, it will call the `yield_task function`, and complete the specific switching process through the `runtime.t_yield` function.`runtime.t_yield` This function mainly completes the functions:
- Find a  TCB with status Ready in the thread vector
- Change the state of the currently running thread to `Ready`, change the state of the new ready thread to `Running`, and set the `current` of runtime to the id of the control block of the new ready thread
- Call the function `switch` to complete the stack and context switching of the two threads;


---
### Programming

**Thread switching managed by user mode**
```rust
fn t_yield(&mut self) -> bool {
         ...
     self.tasks[pos].state = State::Running;
     let old_pos = self. current;
     self. current = pos;

     unsafe {
         switch(&mut self.tasks[old_pos].ctx, &self.tasks[pos].ctx);
     }
     ...
```



---
### Programming
**Thread switching managed by user mode**
  `Switch` mainly completes the work:
- Complete the switching of the current instruction pointer (PC);
- Complete the switching of the stack pointer;
- Complete the switching of the general register set;
---
### Programming
**Thread switching managed by user mode**
  Switch mainly completes the work:
```
unsafe fn switch(old: *mut TaskContext, new: *const TaskContext) {
     // a0: _old, a1: _new
     asm!("
         sd x1, 0x00(a0)
         ...
         sd x1, 0x70(a0)
         ld x1, 0x00(a1)
         ...
         ld t0, 0x70(a1)
         jr t0
     ...
```


---
### Programming
**Thread Execution & Scheduling Managed by User Mode**
```rust
     pub fn run(&mut self){
         while self. t_yield() {}
        println!("All tasks finished!");
     }
```



---
### Practice: TCOS
- Evolution goals
- **General idea**
     - **User thread managed by kernel mode**
- History background
- Practical steps
- Software Architecture
- Programming


![bg right:50% 100%](figs/task-abstracts.png)



---

### General idea 
- How to manage coroutines/threads/processes?
    - task context
    - User mode management
    - **Kernel mode management**

![bg right:65% 100%](figs/task-abstracts.png)


---
### General idea 
- How to manage coroutines/threads/processes?
    - Task context
    - User mode management
    - **Kernel mode management**
![bg right:65% 100%](figs/thread-coroutine-os-detail.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### General idea 
**User thread managed by kernel mode**
- The task control block of the user thread managed by the kernel mode
  - Similar to mission control block in Lec7
  - Refactoring: Multiple task control blocks representing threads in a process
```
pub struct ProcessControlBlockInner {
     pub tasks: Vec<Option<Arc<TaskControlBlock>>>,
     ...
}
```
![bg right:40% 100%](figs/task-abstracts.png)



---
### Practical steps
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch8
```
Contains several applications related to user threads managed by the kernel
```
user/src/bin/
├── threads.rs
├── threads_arg.rs
```
---
### Practical steps
Execute threads_arg this application
```
Rust user shell
>> threads_arg
aaa...bbb...ccc...aaa...bbb...ccc...
thread#1 exited with code 1
thread#2 exited with code 2
ccc...thread#3 exited with code 3
main thread exited.
...
```

---
<style scoped>
{
  font-size: 30px
}
</style>

### Programming
Simple kernel management multi-threaded application `threads_arg.rs`
```rust
fn thread_print(arg: *const Argument) -> ! { //The function body of the thread
     ...
     exit(arg.rc)
}
pub fn main() -> i32 {
     let mut v = Vec::new();
     for arg in args. iter() {
         v.push(thread_create( thread_print, arg )); //Create thread
     ...
     for tid in v. iter() {
         let exit_code = waittid(*tid as usize); //wait for the thread to end
     ...
}
```


---
<style scoped>
{
  font-size: 30px
}
</style>

### Programming -- system calls
During the running of a process, the process can create multiple threads belonging to the process, and each thread has its own thread identifier (TID, Thread Identifier). The prototype of the system call `thread_create` is as follows:
```rust
/// Function: The current process creates a new thread
/// Parameter: entry indicates the address of the entry function of the thread
/// Parameter: arg: represents a parameter of the thread
pub fn sys_thread_create(entry: usize, arg: usize) -> isize
```
- Creating a thread does not require creating a new address space
- There is no parent-child relationship between threads belonging to the same process


---
<style scoped>
{
  font-size: 30px
}
</style>

### Programming -- system calls
When a thread finished, it exits through the `exit` system call. It is necessary for the process/main thread to call `waittid` to reclaim its resources through this thread, so that the entire thread can be completely destroyed. The prototype of the `waittid` system call is as follows:
```rust
/// Parameter: tid means thread id
/// Return value: If the thread does not exist, return -1; if the thread has not exited, return -2; in other cases, return the exit code of the terminated thread
pub fn sys_waittid(tid: usize) -> i32
```
- The process/main thread is responsible for waiting for the threads it created (not the main thread) to end and reclaim their resources in the kernel through the use of `waittid`.
- If the process/main thread first calls the `exit` system call to exit, the entire process (including all threads it belongs to) will exit


---
<style scoped>
{
  font-size: 28px
}
</style>

### Programming -- system calls
After the introduction of the thread mechanism, important system calls related to processes, such as fork, exec, and waitpid, do not have changes in their interfaces, but require certain extensions in their intended functions.
- Split the parts related to processor execution in the previous process into threads
- Creating a process through fork actually means creating a separate main thread to use the processor, and creating a corresponding thread control block vector for creating a new thread in the future
- Relatively speaking, the two system calls `exec` and `waitpid` need relatively small changes, and they are still processed in the same way as the previous process
- Overall, these three system calls related to processes still maintain the semantics of existing process operations, and the introduction of threads did not cause major changes.

---
<style scoped>
{
  font-size: 30px
}
</style>

### Programming -- system calls

Question: "Does the forked child process copy multiple threads of the parent process?"

Choice A: To copy multiple threads; Choice B: No copy, only copy the thread currently executing fork; Choice C: Multi-threaded process execution fork is not supported

The current rcore tutorial chooses C, which simplifies the usage scenarios of the application, that is, the use of `fork` and `create_thread` (and thread-based semaphores, condition variables, etc.) will not appear at the same time. So if there is a `fork`, it is assumed that the application is a single-threaded process, so only the single-threaded structure is copied. Although this simplified design is an ostrich approach, it also avoids some complicated situations caused by allowing `fork` and `create_thread` to coexist...

---
<style scoped>
{
  font-size: 30px
}
</style>

### Programming -- system calls

Question: "Does the forked child process copy multiple threads of the parent process?"

for example:

Before `fork`, there are three threads: Main thread, thread A, thread B. Thread A gets a lock and executes in the critical section; Thread B is writing a file. If the Main thread executes  `fork`, if A is selected, both the Thread B of the child process and the Thread B of the parent process are writing a file. If B is selected, there is only Main Thread in the child process. When it wants to get the lock of ThreadA, this lock cannot be obtained (because ThreadA does not exist in the child process, and the lock cannot be released), and it will fall into continuous busy waiting.

---
<style scoped>
{
  font-size: 30px
}
</style>

### Programming -- Core Data Structures
In order to implement thread management on the basis of existing process management, it is necessary to improve the content and interface of some data structures. 
The basic idea : separate the processor-related parts of the process to form thread-related parts:
- TaskControlBlock: the core data structure of the thread
- TaskManager: the core data structure that manages the collection of threads
- Processor management structure Processor: used for thread scheduling and maintaining the processor status of threads


---
### Programming -- Core Data Structures
Thread control block
```rust
pub struct TaskControlBlock {
     pub process: Weak<ProcessControlBlock>, //The process control block to which the thread belongs
     pub kstack: KernelStack, // task (thread) kernel stack
     inner: UPSafeCell<TaskControlBlockInner>,
}
pub struct TaskControlBlockInner {
     pub res: Option<TaskUserRes>, //task (thread) user state resources
     pub trap_cx_ppn: PhysPageNum, //trap context address
     pub task_cx: TaskContext,//task (thread) context
     pub task_status: TaskStatus, //task (thread) status
     pub exit_code: Option<i32>,//Task (thread) exit code
}
```


---
<style scoped>
{
  font-size: 30px
}
</style>

### Programming -- Core Data Structures
Process control block
```rust
pub struct ProcessControlBlock {
     pub pid: PidHandle,
     inner: UPSafeCell<ProcessControlBlockInner>,
}
pub struct ProcessControlBlockInner {
     pub tasks: Vec<Option<Arc<TaskControlBlock>>>,
     pub task_res_allocator: RecycleAllocator,
     ...
}
```
  RecycleAllocator is an upgraded version of PidAllocator, which is a relatively generic resource allocator that can be used to allocate process identifiers (PIDs) and kernel stacks for threads.


 
---
<style scoped>
{
  font-size: 28px
}
</style>

### Program Design -- Management Mechanism
thread creation
When a system call `sys_thread_create` is issued during the execution of a process, the operating system needs to create a thread on the basis of the current process, that is, initialize each member variable in the thread control block, establish the relationship between the process and the thread, etc. The key elements include:
- Thread user-mode stack: ensure that threads in user mode can execute function calls normally
- Thread kernel state stack: ensure that the thread can execute function calls normally after falling into the kernel
- Thread springboard page: ensure that threads can correctly switch between user mode <–> kernel mode
- Thread context: the register information used by the thread, used for thread switching


---
### Program Design -- Management Mechanism
Thread creation
```rust
pub fn sys_thread_create(entry: usize, arg: usize) -> isize {
     // create a new thread
     let new_task = Arc::new(TaskControlBlock::new(...
     // add new task to scheduler
     add_task(Arc::clone(&new_task));
     // add new thread to current process
     let tasks = &mut process_inner.tasks;
     tasks[new_task_tid] = Some(Arc::clone(&new_task));
     *new_task_trap_cx = TrapContext::app_init_context( //Create trap/task context
         entry,
         new_task_res.ustack_top(),
         kernel_token(),
     ...
```

---
### Program Design -- Management Mechanism
Thread exits
- When a thread other than the main thread issues a `sys_exit` system call, the kernel will call the `exit_current_and_run_next` function to exit the current thread and switch to the next thread, but it will not cause the exit of the process it belongs to.
- When the main thread, that is, the process, issues this system call, when the kernel receives this system call, it will reclaim the resources of the entire process (including all threads it manages) and exit.


---
### Program Design -- Management Mechanism
Thread exits
```rust
pub fn sys_exit(exit_code: i32) -> ! {
     exit_current_and_run_next(exit_code); ...
pub fn exit_current_and_run_next(exit_code: i32) {
     let task = take_current_task().unwrap();
     let mut task_inner = task. inner_exclusive_access();
     drop(task_inner); //release thread resources
     drop(task); //release the thread control block
      if tid == 0 {
         // Release all thread resources of the current process
         // Release the resources of the current process
...
```




---
### Program Design -- Management Mechanism
Wait for thread to end
- If the thread corresponding to tid is found, try to collect the thread's exit code `exit_tid` , otherwise return an error (exit thread does not exist).
- If the exit code exists (meaning that the thread has indeed exited), clear the thread control block corresponding to this thread in the process (so far, the resources occupied by the thread are all cleared), otherwise return an error (the thread has not exited yet).

---
### Program Design -- Management Mechanism
```rust
pub fn sys_waittid(tid: usize) -> i32 {
     ...
     if let Some(waited_task) = waited_task {
         if let Some(waited_exit_code) = waited_task.....exit_code {
             exit_code = Some(waited_exit_code);
         }
     } else {
         return -1; // waited thread does not exist
     }
     if let Some(exit_code) = exit_code {
         process_inner.tasks[tid] = None; //dealloc the exited thread
         exit_code
     } else {
         -2 // waited thread has not exited
     }
```

---
### Program Design -- Management Mechanism
Privilege level switching and scheduling switching in thread execution

- The design and implementation of privilege level switching in thread execution is consistent with the task switching introduced in Lecture 4
- The scheduling switching process in thread execution is consistent with the process scheduling mechanism introduced in Lecture 7


---
### Summary
- User threads managed by user mode
- User threads managed by kernel mode
- Can write Dakotaraptor OS

![bg right 70%](figs/dakotaraptor.png)