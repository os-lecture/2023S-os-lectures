---
noteId: "daf26a91d86511edb7d82f06d610f713"
tags: []
marp: true
theme: "default"
paginate: true
_paginate: false
header: ""
footer: ""
backgroundColor: "white"

---

<!-- theme: gaia -->
<!-- _class: lead -->

# Lecture 3 Privilege based Isolation and Batch Processing
## Section 3 Practice: Batch Operating System

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

<br>
<br>

Spring 2023

---
**Outline**

### 1. Lab objectives
2. Lab steps
3. Software Architecture
4. Related hardware
5. Application Design
6. Kernel programming

---

#### Structure of batch operating systems
![w:750](figs/batch-os-detail.png)

   <!-- - Construct & Load OS&APP
   - Protect OS based on RV privilege mechanism
   - System call support
   - privilege switching -->


---
#### Batch OS targets
- Isolate APP from OS
- Automatically load and run multiple programs
   - **Batch processing**

LibOS target
- Isolate applications from hardware
- Simplifies the difficulty and complexity of accessing hardware from applications
  
![bg right:35% 95%](figs/batch-os.png)

---
#### Lab Requirements
- Understand software that runs other software
- Understand privileges and privilege switching
- Understand system calls
- Can write ``Dunkleosteus`` batch OS

Dunkleosteus:  An ocean overlord with a sturdy shield in the Devonian
![bg right:45% 90%](figs/dunkleosteus.png)

---
#### General Idea
- Compilation: Compile applications and kernel **independently**, merged into one image
- Construction: system call service request interface, application **management and initialization**
- Execution: OS **executes applications** one by one
- Execution: Application issue **system call** requests, and OS completes the system calls.
- Execution: Hardware privilege mechanism based **privilege switching** (between application and OS). 



---
#### History background
- GM-NAA I/O System(1956)
   - Inspiration: Automobile production line
- MULTICS OS (1969, MIT/GE/AT&T)
   - GE(General Electric) 645 has a protection ring with 8-level hardware support
  GM-NAA (General Motors and North American Aviation);

![bg right:38% 95%](figs/deng-fish.png)



---
**Outline**

1. Lab objectives
### 2. Lab steps
3. Software Architecture
4. Related hardware
5. Application Design
6. Kernel programming

---

#### Steps
- Construct a **single execution image** containing OS and multiple APPs
- Support automatic loading and execution of **multiple APPs via batch processing**
- Use the hardware privilege mechanism to achieve the **protection** of OS
- Support **system calls** for crossing privilegea
- Achieve privileges **crossing**

![bg right:35% 100%](figs/batch-os.png)




---
#### Steps of Compilation
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch2

cd os
make run
```

---
#### Referred execution results
```
...
[kernel] num_app = 5
[kernel] app_0 [0x8020a038, 0x8020af90)
...
[kernel] Loading app_0
Hello, world!
[kernel] Application exited with code 0
[kernel] Loading app_1
...
[kernel] Panicked at src/batch.rs:58 All applications completed!
```


---
**Outline**

1. Lab objectives
2. Lab steps
### 3. Software Architecture
4. Related hardware
5. Application Design
6. Kernel programming

---

#### Building the application
Combine multiple applications together with OS to form a binary image
```
├── os
│ ├── build.rs (new: generate link_app.S to link the application to the kernel as a data segment)
│ ├── Cargo.toml
│ ├── Makefile (modification: build the application before building the kernel)
│ └── src
│ ├── link_app.S (build product, output by os/build.rs)
```


---
#### Improve OS
Load and execute programs, privileges/context switching
```
├── os
│ └── src
│ ├── batch.rs (new: implement a simple batch processing system)
│ ├── main.rs (modification: the main function needs to initialize Trap processing and load/execute the application)

│ └── trap (new: Trap related submodule "trap")
│ ├── context.rs (contains Trap context "TrapContext")
│ ├── mod.rs (includes "trap_handler")
│ └── trap.S (contains the assembly code for saving and restoring the Trap context)
```

---
#### System Calls

```
├── os
│ └── src
│ ├── syscall (new: system call submodule "syscall")
│ │ ├── fs.rs (contains file I/O related syscalls)
│ │ ├── mod.rs (provides "syscall" method to distribute different syscall IDs)
│ │ └── process.rs (contains task execution related syscalls)
```

---
#### Add Application
Batch OS will load and execute them sequentially according to the order of numbers in the file names
```
└── user (new: test cases are saved in the "user" directory)
    └── src
       ├── bin (Applications developed based on "user_lib", each application is placed in a separate source file)
       │ ├── 00hello_world.rs # Application to display strings
       │ ├── 01store_fault.rs # Application of illegal write operation
       │ ├── 02power.rs # Application where computing/IO frequently switch
       │ ├── 03priv_inst.rs # Application to execute privileged instructions
       │ └── 04priv_csr.rs # Application to execute CSR operation instructions
```
---
#### Application library and application compilation support
Application library and application compilation support
```
└── user (new: application test cases are saved in the user directory)
    └── src
       ├── console.rs # Functions and macros to support "println!"
       ├── lang_items.rs # Support "panic_handler" function
       ├── lib.rs(user library user_lib) # The underlying support library for applications
       ├── linker.ld # Application link script
       └── syscall.rs (contains assembly instructions generated for system calls,
                      each specific syscall is implemented through "syscall")
```


---
**Outline**

1. Lab objectives
2. Lab steps
3. Software Architecture
### 4. Related hardware
5. Application Design
6. Kernel programming

---

#### RISC-V Trap instructions

- ecall: Trigger different traps depending on the CPU's current privilege
- ebreak: Trigger a breakpoint trap

**RISC-V Privileged Instructions**

- sret: Trigger different traps depending on the CPU's current privilege
  
![bg right:40% 120%](figs/EnvironmentCallFlow.png)


---

#### RISC-V exception vector

| Interrupt | Exception Code | Description |
| --------- | -------------- | --------------------------- |
| 0 | 0 | Instruction address misaligned |
| 0 | 1 | Instruction access fault |
| 0 | 2 | Illegal instruction |
| 0 | 3 | **Breakpoint** |
| 0 | 4 | Load address misaligned |
| 0 | 5 | Load access fault |


            
---
#### RISC-V exception vector

| Interrupt | Exception Code | Description |
| --------- | -------------- | --------------------------- |
| 0 | 6 | Store/AMO address misaligned |
| 0 | 7 | Store/AMO access fault |
| 0 | 8 | **Environment call from U-mode** |
| 0 | 9 | Environment call from S-mode |
| 0 | 11 | Environment call from M-mode |

            
---
#### RISC-V exception vector

| Interrupt | Exception Code | Description |
| --------- | -------------- | --------------------------- |
| 0 | 12 | Instruction page fault |
| 0 | 13 | Load page fault |
| 0 | 15 | Store/AMO page fault |


- AMO: atomic memory operation

---
**Outline**

1. Lab objectives
2. Lab steps
...
### 5. Application Design
- project structure
- memory layout
- system calls
6. Kernel programming

---

#### Separation of Application and underlying support library

```
└── user (application and underlying support library)
    └── src
       ├── bin (this directory places applications developed based on "user_lib")
       ├── lib.rs(user library user_lib) # The underlying library functions
       ├── ...... # Support library related files
       └── linker.ld # Application link script
```

---
#### import external library
```rust
   #[macro_use]
   extern crate user_lib;
```

---
#### Design support library

In lib.rs we define the entry point _start for the user library:
```rust
#[no_mangle]
#[link_section = ".text.entry"]
pub extern "C" fn _start() -> ! {
     clear_bss();
     exit(main());
     panic!("unreachable after sys_exit!");
}
```


---
**Outline**

1. Lab objectives

...

5. Application Design
- project structure
- **Memory layout**
- system calls
6. Kernel programming

---

####  Memory layout of applications

![bg 70%](figs/memlayout.png)

---
<style scoped>
{
  font-size: 31px
}
</style>
#### Design support library
`user/src/linker.ld`

- Set the starting physical address of the program to 0x80400000, and the program will be loaded to this address to run;
- Put the .text.entry (where _start is located at) to the beginning of the entire program. As long as the batch processing system jumps to 0x80400000 after loading, it has entered the entry point of the user library and will jump to the application. The system will jump to application main logic after initialization;
- Provides the start and end addresses of the .bss section of the final executable file, which is used by clear_bss function.

The rest is the same as before.


---
**Outline**

1. Lab objectives

...

5. Application Design
- project structure
- memory layout
- **System Calls**
6. Kernel programming

---

#### System call execution flow of the application

![w:1050](figs/EnvironmentCallFlow.png)

---

#### System call execution flow of the application

- In the submodule "syscall", the application calls the interface provided by the batch system through "ecall"
- The ``ecall`` instruction triggers an exception, named Environment call from U-mode
- Trap falls into the S-Mode to execute exception handler code.
- a0~a6 save the parameters of the system call, a0 saves the return value, and a7 is used to pass the syscall ID
---
#### Support library for System Call
```rust--
/// Function: Dump the data in memory buffer into the file.
/// Parameter: `fd` indicates the file descriptor of the target file to be written into;
/// `buf` indicates the starting address of the memory buffer;
/// `len` indicates the length of the memory buffer.
/// Return value: Returns the length that has been written successfully.
/// syscall ID: 64
fn sys_write(fd: usize, buf: *const u8, len: usize) -> isize;

/// Function: Exit the application and provide the batch system with return value.
/// Parameter: `xstate` represents the return value of the application.
/// Return value: This system call should not return.
/// syscall ID: 93
fn sys_exit(xstate: usize) -> !;
```


---
#### System call parameter passing

```rust
fn syscall(id: usize, args: [usize; 3]) -> isize {
     let mut ret: isize;
     unsafe {
         asm!(
             "ecall",
             inlateout("x10") args[0] => ret, //first parameter & return value
             in("x11") args[1], //the second parameter
             in("x12") args[2], //the third parameter
             in("x17") id //syscall number
         );
     }
     ret //return value
}
```


---
#### System call encapsulation
```rust
const SYSCALL_WRITE: usize = 64;
const SYSCALL_EXIT: usize = 93;
// Encapsulation of system calls
pub fn sys_write(fd: usize, buffer: &[u8]) -> isize {
     syscall(SYSCALL_WRITE, [fd, buffer.as_ptr() as usize, buffer.len()])
}
pub fn sys_exit(xstate: i32) -> isize {
     syscall(SYSCALL_EXIT, [xstate as usize, 0, 0])
}
```

---
#### System call encapsulation
```rust
pub fn write(fd: usize, buf: &[u8]) -> isize { sys_write(fd, buf) }

const STDOUT: usize = 1;

impl Write for Stdout {
     fn write_str(&mut self, s: &str) -> fmt::Result {
         write(STDOUT, s.as_bytes());
         Ok(())
     }
}
```


---
**Outline**

...

5. Application Design
### 6. Kernel programming
- Application management and loading
- Privilege switching
- Trap context
- Trap processing flow
- Execution of applications

---

#### Link the application image to the kernel
```
# os/src/link_app.S generated by script os/build.rs
     .section .data
     .global_num_app
_num_app:
     .quad 5
     .quad app_0_start
     ...
     .quad app_4_end
     .section .data
     .global app_0_start
     .global app_0_end
app_0_start:
     .incbin "../user/target/riscv64gc-unknown-none-elf/release/00hello_world.bin"
app_0_end:
```


---
#### Auxiliary Data Structures for Application Management 

```rust
// os/src/batch.rs

struct AppManager {
     num_app: usize,
     current_app: usize,
     app_start: [usize; MAX_APP_NUM + 1],
}
```


---
#### Find the application binary
- Locate the symbol "_num_app" provided in link_app.S
```rust
lazy_static! {
     static ref APP_MANAGER: UPSafeCell<AppManager> = unsafe { UPSafeCell::new({
         extern "C" { fn _num_app(); }
         let num_app_ptr = _num_app as usize as *const usize;
         ...
         app_start[..=num_app].copy_from_slice(app_start_raw);
         AppManager {
             num_app,
             current_app: 0,
             app_start,
         }
```

---

#### Load the application binary
```rust
unsafe fn load_app(&self, app_id: usize) {
     // clear icache
     asm!("fence.i");
     // clear app area
     ...
     let app_src = core::slice::from_raw_parts(
         self.app_start[app_id] as *const u8,
         self.app_start[app_id + 1] - self.app_start[app_id] );
     let app_dst = core::slice::from_raw_parts_mut(
         APP_BASE_ADDRESS as *mut u8,
         app_src.len());
     app_dst.copy_from_slice(app_src);
}
```


---
#### Load the application binary

- fence.i : used to clear i-cache

Note: ``fence.i`` is an i-cache barrier instruction, a non-privileged instruction, which belongs to the "Zifencei" extended specification

**Why?**

---
#### Load the application binary

- fence.i : used to clear i-cache

- Caches for physical memory include d-cache and i-cache
- OS may modify the memory area that will be fetched by the CPU, which will make the i-cache contain inconsistent content with the memory
- Here OS must use fence.i to manually clear the i-cache to invalidate all the contents, to prevent CPU from reading inconsistent data from cache.



---
**Outline**

...

5. Application Design
6. Kernel programming
- Application management and loading
- **Privilege  Switching**
- Trap context
- Trap processing flow
- Execution of applications

---

#### CSR for privilege  switching
| CSR name | Trap-related function of this CSR |
| ------- | ----------------------------------------- ------------------- |
| sstatus | `SPP` will give the privilege  (S/U) before the Trap occurs |
| sepc | Records the address of last instruction executed before the Trap occurrs |
|scause | Describes the reason for the Trap |
| stval | Gives additional information about the Trap |
| stvec | Records the entry address of the Trap handler |

---
#### Hardware logic after privilege switching

1. SPP of sstatus will record the current CPU privilege(U/S);
2. Set sepc to the return address after the Trap processing is completed(next instruction that will be executed);
3. Scause/stval will record the source of this Trap and relevant additional information;
4. CPU sets the current privilege to S, and jumps to the Trap handler entry address set by stvec.



---
#### Privilege switching and user stack/kernel stack
Why does OS use two different stacks?

For safety!

```rust
const USER_STACK_SIZE: usize = 4096 * 2;
const KERNEL_STACK_SIZE: usize = 4096 * 2;

static KERNEL_STACK: KernelStack = KernelStack { data: [0; KERNEL_STACK_SIZE] };
static USER_STACK: UserStack = UserStack { data: [0; USER_STACK_SIZE] };
```

---
#### Switch stack in privilege switching
```rust
impl UserStack {
     fn get_sp(&self) -> usize {
         self.data.as_ptr() as usize + USER_STACK_SIZE
     }
}
RegSP = USER_STACK. get_sp();
RegSP = KERNEL_STACK.get_sp();
```

---
**Outline**

...

5. Application Design
6. Kernel programming
- Application management and loading
- Privilege switching
- **Trap context**
- Trap processing flow
- Execution of applications

---

#### Auxiliary data structure for Trap context 
```rust
#[repr(C)]
pub struct TrapContext {
     pub x: [usize; 32],
     pub sstatus: Sstatus,
     pub sepc: usize,
}
```
- Control flow of application/kernel runs at different privileges for general-purpose registers
- When entering Trap, the hardware will immediately overwrite scause/stval/sstatus/sepc.


---
#### Trap entry point after privilege switching
```rust
pub fn init() {
     extern "C" { fn __alltraps(); }
     unsafe {
         stvec::write(__alltraps as usize, TrapMode::Direct);
     }
}
```


---
#### Trap context processing during system calls
1. When the application enters the kernel mode through "ecall", OS saves the Trap context of the interrupted application;

![bg right:40% 100%](figs/kernel-stack.png)


---
#### Trap context processing during system calls

2. OS distributes and finishes system call services according to the Trap-related CSR register;

![bg right:40% 100%](figs/kernel-stack.png)


---
#### Trap context processing during system calls

3. After OS completes system call services, it needs to restore the Trap context of the interrupted application, and let the application continue execution through the ``sret`` instruction.
![bg right:40% 100%](figs/kernel-stack.png)
---

#### Switch from user stack to kernel stack
**sscratch CSR:** An important transit register

When the privilege is switched, we need to save the Trap context on the kernel stack, so we need a register to temporarily store the kernel stack address, and use it as the base address pointer to save the contents of the Trap context.

But all general-purpose registers cannot be used as base address pointers, because they all need to be saved. Overwritting them will affect the execution of subsequent application control flow.

---
#### Switch from user stack to kernel stack

**sscratch CSR:** An important transit register

- Temporarily save the address of the kernel stack
- As a transit station, the value of sp (pointing to the address of the user stack) can be temporarily saved in sscratch
- Only need `csrrw sp, sscratch, sp // swap the two register contents of sp and sscratch`
- Complete the switching of user stack --> kernel stack

---

#### Save general-purpose registers in the Trap context

Macros to save general-purpose registers
```
# os/src/trap/trap.S
.macro SAVE_GP n
     sd x\n, \n*8(sp)
.endm
```

---
**Outline**

...

5. Application Design
6. Kernel programming
- Application management and loading
- Privilege switching
- Trap context
- **Trap processing flow**
- Execution of applications

---

#### Trap processing flow


The overall workflow of Trap processing is as follows:
1. First save the Trap context on the kernel stack through __alltraps;
2. Then jump to the trap_handler function to complete trap distribution and processing.
```
__alltraps:
     csrrw sp, sscratch, sp
     # now sp->kernel stack, sscratch->user stack

     # allocate a TrapContext on kernel stack
     addi sp, sp, -34*8
```



---
#### Save Trap context
save general purpose registers
```
     # save general-purpose registers
     sd x1, 1*8(sp)
     # skip sp(x2), we will save it later
     sd x3, 3*8(sp)
     # skip tp(x4), application does not use it
     # save x5~x31
     .set n, 5
     .rept 27
         SAVE_GP %n
         .set n, n+1
     .endr
```


---
#### Save Trap context
save sstatus and sepc
```
     # we can use t0/t1/t2 freely, because they were saved on kernel stack
     csrr t0, sstatus
     csrr t1, sepc
     sd t0, 32*8(sp)
     sd t1, 33*8(sp)
```

---
#### Save Trap context
Save user SP
```
     # read user stack from sscratch and save it on the kernel stack
     csrr t2, sscratch
     sd t2, 2*8(sp)
```
```rust
pub struct TrapContext {
     pub x: [usize; 32],
     pub sstatus: Sstatus,
     pub sepc: usize,
}
```
---

#### Call trap_handler
```
     # set input argument of trap_handler(cx: &mut TrapContext)
     mv a0, sp
     call trap_handler
```

Let the register a0 point to the stack pointer of kernel stack, which is the address of the Trap context we just saved. This is because we will call trap_handler for Trap processing next, and its first parameter cx is obtained from a0 by the calling specification.

---
#### Restore Trap context
1. Most of them are reverse operations of saving registers;
2. The last step is ``sret`` instruction //return from kernel mode to user mode

Note: We will talk about more details of "Restore Trap Context" later.


---
#### Syscall processing by trap_handler

```rust
#[no_mangle]
pub fn trap_handler(cx: &mut TrapContext) -> &mut TrapContext {
    let scause = scause::read();
    let stval = stval::read();
    match scause.cause() {
        Trap::Exception(Exception::UserEnvCall) => {
            cx.sepc += 4;
            cx.x[10] = syscall(cx.x[17], [cx.x[10], cx.x[11], cx.x[12]]) as usize;
        }
    ...
    }
    cx
}
```


---
#### Syscall processing by trap_handler

``` rust
pub fn sys_exit(xstate: i32) -> ! {
    println!("[kernel] Application exited with code {}", xstate);
    run_next_app()
}
```


---
**Outline**

...

5. Application Design
6. Kernel programming
- Application management and loading
- privilege switching
- Trap context
- Trap processing flow
- **Execution of applications**

---

#### When to execute applications
- After the batch OS initialization is complete
- When an application terminates or an error occurrs

---

#### Executions of applications
Switch from kernel mode to user mode
- Ready to apply the saved context ``Trap Context``
- Restore application related registers
- especially pay attention to user stack pointer and previous execution address of the application
- **Return to user mode to continue execute the application**

![bg right:35% 100%](figs/kernel-stack.png)

---

#### Return to user mode to continue execute the app

- Switch from kernel mode to user mode
   - **hardware logic** of ``sret`` instruction:
      - Resume the response to interrupts/exceptions(unmask interrupts)
      - Switch CPU Mode from S-Mode back to U-Mode
      - ``pc`` <-- ``spec`` CSR
      - Continue execution

---
<style scoped>
{
  font-size: 32px
}
</style>

#### Switch to the next application

Call the "run_next_app" function to switch to the next application:
- Construct the Trap context for the application;
- Through the `__restore` function, from the newly constructed Trap context, restore part of the registers of the application;
- Set the content of the `sepc` CSR to the application entry point `0x80400000`;
- switch `scratch` and `sp` registers, set `sp` to point to the application user stack;
- Execute `sret` to switch from S-Mode to U-Mode.


---
#### Construct Trap context
```rust
impl TrapContext {
     pub fn set_sp(&mut self, sp: usize) { self. x[2] = sp; }
     pub fn app_init_context(entry: usize, sp: usize) -> Self {
         let mut sstatus = sstatus::read();
         sstatus.set_spp(SPP::User);
         let mut cx = Self {
             x: [0; 32],
             sstatus,
             sepc: entry,
         };
         cx.set_sp(sp);
         cx
```


---
#### Run the next application
```rust
ub fn run_next_app() -> ! {
     ...
     unsafe {
         app_manager.load_app(current_app);
     }
     ...
     unsafe {
         __restore(KERNEL_STACK.push_context(
             TrapContext::app_init_context(APP_BASE_ADDRESS, USER_STACK.get_sp())
         ) as *const _ as usize);
     }
     panic!("Unreachable in batch::run_current_app!");
}
```

---
#### Run the next application
```
__restore:
     # case1: start running app by __restore
     # case2: back to U after handling trap
     mv sp, a0
     # now sp->kernel stack(after allocated), sscratch->user stack
     # restore sstatus/sepc
     ld t0, 32*8(sp)
     ld t1, 33*8(sp)
     ld t2, 2*8(sp)
     csrw sstatus, t0
     csrw sepc, t1
     csrw sscratch, t2
```


---
#### Run the next application

```
# restore general-purpuse registers except sp/tp
     ld x1, 1*8(sp)
     ld x3, 3*8(sp)
     .set n, 5
     .rept 27
         LOAD_GP %n
         .set n, n+1
     .endr
     # release TrapContext on kernel stack
     addi sp, sp, 34*8
     # now sp->kernel stack, sscratch->user stack
     csrrw sp, sscratch, sp
     sret
```

---
#### Ask a question

  When is sscratch set to the top of the kernel stack?

---
<style scoped>
{
  font-size: 32px
}
</style>

### Summary
- Relationship between OS and hardware
- Relationship between OS and applications
- Isolation mechanisms
- Creation of batch system and execution of applications
- Privilege switching
- system calls
- Be able to write Dengshiyu OS

![bg right:50% 95%](figs/batch-os-detail.png)