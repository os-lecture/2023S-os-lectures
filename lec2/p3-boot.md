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
## Section 3 Hardware Startup and Software Startup

<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

<br>
<br>

Spring 2023

---
Outline

### 1. RISC-V development board
2. QEMU startup parameters and processes
3. x86 boot process

---
#### K210 Development Board
- 64-bit multi-core processor based on RISC-V
![w:450](figs/k210.png)

---
#### Nezha D1 development board
- 64-bit multi-core processor based on RISC-V
![w:600](figs/d1.png)

---
#### HiFive unmatched development board (U740)
- 64-bit multi-core processor based on RISC-V 
![w:1200](figs/sifive-hardware.png)

---
Outline

1. RISC-V development board
### 2. QEMU startup parameters and processes
3. x86 boot process

---

#### QEMU emulator

Using qemu-system-riscv64 software to emulate a 64-bit RISC-V-based computer, which contains:
- A CPU (adjustable to multi-core)
- A piece of physical memory
- Several I/O peripherals

---
#### QEMU startup parameters
```
qemu-system-riscv64 \
     -machine virt \
     -nographic \
     -bios ../bootloader/rustsbi-qemu.bin \
     -device loader, file=target/riscv64gc-unknown-none-elf/release/os.bin, addr=0x80200000
```
- -machine virt means to set the emulated 64-bit RISC-V machine as a virtual machine named virt
- The default size of physical memory is 128MiB

---
#### QEMU startup parameters
```
qemu-system-riscv64 \
     -machine virt \
     -nographic \
     -bios ../bootloader/rustsbi-qemu.bin \
     -device loader, file=target/riscv64gc-unknown-none-elf/release/os.bin, addr=0x80200000
```
-nographic indicates that the emulator does not need to provide a graphical interface, but only needs to output the character streams externally


---
#### QEMU startup parameters
```
qemu-system-riscv64 \
     -machine virt \
     -nographic \
     -bios ../bootloader/rustsbi-qemu.bin\
     -device loader, file=target/riscv64gc-unknown-none-elf/release/os.bin, addr=0x80200000
```
- -bios can set the bootloader that the QEMU emulator uses to initialize when it is powered on
- Use precompiled rustsbi-qemu.bin here

---
#### QEMU startup parameters
```
qemu-system-riscv64 \
     -machine virt \
     -nographic \
     -bios ../bootloader/rustsbi-qemu.bin\
     -device loader, file=target/riscv64gc-unknown-none-elf/release/os.bin, addr=0x80200000
```
- The loader parameter of device can load a file from a host machine into a specified location in QEMU's physical memory before the QEMU simulator starts up
- The file and addr parameters can respectively set the path of the file to be loaded and the physical address on QEMU's physical memory where the file will be loaded

---
#### QEMU startup process

```
qemu-system-riscv64 \
     -machine virt \
     -nographic \
     -bios ../bootloader/rustsbi-qemu.bin\
     -device loader, file=target/riscv64gc-unknown-none-elf/release/os.bin, addr=0x80200000
```
Generally speaking, the startup process after a computer is powered on can be divided into several stages, each of which is responsible for initialization by a layer of software. The function of each layer of software is to perform its initialization work and then jump to the entry address of the next layer of software, transferring control of the computer to the next layer of software.

---

<style scoped>
{
  font-size: 30px
}
</style>


#### QEMU startup process

```
qemu-system-riscv64 \
     -machine virt \
     -nographic \
     -bios ../bootloader/rustsbi-qemu.bin\
     -device loader, file=target/riscv64gc-unknown-none-elf/release/os.bin, addr=0x80200000
```
The startup process of QEMU simulation can be divided into three stages:
1. [Short piece of assembler program](https: //github.com/LearningOS/qemu/blob/386b2a5767f7642521cd07930c681ec8a6057e60/hw/riscv/virt.c#L536) initialize and jump to execute bootloader;
2. The bootloader is responsible for initializing and loading the OS, and jumping to OS execution;
3. Initialization is performed by the kernel.

---
Outline

1. RISC-V development board
2. QEMU startup parameters and process
### 3. x86 startup process

---

<style scoped>
{
  font-size: 33px
}
</style>


#### The boot process of a real computer (x86)
In fact, the boot process of the startup firmware based on x86 PCs has not fundamentally changed since the birth of the IBM PC.

1. Rom Stage: BIOS code is directly executed on ROM;
2. Ram Stage: Code is executed on RAM to detect and initialize chipsets, motherboards, etc.
3. Bootloader Stage: Bootloader is located on the storage device, loaded, and executed;
4. OS Stage: Bootloader initializes peripherals, locates the OS on the storage device, loads it, and executes it.