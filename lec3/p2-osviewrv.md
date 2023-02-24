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

# Lecture 3 Isolation and batch processing based on privilege level
## Section2  looks at RISC-V from the perspective of OS

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

<br>
<br>

Spring 2023

---

**Outline**

### 1. Comparison for Mainstream Processors
2. RISC-V system mode
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
5. RISC-V system programming: kernel programming

---
#### The main goal of this section

- Understand RISC-V privilege levels and hardware isolation mechanisms.
- Understand the basic characteristics of RISC-V's M-Mode and S-Mode
- Understand how  OS **accesses and controls** computer systems in M-Mode and S-Mode
- Understand how different softwares **switch between M-Mode<–>S-Mode<–>U-Mode**
---
#### Mainstream CPU Comparison
<!-- Mainly explain that x86, arm due to compatibility and historical reasons, lead to complex design and implementation, riscv is simple/flexible/extensible, easy to learn and master and used to write OS -->
![w:1150](figs/mainstream-isas.png)

---
#### Mainstream CPU Comparison
* Due to compatibility and history reasons, the design and implementation of x86 and ARM are complicated
* RISC-V is concise/flexible/extensible

![w:1150](figs/x86-arm-rv-compare.png)


---
**Outline**

1. Mainstream CPU Comparison
### 2. RISC-V system mode
   - overview
   - Privileged
   - CSR register
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
5. RISC-V system programming: kernel programming

---

#### RISC-V related terms
- Application Execution Environment (AEE)
- Application Binary Interface (ABI)
- Supervisor Binary Interface (SBI)
- Supervisor Execution Environment (SEE)
- Hypervisor: virtual machine monitor
- Hypervisor Binary interface (HBI)
- Hypervisor Execution Environment (HEE)


---
<style scoped>
{
  font-size: 30px
}
</style>

#### RISC-V system mode
![w:800](figs/rv-privil-arch.png)
- ABI/SBI/HBI:Application/Supervisor/Hypervisor Bianry Interface
- AEE/SEE/HEE:Application/Superv/Hyperv Execution Environment
- HAL: Hardware Abstraction Layer
- Hypervisor, virtual machine monitor (VMM)
- RISC-V system mode: RISC-V mode related to system programming



---
<style scoped>
{
  font-size: 30px
}
</style>

#### RISC-V System Mode: Single Application Scenario
![w:800](figs/rv-privil-arch.png)
- Different software layers have clear privilege-level hardware isolation support
- The **single app** on the left is encoded to run on the ABI
- ABI is the interface for interaction between user-level ISA (Instruction Set Architecture) and AEE
- ABI hides details of AEE to the application, making AEE more flexible

---
<style scoped>
{
  font-size: 30px
}
</style>

#### RISC-V System Mode: Operating System Scenarios
![w:800](figs/rv-privil-arch.png)
- A **traditional operating system** is added in the middle layer to support multi-tasking of multiple applications
- Each application communicates with the OS via **ABI**
- The RISC-V operating system communicates with SEE through **SBI**
- SBI is the interface for the interaction between the OS kernel and SEE, and supports the ISA of the OS

---
#### RISC-V system mode: virtual machine scenario
![w:800](figs/rv-privil-arch.png)
- The virtual machine scene on the right can support **multiple operating systems**



---
#### RISC-V System Mode: Application Scenarios
![w:800](figs/rv-privil-arch.png)
- M Mode: small devices (bluetooth headsets, etc.)
- U+M Mode: Embedded device (TV remote control, credit card machine, etc.)
- U+S+M Mode: mobile phone
- U+S+H+M Mode: data center server

---
<style scoped>
{
  font-size: 28px
}
</style>

#### RISC-V System Mode: Hardware Threading
![w:800](figs/rv-privil-arch.png)
- Privilege level is a protection mechanism provided for different software stack components
- **Hardware thread** (hart, ie CPU core) is running at one specific privilege level (CSR configuration)
- When processor performs a forbidden operation in current privilege level, an **exception** will be generated. These exceptions usually generate a trap, which causes the **lower-level execution environment to take over control right**

---
**Outline**

1. Mainstream CPU Comparison
2. RISC-V system mode
   - overview
   - **Privilege level**
   - CSR register
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
5. RISC-V system programming: kernel programming

---

#### RISC-V system mode: multiple privilege levels
![w:800](figs/rv-privil-arch.png)
-Modern processors generally have multiple privilege levels
- **U**: User | **S**: Supervisor | **H**: Hypervisor | **M**: Machine

Why these **4 levels**? What are their **differences and connections**?




---
#### RISC-V System Mode: Execution Environment
| Execution Environment | Encoding | Meaning | Across Privilege Levels |
| --- | --- | --------------------- | --- |
| APP | 00 | User/Application | ``ecall`` |
| OS | 01 | Supervisor | ``ecall`` ``sret`` |
| VMM | 10 | Hypervisor | --- |
| BIOS | 11 | Machine | ``ecall`` ``mret`` |

- Hardware system consisting of M, S, U modes is suitable for running UNIX-like operating systems


---
#### RISC-V system mode: Flexible combination of privilege levels
![w:800](figs/rv-privil-arch.png)
- **Flexible** and **combinable** hardware structures are required as **requirements of applications change**  
- Therefore, the above four modes appeared. Meanwhile, the hardware design that can be combined between the modes will be flexible.

---
#### RISC-V system mode: user mode
![w:800](figs/rv-privil-arch.png)
- U-Mode (User Mode, user mode, user mode)
   - **Unprivileged** mode :  Fundamental computing
   - U-Mode is the CPU execution mode when **application is running**
   - Cannot execute privileged instructions and cannot directly affect the execution of other applications


---
<style scoped>
{
  font-size: 28px
}
</style>
#### RISC-V system mode: kernel mode
![w:800](figs/rv-privil-arch.png)
- S-Mode (Supervisor Mode, Kernel Mode)
   - The operating system in the kernel mode has sufficient **hardware control capability**
   - Privileged Mode: **Limit execution and memory access of APPs** 
   - S-Mode is the CPU execution mode when **operating system is running**
   - Can execute kernel-mode privileged instructions, can directly **affect application program execution**

---
<style scoped>
{
  font-size: 30px
}
</style>
#### RISC-V system mode: H-Mode
![w:800](figs/rv-privil-arch.png)
- H-Mode (Hypervisor Mode, Virtual Machine Mode)
   - Privileged mode: **Limit the memory space accessed by the OS**
   - H-Mode is the CPU execution mode of **virtual machine monitor operation**, which can execute H-Mode privileged instructions and affect OS execution directly**


---
<style scoped>
{
  font-size: 30px
}
</style>
#### RISC-V system mode: M-Mode
![w:800](figs/rv-privil-arch.png)
- M-Mode (Machine Mode, Physical Machine Mode)
   - Privileged mode: **control physical memory**, directly shut down
   - M-Mode is the CPU execution mode when **Bootloader/BIOS is running**
   - Can execute M-Mode privileged instructions, which can directly affect the execution of other software mentioned above

---
**Outline**

1. Mainstream CPU Comparison
2. RISC-V system mode
   - overview
   - Privileged
   - **CSR register**
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
5. RISC-V system programming: kernel programming

---

#### RISC-V CSR register classification

- **General purpose registers** x0-x31
   - Accessed by general instructions
   - The lowest but fastest storage level that unprivilege instructions can get access to.
- **Control and Status Register** (CSR)
   - Accessed via the **Control Status Register Instruction**, there can be 4096 CSRs
   - Applications running in **User Mode** cannot access most of the CSR registers
   - The operating system running in **kernel mode** controls the computer by accessing the CSR register

<!---
## RISC-V system mode: control status register CSR
Mandatory isolation to avoid availability/reliability/security impact on the entire system -->
---
<style scoped>
{
  font-size: 32px
}
</style>
#### Isolation via CSR register
OS ensures the safety and reliability of the computer through hardware isolation.
- Set CSR to achieve isolation
   - Right: Prevent applications from accessing system-control registers
     - **ADDRESS SPACE CONFIGURATION** register: mstatus/sstatus CSR
   - Time: Prevent apps from occupying 100% CPU for long time
     - **interrupt configuration** register: sstatus/stvec CSR
   - Data: Prevent app from damaging or stealing data
     - **Address space related** registers: sstatus/satp/stvec CSR

<!---
## RISC-V 系统模式：控制状态寄存器CSR

- mtvec(MachineTrapVector)保存发生异常时需要跳转到的地址。
- mepc(Machine Exception PC)指向发生异常的指令。
- mcause(Machine Exception Cause)指示发生异常的种类。
- mie(Machine Interrupt Enable)指出处理器目前能处理的中断。
- mip(Machine Interrupt Pending)列出目前正准备处理的中断。
- mtval(Machine Trap Value)保存陷入(trap)附加信息:地址例外中出错的地址、发生非法指令例外的指令本身；对于其他异常，值为0。
- mscratch(Machine Scratch)它暂时存放一个字大小的数据。
- mstatus(Machine Status)保存全局中断以及其他的状态
-->

<!-- 
---
## RISC-V 系统模式：控制状态寄存器CSR

- mcause(Machine Exception Cause)它指示发生异常的种类。
  - SIE控制S-Mode下全局中断，MIE控制M-Mode下全局中断。
  - SPIE、MPIE记录发生中断之前MIE和SIE的值。
---
## RISC-V 系统模式：控制状态寄存器CSR

- sstatus(supervisor status)保存发生异常时需要跳转到的地址。
- stvec(supervisor trap vector)保存s模式的trap向量基址。stvec总是4字节对齐
- satp(supervisor Address Translation and Protection) S-Mode控制状态寄存器控制了分页系统。
- sscratch (supervisor Scratch Register) 保存指向hart-local supervisor上下文的指针. 在trap处理程序的开头，sscratch与用户寄存器交换，以提供初始工作寄存器。
- sepc(supervisor Exception PC)它指向发生异常的指令。
-->

---
<style scoped>
{
  font-size: 32px
}
</style>

**Outline**

1. Mainstream CPU Comparison
2. RISC-V system mode
### 3. RISC-V system programming: user mode programming
   - brief description
   - U-Mode programming: system calls
   - Privileged operations
4. RISC-V system programming: M-Mode programming
5. RISC-V system programming: kernel programming

---

#### System Programming
- System programming needs to understand the **privilege level architecture** of the processor, and be familiar with the allowed register resources, memory resources and peripheral resources of each privilege level
- **Write kernel-level code**, construct the operating system, and support application execution
   - Memory management, process scheduling
   - Exception handling, interrupt handling
   - System calls, Peripheral control
- System programming is usually **not** supported by extensive user **programming libraries** and convenient dynamic **debugging tools**
- System programming of this course mainly focuses on S-Mode and U-Mode of RISC-V, involving some understanding of M-Mode

---
#### RISC-V U-Mode programming: using system calls
- Applications under U-Mode cannot directly use physical resources of the computer
- Environment call exception: Occurs when executing ``ecall``, which is equivalent to a system call
- OS can access physical resources directly
- What to do if the application needs to use hardware resources?
   - print "hello world" to the screen
   - read data from file
- Obtain services from OS through system calls

---
#### U-Mode programming:  "hello world"
[Execution flow of printing "hello world" in user mode](https://github.com/chyyuu/os_kernel_lab/tree/v4-kernel-sret-app-ecall-kernel/os/src)

![w:1000](figs/print-app.png)


---
#### Start execution of the first example
[Start flow of printing "hello world" in user mode](https://github.com/chyyuu/os_kernel_lab/blob/v4-kernel-sret-app-ecall-kernel/os/src/main.rs#L302)

![w:1000](figs/boot-print-app.png)



---
#### Example 2: Execute privileged instructions in user mode
[Start and execution flow of executing privileged instructions in user mode](https://github.com/chyyuu/os_kernel_lab/blob/v4-illegal-priv-code-csr-in-u-mode-app-v2/os/src/ main.rs#L306)

![w:1000](figs/boot-priv-code-app.png)


<!-- Zifencei extension https://www.cnblogs.com/mikewolf2002/p/11191254.html -->
---
#### Privileged Operations
- Privileged operations: Privileged instructions and CSR read/write operations
- Very few instructions:
   - ``mret`` return from machine mode(M-Mode)
   - ``sret`` return from supervisor mode(S-Mode)
   - ``wfi`` wait for interrupt
   - ``sfence.vma`` virtual address barrier instruction
  
- Many other system management functions are implemented by reading/writing CSR
Note: ``fence.i`` is an i-cache barrier instruction, a unprivileged instruction, which belongs to the "Zifencei" extended specification

<!-- Before executing the fence.i instruction, for the same hardware thread (hart), RISC-V does not guarantee that the data written to the memory instruction area by the store instruction can be fetched by the fetch instruction. After using the fence.i command, for the same hart, you can ensure that the command reads the data that was written to the memory command area recently. However, fence.i will not guarantee that the reading of other riscv hart instructions can also meet the read and write consistency. If you want to make the write instruction memory space meet the consistency requirements for all harts, you need to execute the fence instruction. -->


---
<style scoped>
{
  font-size: 32px
}
</style>
**Outline**

1. Mainstream CPU Comparison
2. RISC-V system mode
3. RISC-V system programming: user mode programming
### 4. RISC-V system programming: M-Mode programming
   - Interrupt mechanism and exception mechanism
   - Hardware response to interrupt/exception
   - Handover of control for interrupt/exception handling
5. RISC-V system programming: kernel programming

---
<style scoped>
{
  font-size: 30px
}
</style>
#### M-Mode programming
- M-Mode is the **highest privilege mode** of hart (hardware thread) in RISC-V
- In M-Mode, hart has **full access to the underlying functions of the computer system**
- The most important feature of M-Mode is **intercept and handle interrupt/exception**
   - **Synchronous exception**: generated during execution, i.e. accessing an invalid register address, or executing an instruction with an invalid opcode
   - **Asynchronous Interrupt**: Asynchronous external events or interrupts, such as clock interrupts
- RISC-V requires the implementation of **precise exception**: to ensure that all instructions before the exception are fully executed, and subsequent instructions have not started to execute

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Interrupt CSR in M-Mode

- mtvec(MachineTrapVector) saves the **interrupt handler entry address** when an interrupt/exception occurs
- mepc(Machine Exception PC) saves the **instruction PC** when an interrupt/exception occurred
- mcause(Machine Exception Cause) indicates the type of **interrupt/exception**
- mie(Machine Interrupt Enable) interrupt **enable** register
- mip (Machine Interrupt Pending) interrupt **request** register
- mtval(Machine Trap Value) saves  **Additional Information** of the trap
- mscratch (Machine Scratch) temporarily stores a word-sized **data**
- mstatus(Machine Status) saves global interrupts and other **status**

<!-- mtval (Machine Trap Value) saves the additional information of the trap: the address of the error in the address exception, the instruction itself where the exception of the illegal instruction occurred; for other exceptions, the value is 0. -->
---
<style scoped>
{
  font-size: 28px
}
</style>
#### Mstatus CSR register

- mstatus(Machine Status) saves global interrupts and other **status**
   - SIE controls the global interrupt in S-Mode, and MIE controls the global interrupt in M-Mode.
   - SPIE, MPIE saves old values of MIE and SIE before the interrupt occurred.
   - SPP indicates the privilege level is S-Mode or U-Mode before the change
   - MPP indicates it is S-Mode, U-Mode or M-Mode before
   <br>
   PP: Previous Privilege


![w:1000](figs/mstatus.png)


---
#### Mcause CSR register

When an exception occurs, mcause CSR will record a code indicating **the event type**. If the event is caused by an interrupt, the ``Interrupt`` bit is set. ``Exception Code`` field contains the code indicating last exception.

![w:1150](figs/rv-cause.png)

---
#### M-Mode Clock Interrupt Timer
- Interrupts happen asynchronously
   - Signals from external I/O devices external to the processor
- Timer can generate interrupts in a stable and regular manner
   - Prevent the application program from occupying the CPU for a long time, so that the OS Kernel can get the execution right...
   - **Software in high privilege mode** can control CPU
   - Software in high privilege mode can **authorize** softwares in low privilege mode to handle interrupts

---
#### RISC-V processor FU540 module diagram
![w:650](figs/fu540-top-block.png) 

---
<style scoped>
{
  font-size: 32px
}
</style>
**Outline**

1. Mainstream CPU Comparison
2. RISC-V system mode
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
   - Interrupt mechanism and exception mechanism
### Interrupt/exception hardware response
   - Handover of control for interrupt/exception handling
5. RISC-V system programming: kernel programming

---
<style scoped>
{
  font-size: 25px
}
</style>
#### Hardware's response to M-Mode interrupt
- The PC of the **exception instruction** is saved in mepc, and PC is set to mtvec.
   - For synchronous exceptions, mepc points to the instruction that caused the exception;
   - For interrupts(asynchronous), mepc points to next instruction after the current executing instruction.
- Set mcause according to **exception source**, and set mtval to error address or other reserved information word 
- Set mstatus[MIE bit] to zero to **disable interrupts** and **save previous MIE value** in MPIE
     - SIE controls the global interrupt in S mode, MIE controls the global interrupt in M mode;
     - SPIE records the value before SIE interrupt, and MPIE records the value before MIE interrupt
- **Save the privilege mode before the exception occurred** in MPP of mstatus, and then **change the privilege mode** to M. (MPP indicates that the privilege level before the change is S, M or U mode)

---
#### M-Mode interrupt handler
```
     let cause = cause::read();
     let stval = stval::read();

     match cause. cause() {
         Trap::Exception(Exception::UserEnvCall) => {
             cx.sepc += 4;
             cx.x[10] = do_syscall(cx.x[17], [cx.x[10], cx.x[11], cx.x[12]]) as usize;
         }
         _ => {
             panic!(
                 "Unsupported trap {:?}, stval = {:#x}!",
                 cause. cause(),
                 stval
             );
         }
     }

```
---

#### M-Mode interrupt classification
MIE of mcause register indicates the type of interrupt:
- **Software** interrupt: Triggered by writing data to a memory-mapped register, one hart interrupts another hart (interprocessor interrupt)
- **Clock** interrupt: hart's mtime register is greater than mtimecmp register
- **External** interrupt: Triggered by the interrupt controller, from peripherals connected to this interrupt controller

---
#### RISC-V Interrupts/Exceptions
Some bits of mcause register indicates the interrupt source.
The first column 1 represents interrupts, the second column represents interrupt IDs, and the third column is explanation of interrupts.
![w:1000](figs/rv-interrupt.png)


---

#### M-Mode RISC-V exception mechanism
Some bits of mcause register indicates the exception details.
The first column 0 represents exceptions, the second column represents exception IDs, and the third column is explanation of exceptions.
![bg right:45% 100%](figs/rv-exception.png)

---
#### Hardware response to M-Mode interrupts/exceptions 
- The PC of the interrupt/exception instruction is stored in mepc, and the PC is set to mtvec.
    - For exceptions, mepc points to the instruction caused the exception
    - For interrupts, mepc points to where execution should resume after finishing handling interrupt
- Set mcause based on the **interrupt/exception source** and set mtval to the address of the error or other information word suitable for the specific exception.

---
#### Hardware response to M-Mode interrupts/exceptions
- Set mstatus[MIE bit] to zero to **disable interrupts** and **save previous MIE value** in MPIE
     - SIE controls the global interrupt in S mode, MIE controls the global interrupt in M mode;
     - SPIE records the value before SIE interrupt, and MPIE records the value before MIE interrupt
- **Save the privilege mode before the exception occurred** in MPP of mstatus, and then **change the privilege mode** to M. (MPP indicates that the privilege level before the change is S, M or U mode)
- Jump to the address configured by mtvec CSR.

---
<style scoped>
{
  font-size: 32px
}
</style>
**Outline**

1. Mainstream CPU Comparison
2. RISC-V system mode
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
   - Interrupt mechanism and exception mechanism
   - Interrupt/exception hardware response
###  Handover of control for interrupt/exception handling
5. RISC-V system programming: kernel programming


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Transfer of control for interrupt/exception handling in M-Mode
- By default, all interrupts/exceptions result in the transfer of control to the interrupt/exception handler in M-Mode.
- The interrupt/exception handler in M-Mode can redirect the interrupt/exception to S-Mode, but this additional operation slows down the processing speed of interrupts/exceptions.
- RISC-V provides an interrupt/exception delegation mechanism, which selectively delegates interrupts/exceptions to S-Mode for handling, completely bypassing M-Mode.


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Transfer of control for interrupt/exception handling in M-Mode
- **mideleg/medeleg** (Machine Interrupt/Exception Delegation) CSR controls which interrupts/exceptions are delegated to S-Mode for processing
- Each of mideleg/medeleg corresponds to an interrupt/exception
   - For example, mideleg[5] corresponds to the S-Mode clock interrupt. If it is set, clock interrupt in S-Mode will be delegated to the S-Mode interrupt/exception handler rather than M-Mode interrupt/exception handler program.
   - Any interrupts delegated to S-Mode can be masked by S-Mode software. sie (Supervisor Interrupt Enable) and sip (Supervisor Interrupt Pending) CSR are S-Mode control status registers

---

#### Interrupt delegation register mideleg
- Mideleg (Machine Interrupt Delegation) controls which interrupts will be delegated to S-mode processing
- Each bit in mideleg corresponds to an interrupt/exception
   - mideleg[1] is used to control whether **inter-core interrupt** is delegated to S-Mode for processing
   - mideleg[5] is used to control whether to delegate **timing interrupt** to S-Mode for processing
   - mideleg[9] is used to control whether to delegate **external interrupt** to S-Mode for processing


---

#### Exception delegation register medeleg
- Medeleg (Machine Exception Delegation) controls which exceptions are delegated to S mode for processing
- Each in the medeleg corresponds to an interrupt/exception
   - medeleg[1] is used to control whether to delegate the **instruction acquisition error exception** to S-Mode for processing
   - medeleg[12] is used to control whether to delegate **instruction page exception** to  S-Modefor processing
   - medeleg[9] is used to control whether **data page exception** is delegated to  S-Mode for processing

<!-- , is a subset of mie and mip. These two registers have the same layout as in M-Mode. In sie and sip, only bits corresponding to interrupts delegated by mideleg can be read and written, and bits corresponding to interrupts not delegated are always 0 -->

---
#### Transfer of control for interrupt/exception handling in M-Mode


- When an interrupt/exception occurs, processor control **usually** will not be delegated to a lower-level privileged mode
   - eg. medeleg[15] will delegate store page fault to S-Mode
   - Exceptions that occur in M-Mode are always handled in M-Mode
   - Exceptions in S-Mode are always handled in M-Mode, or in S-Mode
   - Exceptions that occur in the above two modes will not be handled by U-Mode
  
**Why?**


---
#### Thinking questions

- How to implement the breakpoint debugging through the breakpoint exception?
- How to implement single-stepping tracking?
---
**Outline**

1. Mainstream CPU Comparison
2. RISC-V system mode
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
### 5. RISC-V system programming: kernel programming
   - Interrupt/exception mechanism
   - Interrupt/exception handling
   - Virtual memory mechanism
---
<style scoped>
{
  font-size: 30px
}
</style>
#### S-Mode interrupt control and status register

- stvec(SupervisorTrapVector) saves the address **to jump to when an interrupt/exception occurs**
- sepc(Supervisor Exception PC) points to the **instruction when an interrupt/exception occurred**
- cause(Supervisor Exception Cause) indicates the **kind** of the interrupt/exception that occurred
- sie(Supervisor Interrupt Enable) interrupt **enable** register
- sip (Supervisor Interrupt Pending) interrupt **request** register
- stval(Supervisor Trap Value) saves trap (trap) **Additional Information**
- sscratch (Supervisor Scratch) different mode exchange **data transfer station**
- sstatus (Supervisor Status) saves global interrupts and other **status**

---
#### Sstatus register
- The SIE and SPIE bits of sstatus register respectively save the current interrupt enable **status** and status before the interrupt/exception occurs

![w:1100](figs/rv-sstatus.png)

---
#### S-Mode Interrupt/Exception Mechanism

**sie & sip register** is a CSR used to save **pending interrupt** and **interrupt enable**

- sie (supervisor interrupt-enabled register)
- sip (supervisor interrupt pending)

![w:1150](figs/rv-sie-sip.png)


---
<style scoped>
{
  font-size: 32px
}
</style>
#### Scause register
When an exception occurs, an event number indicating **source event of the interrupt/abnormal** is written into the CSR, recorded in the ``Exception Code`` field; if the event is caused by an interrupt, the ``Interrupt`` bit is set.
scause register

![w:1150](figs/rv-cause.png)


---
<style scoped>
{
  font-size: 32px
}
</style>
#### Mtvec & stvec registers
The trap-vector base address register (stvec CSR) is used to configure the **trap_handler address**
  - Including vector base address(BASE) and vector mode (MODE): the value in the BASE field is 4-byte aligned
     - MODE = 0 means a trap_handler handles all interrupts/exceptions
     - MODE = 1 means each interrupt/exception has a corresponding trap_handler

mtvec & stvec registers
![w:1000](figs/rv-tvec.png)



---
**Outline**

1. Mainstream CPU Comparison
2. RISC-V system mode
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
5. RISC-V system programming: kernel programming
   - Interrupt/exception mechanism
   - **Interrupt/Exception Handling**
   - Virtual memory mechanism
---
<style scoped>
{
  font-size: 32px
}
</style>
#### Hardware response to S-Mode interrupts/exceptions

**Hardware Execution Content**

The hart accepts the interrupt/exception and needs to delegate them to S-Mode, then the hardware will undergo the following state transitions atomically

1. **Interrupt/abnormal instruction PC** is stored in sepc, and PC is set to stvec
2. scause sets interrupt/abnormal **type**, stval is set to error address/exception **related information**
3. Set the SIE bit in sstatus to zero, **mask the interrupt**, previous  **SIE** is saved in the SPIE.


---
#### Hardware response to S-Mode interrupts/exceptions

4. **The privilege mode before the exception occurred** is saved in the SPP (previous privilege) field of sstatus, and then set the current privilege mode to S-Mode
5. **Jump** to the address set by stvec CSR to continue execution


---
#### Software Handling for S-Mode Interrupt/Exception 


- **Initialization**
   - Write interrupt/exception handling routines (such as trap_handler)
   - Set trap_handler address to stvec
- Software execution
   1. The processor jumps to **trap_handler**
   2. trap_handler **handling** interrupt/exception/system call, etc.
   3. **Return** to the previous instruction and the previous privilege level to continue execution


---
**Outline**

1. Mainstream CPU Comparison
2. RISC-V system mode
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
5. RISC-V system programming: kernel programming
   - Interrupt/exception mechanism
   - Interrupt/exception handling
   - **Virtual storage mechanism**
---
<style scoped>
{
  font-size: 27px
}
</style>
#### S-Mode virtual memory system

- Virtual addresses divide memory into **fixed-size pages** for **address translation** and **content protection**.
- satp (Supervisor Address Translation and Protection)  **controls paging**. satp has three domains:
   - The MODE field can **turn on paging** and select the number of page table levels
   - ASID (Address Space Identifier) field is optional, it can be used to reduce the overhead of context switching
   - The PPN field records the physical page number of the **root page table**
![w:900](figs/satp.png)

---
#### S-Mode Virtual Storage Mechanism

- Establish **page table base address** through stap CSR
- Create **page tables** for OS and APP
- Handle memory access **Exception**


![bg right:50% 100%](figs/riscv_pagetable.svg)

---
<style scoped>
{
  font-size: 32px
}
</style>
#### S-Mode virtual memory address translation
In S, U-Mode, the virtual address will be converted to a physical address by traversing the page table from the root:

- satp.PPN gives the **first-level page table base address**, VA [31:22] gives the first-level page number, and the CPU will read the address located at (satp.PPN × 4096 + VA[31: 22 ] × 4) Page table entry.
- PTE contains **second-level page table base address**, VA[21:12] gives the second-level page number, CPU reads at address (PTE. PPN × 4096 + VA[21: 12] × 4) leaf Node page table entry.
- **The PPN field of the leaf node page table entry** and the page offset (the lowest 12 effective bits of the original virtual address) form the final result: physical address (LeafPTE.PPN×4096+VA[11: 0])


---
#### S-Mode virtual memory address translation

![w:650](figs/satp2.png)


<!-- ---
## RISC-V System Programming: Isolation in S-Mode
- S-Mode is more privileged than U-Mode, but less privileged than M-Mode
- Software running in S-Mode cannot use M-Mode CSR and instructions, and is restricted by PMP
- Support page-based virtual memory -->




---
### Summary

- Understand RISC-V privilege levels and hardware isolation methods
- Understand the basic characteristics of RISC-V's M-Mode and S-Mode
- Understand how the OS accesses and controls the computer system in M-Mode and S-Mode
- Learn how different software switches between M-Mode<–>S-Mode<–>U-Mode