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
## Section 2 Compiler and OS

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

<br>
<br>

Spring 2023

---
Outline

### 1. Hardware environment
2. Application execution environment
3. OS execution environment

---

#### Hardware Environment for Development
![bg right:60% 80%](figs/x86.png)

---

#### Hardware Environment for Deployment
![bg right:60% 85%](figs/rv.png)

---
Outline

1. Hardware environment
### 2. Application execution environment
3. OS execution environment

---

#### Compiler
- Source code --> assembly code
![bg right:55% 100%](figs/app-software-stack.png)
---

#### Assembler
- Assembly code --> machine code
![bg right:55% 100%](figs/app-software-stack.png)
---
#### Linker
- Multiple object files in machine code --> a single executable file in machine code
![bg right:55% 100%](figs/app-software-stack.png)

---

#### OS
- Load/execute/manage execution files in machine code
![bg right:50% 100%](figs/app-software-stack.png)


---
Outline

1. Hardware environment
2. Application execution environment
### 3. OS execution environment

---

#### Compiler/Assembler/Linker Workflow
- Source code ---> Assembly code ---> Machine code ---> Executable program
- Bootloader loads OS for execution

![bg right:45% 100%](figs/os-software-stack.png)


---

#### Executable file format
Triplet:
* CPU Architecture/Vendor/OS
```
rustc --print target-list | grep riscv
riscv32gc-unknown-linux-gnu
...
riscv64gc-unknown-linux-gnu
riscv64imac-unknown-none-elf
```
* ELF: Executable and Linkable Format

![bg right:45% 100%](figs/os-software-stack.png)


---

#### Link and Execution

![bg 60%](figs/link.png)

---
#### Function library
- Standard library: OS dependent
   - Rust: std standard library
   - C: glibc, musl libc
- Core library: OS independent 
   - Rust: core library
   - C: Linux/BSD kernel libc
![bg right:45% 100%](figs/os-software-stack.png)

---

<style scoped>
{
  font-size: 32px
}
</style>

#### Bare metal program
OS-type programs that are independent on OS (Bare Metal program)



```
// os/src/main.rs
#![no_std]
#![no_main]

mod lang_items;

// os/src/lang_items.rs
use core::panic::PanicInfo;

#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
     loop {}
}
```

---
#### ELF file format

File format
```
file target/riscv64gc-unknown-none-elf/debug/os
target/riscv64gc-unknown-none-elf/debug/os: ELF 64-bit LSB executable, UCB RISC-V,  …
```
[ELF file format](https://wiki.osdev.org/ELF) Executable and Linkable Format

---
#### ELF file format

![bg 55%](figs/elf.png)

---
#### File header information

File header information
```
rust-readobj -h target/riscv64gc-unknown-none-elf/debug/os
    File: target/riscv64gc-unknown-none-elf/debug/os
    Format: elf64-littleriscv
    Arch: riscv64
    AddressSize: 64bit
     …
    Type: Executable (0x2)
    Machine: EM_RISCV (0xF3)
    Version: 1
    Entry: 0x0
     …
    }
```

---
#### Export assembly code


Disassemble to export assembly code
```
rust-objdump -S target/riscv64gc-unknown-none-elf/debug/os
    target/riscv64gc-unknown-none-elf/debug/os: file format elf64-littleriscv
```
The main function is removed in the code and the project is set to #![no_main]
  - There is no traditional entry point (entry point: the location of the first executed instruction of the program)
  - The Rust compiler generates an empty program
  - This is a program for **OS development**

---
#### App/OS Memory Layout
- .text: data segment
- .rodata: initialized data segment, read-only global data (constant number or constant string)
- .data: modifiable global data
- .bss: uninitialized data segment
- The heap grows towards higher address
- The stack grows towards lower address
![bg right:45% 130%](figs/memlayout.png)
