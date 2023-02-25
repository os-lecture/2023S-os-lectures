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

# Lecture 2 Introduction to Practice and Experiments
## Section 4 Practice: Bare Metal Program -- LibOS

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

<br>
<br>

Spring 2023

---
<style scoped>
{
  font-size: 32px
}
</style>
Outline

### 1. Experimental Objectives And Ideas
2. Experimental Requirements
3. Practical Steps
4. Code Structure
5. Memory Layout
6. Verify The Startup Process Based on GDB
7. Function Calls
8. LibOS Initialization
9. SBI Calls

---
<style scoped>
{
  font-size: 28px
}
</style>
#### Experimental Goals for LibOS

Bare Metal Program: an OS-type program that has nothing to do with the operating system

- Establish the execution environment of the application
   - Keep applications isolated from hardware
   - Simplifiey the difficulty and complexity of applications accessing hardware
- Execution Environment: The multi-level **software and hardware system** which is responsible for providing corresponding functions and resources for software. 
 
![bg right:30% 100%](figs/os-as-lib.png)


---
<style scoped>
{
  font-size: 32px
}
</style>

#### LibOS Historical Background
From 1949 to 1951, the British J. Lyons and Co. (a chain restaurant and food manufacturing group company) pioneered the introduction and use of the EDSAC computer of the University of Cambridge, and jointly designed and implemented the LEO I 'Lyons Electronic Office' hardware and software system


![bg right:50% 70%](figs/LEO_III_computer_circuit_board.jpg)



---
<style scoped>
{
  font-size: 28px
}
</style>

#### LibOS Historical Background -- Subroutines

- David Wheeler, who worked on the EDSAC project, invented the concept of **subroutines** – Wheeler Jump
- With the convenient and effective subroutine concept and subroutine calling mechanism, software developers developed a large number of system **subroutine libraries** on EDSAC and subsequent LEO computers, forming the earliest operating system prototype.

![bg right:50% 70%](figs/LEO_III_computer_circuit_board.jpg)


---

#### General idea of LibOS
- Compilation: Support compiling bare-metal programs by setting the compiler
- Construction: Build the stack of the bare metal program and the SBI service request interface
- Run: Initialize the OS starting address and execution environment

![bg right:45% 100%](figs/os-as-lib.png)



---
<style scoped>
{
  font-size: 32px
}
</style>
Outline

1. Experimental Goals and Ideas
### 2. Experimental Requirements
3. Practical Steps
4. Code Structure
5. Memory Layout
6. Verify the Startup Process based on GDB
7. Function Calls
8. LibOS Initialization
9. SBI Calls

---
#### Understand the execution process of LibOS
- Can write/compile/run bare metal programs
- Understand function calls based on bare metal programs
- Understand assembly code and pseudo code
- Understand inline assembly code
- Preliminary understande  SBI calls
![bg right:35% 100%](figs/os-as-lib.png)
---
#### Master the basic concepts
- **Can write the trilobite operating system now!**
   - What is ABI?
   - What is SBI?
   - Supervisor Binary Interface?
![bg right:50% 90%](figs/trilobita.png)

Note: Trilobita is the most representative ancient animal in the Cambrian Period

---
#### Analyze Execution Details

- **Understanding functions at the machine level**
   - registers
   - function call/return (call/return)
   - Function enter/exit (enter/exit)
   - Function prologue/epilogue (prologue/epilogue)

![bg right:50% 90%](figs/trilobita.png)

---

#### The OS is not always the bottom layer of the software
   - There is a sky
   - Why?

![bg right:50% 90%](figs/trilobita.png)

---
<style scoped>
{
  font-size: 32px
}
</style>
Outline

1. Experimental Goals and Ideas
2. Experimental Requirements
### 3. Practical Steps
4. Code Structure
5. Memory Layout
6. Verify the Startup Process based on GDB
7. Function Calls
8. LibOS Initialization
9. SBI Calls

---

#### Practical Steps
- Build the development and experiment environment
- Remove standard library dependencies
- Support function call
- Complete output and shutdown based on SBI service

**Understand the memory space and stack of running programs**

![bg right:45% 100%](figs/os-as-lib.png)

---

#### Steps
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch1

cd os
make run
```

---

#### Results of the
```
[RustSBI output]
Hello, world!
.text [0x80200000, 0x80202000)
.rodata [0x80202000, 0x80203000)
.data [0x80203000, 0x80203000)
boot_stack [0x80203000, 0x80213000)
.bss [0x80213000, 0x80213000)
Panicked at src/main.rs:46 Shutdown machine!
```

In addition to displaying  Hello, world!  There are some additional information, and finally shut down.

---
<style scoped>
{
  font-size: 32px
}
</style>
Outline

1. Experimental Goals and Ideas
2. Experimental Requirements
3. Practical Steps
### 4. Code Structure
5. Memory Layout
6. Verify The Startup Process Based on GDB
7. Function Calls
8. LibOS Initialization
9. SBI Calls

![bg right:50% 100%](figs/lib-os-detail.png)

---
#### LibOS code structure
```
./os/src
Rust 4 Files 119 Lines
Assembly 1 Files 11 Lines

├── bootloader (the implementation of SBI running at the M privilege level that the kernel depends on, we use RustSBI in this project)
│ ├── rustsbi-k210.bin (precompiled binary version that can run on k210 real hardware platform)
│ └── rustsbi-qemu.bin (precompiled binary version that can run on the qemu virtual machine)
```

---
#### LibOS code structure
```
├── os (our kernel implementation is placed in the os directory)
│ ├── Cargo.toml (some configuration files implemented by the kernel)
│ ├── Makefile
│ └── src (all kernel source codes are placed in the os/src directory)
│ ├── console.rs (to further encapsulate the SBI interface of printing characters to achieve more powerful formatted output)
│ ├── entry.asm (a piece of assembly code to set the kernel execution environment)
│ ├── lang_items.rs (some semantic items we need to provide to the Rust compiler, currently contains the processing logic when the kernel panics)
│ ├── linker-qemu.ld (the linker script that controls the kernel memory layout to make the kernel run on the qemu virtual machine)
│ ├── main.rs (kernel main function)
│ └── sbi.rs (call the SBI interface provided by the underlying SBI implementation)
```


<!-- https://blog.51cto.com/onebig/2551726
(In-depth understanding of computer systems) bss segment, data segment, text segment, heap (heap) and stack (stack) -->


---
<style scoped>
{
  font-size: 32px
}
</style>
Outline

1. Experimental Goals and Ideas
2. Experimental Requirements
3. Practical Steps
4. Code Structure
### 5. Memory layout
6. Verify the Startup Process based on GDB
7. Function Calls
8. LibOS Initialization
9. SBI Calls

---
#### App/OS Memory Layout
![bg w:950](figs/memlayout.png)

---
#### BSS Segment

- Block Started by Symbol (BSS)
- usually refers to a memory area used to store **uninitialized global variables** in the program
- belongs to **static memory allocation**
![bg right:45% 130%](figs/memlayout.png)


---
#### Data Segment

-  usually refers to a memory area used to store **initialized global variables** in the program
- belongs to **static memory allocation**
![bg right:50% 130%](figs/memlayout.png)



---
#### Text Segment

- Code segment (code segment/text segment) refers to the memory that stores **executive code**
- fixed memory size, usually **read-only**
- It's possible to contain some **read-only constant variables**
![bg right:45% 130%](figs/memlayout.png)



---
#### Heap

- used for **dynamic allocation**, can be dynamically expanded or reduced
- The program calls functions such as **malloc** to allocate newly allocated memory to be dynamically added to the heap
- The program calls functions  such as **free** to remove memory from the heap
![bg right:45% 130%](figs/memlayout.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Stack

- A **local variable** temporarily created by the user to save the program
- When a function is called, its **parameters** and the **return value** of the function will also be placed on the stack
- Due to the **first-in last-out** feature of the stack, the stack is particularly convenient for saving/restoring the current execution state



![bg right:45% 130%](figs/memlayout.png)


---
#### Stack

The stack can be regarded as a memory area for **registering and exchanging temporary data**

Difference between OS programming and application programming: OS programming requires an understanding of the **physical memory structure on the stack** and the **machine-level content**.

![bg right:45% 130%](figs/memlayout.png)



---

#### Memory Layout Customization at Link Time
```
# os/src/linker-qemu.ld
OUTPUT_ARCH(riscv)
ENTRY(_start)
BASE_ADDRESS = 0x80200000;

SECTIONS
{
     . = BASE_ADDRESS;
     skernel = .;

     text = .;
     .text : {
       *(.text.entry)
```
![bg right:45% 130%](figs/memlayout.png)

---
#### Memory Layout Customization at Link Time
```
     .bss : {
         *(.bss.stack)
         sbss = .;
         *(.bss.bss.*)
         *(.sbss.sbss.*)
```

BSS: Block Started by Symbol
SBSS: small bss, near data


![bg right:45% 130%](figs/memlayout.png)



---

#### Generate Kernel Binary Image

![bg w:850](figs/load-into-qemu.png)

---

#### Generate Kernel Binary Image

```
rust-objcopy --strip-all \
target/riscv64gc-unknown-none-elf/release/os \
-O binary target/riscv64gc-unknown-none-elf/release/os.bin
```

---
<style scoped>
{
  font-size: 32px
}
</style>
Outline

1. Experimental Goals and Ideas
2. Experimental Requirements
3. Practical Steps
4. Code Structure
5. Memory Layout
### 6. Verify the Startup Process based on GDB
7. Function Calls
8. LibOS Initialization
9. SBI Calls

---

#### Verify the Startup Process based on GDB
```
qemu-system-riscv64 \
     -machine virt \
     -nographic \
     -bios ../bootloader/rustsbi-qemu.bin\
     -device loader,file=target/riscv64gc-unknown-none-elf/release/os.bin,addr=0x80200000 \
     -s -S
```

```
riscv64-unknown-elf-gdb \
     -ex 'file target/riscv64gc-unknown-none-elf/release/os' \
     -ex 'set arch riscv:rv64' \
     -ex 'target remote localhost:1234'
[GDB output]
0x0000000000001000 in ?? ()
```


---
<style scoped>
{
  font-size: 32px
}
</style>
Outline

1. Experimental Goals and Ideas
2. Experimental Requirements
3. Practical Steps
4. Code Structure
5. Memory Layout
6. Verify the Startup Process based on GDB
### 7. Function Calls
8. LibOS Initialization
9. SBI Calls

---
#### Compilation principle support for function calls

* Principles of Compliers -- [implement compilation support for function call compilation ](https://decaf-lang.github.io/minidecaf-tutorial/docs/step9/example.html)
* [Documentation for Quick Start RISC-V Assembly](https://github.com/riscv-non-isa/riscv-asm-manual/blob/master/riscv-asm.md)

![bg right:60% 90%](figs/function-call.png)


---
<style scoped>
{
  font-size: 32px
}
</style>
#### call/return directive

Directives | Basic instructions | Meaning |
:----------------|:-----------|:----------|
ret | jalr x0, x1, 0 | function returns
call offset | auipc x6, offset[31:12]; jalr x1, x6, offset[11:0] | function call

auipc (add upper immediate to pc):used to construct a PC-relative address by adding a U-type immediate value. The lower 12 bits are filled with 0, and the upper 20 bits are used as U-type immediate data to create a 32-bit offset. This offset is then added to the current PC value, and the resulting value is saved in register x1.

---
<style scoped>
{
  font-size: 32px
}
</style>
#### Function Call Jump Instruction
![w:1000](figs/fun-call-in-rv.png)

The pseudo-instruction,**ret**,is translated as jalr x0, 0(x1), which means jumping to the address saved by the register ra (x1).
*[Documentation for Quick Start RISC-V Assembly](https://github.com/riscv-non-isa/riscv-asm-manual/blob/master/riscv-asm.md)*


---
<style scoped>
{
  font-size: 30px
}
</style>
#### call/return pseudo-instruction
pseudo-instruction | Basic instructions | Meaning |
:----------------|:-----------|:----------|
ret | jalr x0, x1, 0 | function return
call offset | auipc x6, offset[31:12]; jalr x1, x6, offset[11:0] | function call

Function call core mechanism
- When the function is called, save the return address and jump by the call pseudo-instruction;
- When the function returns, return to the next instruction before the jump through the ret pseudo-instruction to continue execution


---
#### Function calling convention

The function **calling convention** stipulates how to implement the function call of a certain programming language on a certain instruction set architecture. Including:

- How to pass the input parameters and return value of the function;
- The division of caller/callee saved registers in the function call context;
- Other methods of using registers in the function call process.

---

#### RISC-V Function Calling Convention: call parameters and transfer return value transfer
![w:1200](figs/rv-call-regs.png)
- RISC-V32: If the return value is 64bit, use a0~a1 to place it
- RISC-V64: If the return value is 64bit, use a0 to place it


---

#### Risc-V Function Calling Convention: Stack Frame
![w:1000](figs/call-stack.png)

---
#### RISC-V function calling convention: stack frame

**Stack Frames**
```
return address *
previous fp
saved registers
local variables
…
return address fp register
previous fp (pointed to *)
saved registers
local variables
… sp register
```
![bg right:50% 180%](figs/stack-frame.png)



---
<style scoped>
{
  font-size: 32px
}
</style>
#### RISC-V Function Calling Convention: Stack Frame
- Stack frames may have different sizes and contents, but the overall structure is similar
- Each stack frame starts with the **return value** of this function and the fp value of the **previous function**
- The sp register always points to the **bottom** of the current stack frame
- The fp register always points to the **top** of the current stack frame

![bg right:40% 180%](figs/stack-frame.png)



---

#### RISC-V Function Calling Convention: ret Instruction
- When the ret instruction is executed, the following pseudo-code adjust the stack pointer and PC:
```
pc = return address
sp = fp + ENTRY_SIZE
fp = previous fp
```
![bg right:50% 180%](figs/stack-frame.png)

---

#### RISC-V Function Calling Convention: Function Structure
Function structure: ``prologue``, ``body part`` and ``epilogue``
```
.global sum_then_double
sum_then_double:
addi sp, sp, -16 # prologue
sd ra, 0(sp)

call sum_to # body part
li t0, 2
mul a0, a0, t0

ld ra, 0(sp) # epilogue
addi sp, sp, 16
ret
```

---
#### RISC-V Function Calling Convention: Function Structure

Function structure: ``prologue``, ``body part`` and ``epilogue``
```
.global sum_then_double
sum_then_double:

call sum_to # body part
li t0, 2
mul a0, a0, t0

ret
```
Q: what's the difference between the above code and the code on the previous page?

<!-- https://blog.csdn.net/zoomdy/article/details/79354502 RISC-V Assembly Programmer's Manual
https://shakti.org.in/docs/risc-v-asm-manual.pdf
https://github.com/riscv-non-isa/riscv-asm-manual/blob/master/riscv-asm.md
-->

---
<style scoped>
{
  font-size: 32px
}
</style>
Outline

1. Experimental Objectives And Ideas
2. Experimental Requirements
3. Practical Steps
4. Code Structure
5. Memory Layout
6. Verify the Startup Process based on GDB
7. Function Calls
### 8. LibOS Initialization
9. SBI Calls

---
<style scoped>
{
  font-size: 32px
}
</style>
#### Allocate and Use the Startup Stack

Allocate and use the startup stack *[Quickstart RISC-V assembly documentation](https://github.com/riscv-non-isa/riscv-asm-manual/blob/master/riscv-asm.md)*
```
# os/src/entry.asm
     .section.text.entry
     .globl_start
_start:
     la sp, boot_stack_top
     call rust_main

     .section .bss.stack
     .globl boot_stack
boot_stack:
     .space 4096 * 16
     .globl boot_stack_top
boot_stack_top:
```

---
#### Allocate and Use the Startup Stack
```
# os/src/linker-qemu.ld
.bss : {
     *(.bss.stack)
     sbss = .;
     *(.bss.bss.*)
     *(.sbss.sbss.*)
}
ebss = .;
```
In the linker script linker.ld the .bss.stack section will eventually be pooled into the .bss section
The .bss section generally places data that needs to be initialized to zero


---
#### Transfer of Control: ASM --> Rust/C

Transfer control to Rust code, the entry point is the ``rust_main`` function in main.rs
```rust
// os/src/main.rs
pub fn rust_main() -> ! {
     loop {}
}
```
- ``fn`` keyword: function; ``pub`` keyword: externally visible, public
- ``loop`` keyword: loop



---
#### Clear the bss Segment

Clear the bss segment (uninitialized data segment)
```Rust
pub fn rust_main() -> ! {
     clear_bss(); //call clear bss function clear_bss()
}
fn clear_bss() {
     extern "C" {
         fn sbss(); //Start address of bss segment
         fn ebss(); //end address of bss segment
     }
     //Clear the memory space of [sbss..ebss]
     (sbss as usize..ebss as usize).for_each(|a| {
         unsafe { (a as *mut u8).write_volatile(0) }
     });
}
```


---
<style scoped>
{
  font-size: 32px
}
</style>
Outline

1. Experimental Objectives And Ideas
2. Experimental Requirements
3. Practical Steps
4. Code Structure
5. Memory Layout
6. Verify the Startup Process based on GDB
7. Function Calls
8. LibOS Initialization
### 9. SBI Calls

---
<style scoped>
{
  font-size: 30px
}
</style>
#### SBI service interface
Print Hello world! to the screen
* SBI service interface
     - Supervisor Binary Interface
     - Services provided by lower-level software to the operating system
* RustSBI
     - Implement basic SBI services
     - Follow the SBI calling convention
![bg right:50% 100%](figs/rv-privil-arch.png)
---

#### SBI Service Number
```rust
// os/src/sbi.rs
const SBI_SET_TIMER: usize = 0;
const SBI_CONSOLE_PUTCHAR: usize = 1;
const SBI_CONSOLE_GETCHAR: usize = 2;
const SBI_CLEAR_IPI: usize = 3;
const SBI_SEND_IPI: usize = 4;
const SBI_REMOTE_FENCE_I: usize = 5;
const SBI_REMOTE_SFENCE_VMA: usize = 6;
const SBI_REMOTE_SFENCE_VMA_ASID: usize = 7;
const SBI_SHUTDOWN: usize = 8;
```
- ``usize`` machine word-sized unsigned int
---
#### Assembly-level SBI Calls

```rust
// os/src/sbi.rs
#[inline(always)] //Always expand the function
fn sbi_call(which: usize, arg0: usize, arg1: usize, arg2: usize) -> usize {
     let mut ret; // modifiable variable ret
     unsafe {
         asm!(//Inline assembly
             "ecall", //Switch to a higher privilege level machine instruction
             inlateout("x10") arg0 => ret, //SBI parameter 0& return value
             in("x11") arg1, //SBI parameter 1
             in("x12") arg2, //SBI parameter 2
             in("x17") which, //SBI number
         );
     }
     ret // return ret value
}
```
---
#### SBI Calls: Output Characters

Print a character on the screen
```rust
// os/src/sbi.rs
pub fn console_putchar(c: usize) {
     sbi_call(SBI_CONSOLE_PUTCHAR, c, 0, 0);
}
```
Implement formatted output
- Write a println! macro based on console_putchar 

---
#### SBI Call: Shutdown
```rust
// os/src/sbi.rs
pub fn shutdown() -> ! {
     sbi_call(SBI_SHUTDOWN, 0, 0, 0);
     panic!("It should shutdown!");
}
```
- ``panic!`` and ``println!`` are macros (like C macro), and ``!`` means  macro
---
#### Handle Error Panic Gracefully
```rust
#[panic_handler]
fn panic(info: &PanicInfo) -> ! { //PnaicInfo is a structure type
     if let Some(location) = info.location() { //Does the error location exist?
         println!(
             "Panicked at {}:{} {}",
             location.file(), //error file name
             location.line(), //The number of lines in the error file
             info.message().unwrap() //error message
         );
     } else {
         println!("Panicked: {}", info. message(). unwrap());
     }
     shutdown() //shutdown
}
```

---
#### LibOS Full Features
Handle panics gracefully
```rust
pub fn rust_main() -> ! {
     clear_bss();
     println!("Hello, world!");
     panic!("Shutdown machine!");
}
```
output:
```
[RustSBI output]
Hello, world!
Panicked at src/main.rs:26 Shutdown machine!
```

---
### Summary
- Knowledge points that need to be mastered in the practice of constructing various OS (principle & implementation)
- Understand the relationship between Compiler/OS/Machine
- Know the process from machine startup to application printing out strings
- Can write Trilobita OS