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

# Lecture 10 Inter Process Communication(IPC)

## Section 2.  IPC-Supporting OS(IPC OS, IOS)

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023

---
<style scoped>
{
  font-size: 28px
}
</style>

**Outline**

### 1. Lab arrangement
- Lab objectives
- Overall idea
- History background
- Practical steps
2. Code structure
3. Design and Implementation of Pipes
4. Design and Implementation of Signals

![bg right:50% 95%](figs/ipc-os-detail-2.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Past Lab Goals

Improve performance, simplify development, enhance security, and support data persistence.
- Filesystem OS: support for persistent data storage
- Process OS: enhance process and resource management
- Address Space OS: isolate the memory address space accessed by apps
- Multiprog & time-sharing OS: allow apps to share CPU resources
- BatchOS: isolate apps from the OS, enhance system security, and improve execution efficiency
- LibOS: isolate apps from the hardware, simplify the difficulty and complexity of accessing hardware by applications.

---
<style scoped>
{
  font-size: 28px
}
</style>

#### Lab Objectives
Support application flexibility and IPC
- Expand file abstractions: Pipe, Stdout, Stdin
- Exchange data between processes in file format
- Input and output through serial port in file format
- Signal implementation of interprocess asynchronous notification mechanism
- System call number: 11 --> 17
  - Pipes: 2, used for data transfer
  - Signals: 4, used for notification

![bg right:35% 95%](figs/ipc-os-detail-2.png)

---

#### Lab Requirements

- Understand the file abstraction
- Understand the design and implementation of IPC mechanism
   - pipe
   - signal
- Be able to write an IPC-enabled OS.

<!-- Velociraptor possesses stealth and cunning. Velociraptor is smarter, has teamwork ability, and is good at teamwork Operating system -->

![bg right:40% 80%](figs/velociraptor.png)



---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

1. Lab arrangement
- Lab objectives
### Overall idea
- History background
- Practical steps
2. Code structure
3. Design and Implementation of Pipes
4. Design and Implementation of Signals

![bg right:40% 95%](figs/ipc-os-detail-2.png)

---

#### Issues to consider when implementing pipes:

- What is a pipe?
- How to access the pipe?
- How are pipes managed?

![w:1100](figs/pipe-fds.png)

---
<style scoped>
{
  font-size: 28px
}
</style>

#### Understand Pipes

**A pipe is a block of memory in the kernel**
- Sequentially writes/reads a byte stream

**Pipes can be abstracted as files**
- Process contains the pipe file descriptor
   - The interface of the `File` Trait for pipes
   - read/write
- The system call for the application to create the pipeline
   - `sys_pipe`

![bg right:40% 100%](figs/pipe-fds-close.png)


---

#### Pipe sample program (user mode)
```rust
...//usr/src/bin/pipetest.rs
static STR: &str = "Hello, world!" //Global string variable
pub fn main() -> i32 {
     let mut pipe_fd = [0usize; 2]; // fd array containing two elements
     pipe(&mut pipe_fd); // create pipe
     if fork() == 0 { // child process, read from parent
         close(pipe_fd[1]); // close write_end
         let mut buffer = [0u8; 32]; //A byte array containing 32 bytes
         let len_read = read(pipe_fd[0], &mut buffer) as usize; //read pipe
     } else { // parent process, write to child
         close(pipe_fd[0]); // close read end
         write(pipe_fd[1], STR.as_bytes()); //write pipe
         let mut child_exit_code: i32 = 0;
         wait(&mut child_exit_code); //The parent process waits for the child process to finish
     }
...
```
---


#### Relationship between pipes and processes
- `pipe` is one of the resources of the process control block

![bg right:60% 95%](figs/process-os-key-structures-file-ipc.png)

---

#### Issues to consider when implementing signals
- what are signals?
- How to use the signal?
- How to manage signals?

![bg right:60% 90%](figs/signal.png)

<!-- Those things about linux signal http://blog.chinaunix.net/uid-24774106-id-4061386.html -->

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Understand Signals
A `signal` is a software interrupt  in the kernel that notifies an application.

**Preparation Phase**
- Set the integer value of `signal`
- Establish a routine `signal_handler` to respond to a certain `signal` value

**Execution phase**
- Send a signal to a process, interrupt the current execution of the process, and switch to `signal_handler` for execution

![bg right:40% 100%](figs/signal.png)



---

#### Signal sample program (user mode)
```rust
...//usr/src/bin/sig_simple.rs
fn func() { //signal_handler
     println!("user_sig_test succsess");
     sigreturn(); //return to the position before signal handling and continue execution
}
}
pub fn main() -> i32 {
     let mut new = SignalAction::default(); //new signal configuration
     let old = SignalAction::default(); //Old signal configuration
     new.handler = func as usize; //Set up a new signal handling routine
     if sigaction(SIGUSR1, &new, &old) < 0 { //setup signal_handler
         panic!("Sigaction failed!");
     }
     if kill(getpid() as usize, SIGUSR1) <0{ //send SIGUSR1 to itself
       ...
     }
...
```

---

#### Relationship between Signals and Processes
- `signal` is one of the resources of the process control block

![bg right:60% 90%](figs/process-os-key-structures-file-ipc.png)



---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

1. Lab arrangement
- Lab objectives
- Overall idea
### History background
- Practical steps
2. Code structure
3. Design and Implementation of Pipes
4. Design and Implementation of Signals

![bg right:40% 95%](figs/ipc-os-detail-2.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Pipes: The Most Remarkable Invention in Unix

- The concept of pipes was introduced by Douglas McIlroy of Bell Laboratories in an internal memorandum he wrote in 1964, in which he proposed the idea of connecting and twisting together multiple programs "like garden hoses" so that data could flow between different programs.

- Around the second half of 1972, Ken Thompson, inspired by McIlroy's idea of pipes, quickly implemented the pipe mechanism in UNIX.
 
![bg right:35% 90%](figs/douglas-mcllroy.jpg)

<!-- Douglas McIlroy (born 1932) is a mathematician, engineer and programmer. Since 2007 he is an adjunct professor of computer science at MIT and received his Ph.D.
McIlroy is best known for the original Unix pipeline implementation, software component parts and several Unix tools such as computer virus, diff, sort, join, talk, and tr. -->


---
#### Signal: Error-Prone Software Interrupts in Unix

Signals have been present in Unix since the first version, but they were a bit different from what we know today and required different system calls to catch different types of signals. Starting with Version 4, improvements were made so that all signals could be caught with a single system call.

![bg right:35% 90%](figs/douglas-mcllroy.jpg)

<!-- https://venam.nixers.net/blog/unix/2016/10/21/unix-signals.html
https://unix.org/what_is_unix/history_timeline.html
https://en.wikipedia.org/wiki/Signal_(IPC) -->

---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

1. Lab arrangement
- Lab objectives
- Overall idea
- History background
### Practical steps
2. Code structure
3. Design and Implementation of Pipes
4. Design and Implementation of Signals

![bg right:40% 95%](figs/ipc-os-detail-2.png)

---

#### Practical steps
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch7
cd os
make run
```


---
#### Reference output
```
[RustSBI output]
...
filetest_simple
fantastic_text
***************/
Rust user shell
>>
```
After the operating system boots up its `shell`, users can execute applications by typing in the application name in the `shell`.

---
#### Test case: pipetest
Here, we run the test case `pipetest` of this chapter:
```
>> pipe test
Read OK, child process exited!
pipe test passed!
>>
```
This application passes the string `"Hello, world!"` between its parent and child processes using a pipe.

---
#### Test case: sig_simple

Here we run the test case `sig_simple` of this chapter:

```
>> sig_simple
signal_simple: sigaction
signal_simple: kill
user_sig_test succsess
signal_simple: Done
>>
```
This application establishes a signal handler `func` for the `SIGUSR1` signal, and then sends a signal `SIGUSR1` to itself through `kill`, and finally `func` will be called.


---

**Outline**

1. Lab arrangement
### 2. Code structure
3. Design and Implementation of Pipes
4. Design and Implementation of Signals

![bg right:40% 95%](figs/ipc-os-detail-2.png)

---

#### User Code Structure
```
└── user
     └── src
         ├── bin
         │ ├── pipe_large_test.rs (new: large data pipe transfer)
         │ ├── pipetest.rs (new: parent-child process pipe transfer)
         │ ├── run_pipe_test.rs (new: pipe tests)
         │ ├── sig_tests.rs (new: signal mechanism tests)
         │ ├── sig_simple.rs (new: sending signal to itself)
         │ ├── sig_simple2.rs (new: parent process sending signal to child process)
         ├── lib.rs (two new system calls: sys_close/sys_pipe/sys_sigaction/sys_kill...)
         └── syscall.rs (two new system calls: sys_close/sys_pipe/sys_sigaction/sys_kill...)
```

---
#### Kernel code structure
```
├── fs (Added: file system sub-module fs)
│   ├── mod.rs (contains abstract File Trait for files that are open and can be read/written by processes)
│   ├── pipe.rs (implements the first branch of the File Trait - pipes for inter-process communication)
│   └── stdio.rs (implements the second branch of the File Trait - standard input/output)
├── mm
│   └── page_table.rs (Added: UserBuffer abstraction for application address space buffer and its iterator implementation)
├── syscall
│   ├── fs.rs (Modified: adjusted sys_read/write implementation, added sys_close/pipe)
│   ├── mod.rs (Modified: adjusted syscall dispatch)
├── task
│   ├── action.rs (Definition and default behavior of SignalAction for signal handling)
│   ├── mod.rs (Signal handling related functions)
│   ├── signal.rs (Signal value definitions for signal handling, etc.)
│   └── task.rs (Modified: added signal related contents in task control block)
└── trap
├── mod.rs (Signal handling when entering/exiting the kernel)
```


---

**Outline**

1. Lab arrangement
2. Code structure
### 3. Design and Implementation of Pipes
4. Design and Implementation of Signals

![bg right:40% 95%](figs/ipc-os-detail-2.png)

---
<style scoped>
{
  font-size: 32px
}
</style>

#### Implementations of Pipes
Based on file abstraction, supporting I/O redirection
1. [K] Implement file-based standard input/output
2. [K] Implement file-based pipes
3. [U] Support command line parameters
4. [U] Support "|" symbol

![bg right:40% 100%](figs/tcb-ipc-standard-file.png)

---
#### Standard Files

1. Implement file-based standard input/output
  - FD: 0 -- Stdin ; 1/2 -- Stdout
  - Implement the File interface
    - read -> call(SBI_CONSOLE_GETCHAR)
    - write -> call(SBI_CONSOLE_PUTCHAR)


![bg right:40% 100%](figs/tcb-ipc-standard-file.png)






---
#### Initialization of Standard Files

2. Initialize `fd_table` when creating TCB

```rust
TaskControlBlock::fork(...)->... {
   ...
   let task_control_block = Self {
       ...
           fd_table: vec![
               // 0 -> stdin
               Some(Arc::new(Stdin)),
               // 1 -> stdout
               Some(Arc::new(Stdout)),
               // 2 -> stderr
               Some(Arc::new(Stdout)),
           ],
...
```

![bg right:35% 100%](figs/tcb-ipc-standard-file.png)

---
#### Standard File Creation in the Fork Implementation

3. Copy `fd_table` during `fork`

```rust
TaskControlBlock::new(elf_data: &[u8]) -> Self{
   ...
     // copy fd table
     let mut new_fd_table = Vec::new();
     for fd in parent_inner.fd_table.iter() {
         if let Some(file) = fd {
             new_fd_table.push(Some(file.clone()));
         } else {
             new_fd_table. push(None);
         }
     }
```

![bg right:35% 100%](figs/tcb-ipc-standard-file.png)



---
#### Pipe File

1. System calls of Pipes

```rust
/// Function: Open a pipe for the current process.
/// Argument: `pipe` is the starting address of a usize array of length 2 in the application address space. 
/// The kernel needs to write the file descriptors of the pipe read end and write end in order into the array.
/// Returns: 0 on success, -1 on error.
/// Possible error: The address passed is invalid.
/// Syscall ID: 59
pub fn sys_pipe(pipe: *mut usize) -> isize;
```

![bg right:35% 100%](figs/tcb-ipc-standard-file.png)

---
#### Pipe File

2. Create a Buffer for the pipe

```rust
pub struct PipeRingBuffer {
     arr: [u8; RING_BUFFER_SIZE],
     head: usize,
     tail: usize,
     status: RingBufferStatus,
     write_end: Option<Weak<Pipe>>,
}

make_pipe() -> (Arc<Pipe>, Arc<Pipe>) {
     let buffer = PipeRingBuffer::new();
     let read_end = Pipe::read_end_with_buffer();
     let write_end = Pipe::write_end_with_buffer();
     ...
     (read_end, write_end)
```
![bg right:35% 100%](figs/tcb-ipc-standard-file.png)



---
#### Pipe File

3. Implementation of file-based input/output
  - Implement the File interface
```rust
     fn read(&self, buf: UserBuffer) -> usize {
        *byte_ref = ring_buffer. read_byte();
     }
     fn write(&self, buf: UserBuffer) -> usize {
       ring_buffer.write_byte( *byte_ref );
     }
```

![bg right:35% 100%](figs/tcb-ipc-standard-file.png)


---
#### Command line arguments for the exec system call
- The system call interface of sys_exec has been changed
```rust
// Add the args parameter
pub fn sys_exec(path: &str, args: &[*const u8]) -> isize;
```
- Command line argument segmentation in shell programs
```rust
// Get parameters from a line of string
let args: Vec<_> = line.as_str().split(' ').collect();
// Execute the sys_exec system call with the application name and argument addresses
exec(args_copy[0].as_str(), args_addr.as_slice())
```
![bg right:35% 100%](figs/tcb-ipc-standard-file.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Command line arguments for the exec system call

- Push the obtained parameter string onto the user stack
```rust
impl TaskControlBlock {
  pub fn exec(&self, elf_data: &[u8], args: Vec<String>) {
    ...
    // push arguments on user stack
  }
```
- The a0/a1 registers in the trap context, with a0 indicating the number of command line arguments and a1 indicating the starting address of argv_base in the blue region in the figure.

![bg right:30% 100%](figs/user-stack-cmdargs.png)



---

#### Command line arguments for the exec system call

```rust
pub extern "C" fn _start(argc: usize, argv: usize) -> ! {
    //Get the number of command lines in the application, get the command line parameters to v
   //Execute the application's main function
    exit(main(argc, v. as_slice()));
}
```
![bg right:30% 100%](figs/user-stack-cmdargs.png)



---
#### Redirection
- System call to duplicate file descriptors
```rust
/// Function: Duplicates a file that is already open in a process
/// and allocates it to a new file descriptor.
/// Parameters: 'fd' represents the file descriptor of an already opened file in the process.
/// Returns: Returns -1 if an error occurs, otherwise the newly allocated file descriptor to the already opened file can be accessed.
/// Possible error reasons are: 'fd' passed in does not correspond to a valid open file.
/// Syscall ID: 24
pub fn sys_dup(fd: usize) -> isize;
```
<!-- ![bg right:30% 100%](figs/user-stack-cmdargs.png) -->


---
#### Redirection
- System call to duplicate file descriptors
```rust
pub fn sys_dup(fd: usize) -> isize {
   ...
   let new_fd = inner.alloc_fd();
   inner.fd_table[new_fd] = inner.fd_table[fd];
   newfd
}
```
<!-- ![bg right:30% 100%](figs/user-stack-cmdargs.png) -->


---
#### Shell redirection "$A | B"
```rust
// user/src/bin/user_shell.rs
{
  let pid = fork();
  if pid == 0 {  
    let input_fd = open(input, ...); // Input redirection -- B child process
    close(0); // Close file descriptor 0
    dup(input_fd); // File descriptor 0 and input_fd point to the same file
    close(input_fd); // Close input_fd file descriptor
    // Or
    let output_fd = open(output, ...);// Output redirection -- A child process
    close(1); // Close file descriptor 1
    dup(output_fd); // File descriptor 1 and output_fd point to the same file
    close(output_fd); // Close output_fd file descriptor
    // Execute new program after I/O redirection
    exec(args_copy[0].as_str(), args_addr.as_slice()); 
  }...
```

<!-- ![bg right:30% 100%](figs/user-stack-cmdargs.png) -->



---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

1. Lab arrangement
2. Code structure
3. Design and Implementation of Pipes
### 4. Design and Implementation of Signals
- System calls related to signals
- Core data structure of signals
- Establishsignal handler
- Support `kill` system call

![bg right:40% 95%](figs/ipc-os-detail-2.png)

---

#### System Calls Related to Signals

<!-- https://www.onitroad.com/jc/linux/man-pages/linux/man2/sigreturn.2.html -->
- sigaction: Set signal handling routine
- sigprocmask: Set signals to be blocked
- kill: Send a signal to a process
- sigreturn: Clear stack frame and return from signal handling routine

![bg right:50% 100%](figs/signal-process.png)

---
#### System Calls Related to Signals
<!-- https://www.onitroad.com/jc/linux/man-pages/linux/man2/sigreturn.2.html -->
```rust
// Set signal handler
// signum: the signal to be set
// action: new signal handling configuration
// old_action: old signal handling configuration
sys_sigaction(signum: i32,
    action: *const SignalAction,
    old_action: *const SignalAction)
    -> isize

pub struct SignalAction {
     // Address of the signal handler
     pub handler: usize,
     // signal mask
     pub mask: SignalFlags
}
```

![bg right:50% 100%](figs/signal-process.png)


---
#### System Calls Related to Signals
<!-- https://www.onitroad.com/jc/linux/man-pages/linux/man2/sigreturn.2.html -->
```rust
// Set signals to be blocked
// mask: signal mask
sys_sigprocmask(mask: u32) -> isize
```
```rust
// Clear stack frame and return from signal handler
  sys_sigreturn() -> isize
```
```rust
// Send a signal to a process
// pid: process pid
// signal: signal integer code
sys_kill(pid: usize, signal: i32) -> isize
```
![bg right:50% 100%](figs/signal-process.png)


---
#### Core Data Structure of Signals
Signal core data structure in process control block
```rust
pub struct TaskControlBlockInner {
    ...
    pub signals: SignalFlags,     // Signals to be responded to
    pub signal_mask: SignalFlags, // Signals to be blocked
    pub handling_sig: isize,      // Signal being handled
    pub signal_actions: SignalActions,       // Signal handling routine table
    pub killed: bool,             // Whether the task has been killed
    pub frozen: bool,             // Whether the task has been paused
    pub trap_ctx_backup: Option<TrapContext> // Interrupted trap context
}
```
<!--
The function of killed is to mark whether the current process has been killed. Because the process will not end immediately when it receives the kill signal, but will exit at an appropriate time. At this time, killed is needed as a mark to exit the unnecessary signal processing loop.

The frozen flag is related to the two signals SIGSTOP and SIGCONT. SIGSTOP will suspend the execution of the process, that is, set frozen to true. At this time, the current process will block waiting for SIGCONT (that is, the unfreezing signal). When the signal receives SIGCONT, set frozen to false, exit the cycle of waiting for the signal, and return to user mode to continue execution. -->

---
#### Establish signal_handler

```rust
fn sys_sigaction(signum: i32, action: *const SignalAction, 
                          old_action: *mut SignalAction) -> isize {
  // Save the old signal_handler address to old_action
  let old_kernel_action = inner.signal_actions.table[signum as usize];
  *translated_refmut(token, old_action) = old_kernel_action;
  // Set the new signal_handler address to TCB's signal_actions
  let ref_action = translated_ref(token, action);
  inner.signal_actions.table[signum as usize] = *ref_action;
```
For the signal number `signum` that needs to be modified:
1. Save the old signal_handler address to `old_action`
2. Set `action` as the new signal_handler address



---
#### Send signals through kill

```rust
fn sys_kill(pid: usize, signum: i32) -> isize {
      let Some(task) = pid2task(pid);
      // insert the signal if legal
      let mut task_ref = task.inner_exclusive_access();
      task_ref.signals.insert(flag);
     ...
```
Send a signal with value `signum` to the process with process ID `pid`:
1. Find TCB based `pid`
2. Insert the `signum` signal value in the `signals` in the TCB



---
#### Send and Handle Signals through kill
From when process `pid` enters the kernel, the execution process before returning to the user mode from the kernel:
```
Execute APP --> __alltraps
          --> trap_handler
             --> handle_signals
                 --> check_pending_signals
                     --> call_kernel_signal_handler
                     --> call_user_signal_handler
                        --> // backup trap Context
                             // modify trap Context
                             trap_ctx.sepc = handler; // Set the entry point of the interrupt handler
                             trap_ctx.x[10] = sig; // Put the signal value into Reg[10]
             --> trap_return // Find and jump to the `__restore` assembly function located in the trampoline page
        --> __restore // Restore the modified trap context and execute sret
Execute the signal_handler function of APP
  ```

 
---
#### Resume Normal Execution of APP

After the process with process ID `pid` has finished executing the body of the `signal_handler` function, it will issue a `sys_sigreturn` system call:
```rust
fn sys_sigreturn() -> isize {
  ...
  // Restore the trap context that was previously backed up
  let trap_ctx = inner.get_trap_cx();
  *trap_ctx = inner.trap_ctx_backup.unwrap();
  ...
Execute APP --> __alltraps 
       --> trap_handler 
            --> Handling sys_sigreturn system call
            --> trap_return // Find and jump to the `__restore` assembly function located in the trampoline page
    -->  __restore // Restore the modified trap context and execute sret
Execute APP at the point where it was interrupted
```


---
#### Mask Signal
```rust
fn sys_sigprocmask(mask: u32) -> isize {
     ...
     inner.signal_mask = flag;
     old_mask. bits() as isize
     ...
```
Directly record the signals to be masked in the signal_mask data of TCB.

---
### Summary
- Concept and implementation of pipes
- Concept and implementation of signals
- Ability to write velociraptor OS

![bg right 80%](figs/velociraptor.png)

---

### Lab 4. File System and IPC


* Chapter 6: File system and I/O redirection -> chapter6 exercises ->
     * [rCore](https://learningos.github.io/rCore-Tutorial-Guide-2022A/chapter6/4exercise.html#id1)
     * [uCore](https://learningos.github.io/uCore-Tutorial-Guide-2022A/chapter6/5exercise.html#id3)
* Lab tasks
     * Hard link
* Lab submission requirements
     * The 11th day after the task is assigned (December 04, 2022);