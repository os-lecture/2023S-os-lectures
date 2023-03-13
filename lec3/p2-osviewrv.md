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
<!-- In RISC-V mannual, privilege is used for "privilege level"-->

# Lecture 3 Privilege based Isolation and Batch Processing
## Section2  RISC-V : An OS's perspective

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

- Understand RISC-V privilege and hardware isolation mechanisms.
- Understand the basic characteristics of RISC-V's M-Mode and S-Mode
- Understand how  OS **accesses and controls** computer systems in M-Mode and S-Mode
- Understand how different softwares **switch between M-Mode<–>S-Mode<–>U-Mode**
---
#### Comparison for Mainstream Processors
<!-- Mainly explain that x86, arm due to compatibility and historical reasons, lead to complex design and implementation, riscv is simple/flexible/extensible, easy to learn and master and used to write OS -->
![w:1150](figs/mainstream-isas.png)

---
#### Comparison for Mainstream Processors
* Due to compatibility and history reasons, the design and implementation of x86 and ARM are complicated
* RISC-V is concise/flexible/extensible

![w:1150](figs/x86-arm-rv-compare.png)


---
**Outline**

1. Comparison for Mainstream Processors
### 2. RISC-V system mode
   - Overview
   - Privilege
   - CSR Registers
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
- RISC-V system modes:  modes related to system programming



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
- ABI hides the details of AEE to apps, making AEE more flexible

---
<style scoped>
{
  font-size: 30px
}
</style>

#### RISC-V System Mode: OS Scenario
![w:800](figs/rv-privil-arch.png)
- A **traditional OS** is added in the middle layer to support multi-tasking of multiple applications
- Each application communicates with OS via **ABI**
- The RISC-V OS communicates with SEE through **SBI**
- SBI is the interface for the interaction between the OS kernel and SEE, and supports the ISA of OS

---
#### RISC-V system mode: Virtual Machine Scenario
![w:800](figs/rv-privil-arch.png)
- The virtual machine on the right can support **multiple operating systems**



---
#### RISC-V System Mode: Application Scenarios
![w:800](figs/rv-privil-arch.png)
- M Mode: small devices (e.g., bluetooth earphones)
- U+M Mode: Embedded devices (e.g., credit card machine)
- U+S+M Mode: mobile phone
- U+S+H+M Mode: data center

---
<style scoped>
{
  font-size: 28px
}
</style>

#### RISC-V System Mode: Hardware Threading
![w:800](figs/rv-privil-arch.png)
- Privilege is a protection mechanism for different parts in the software stack.
- **Hardware thread** (hart, i.e., CPU core) is running at a specific privilege (CSR configurations)
- When processor performs a forbidden operation in current privilege, an **exception** will be generated. These exceptions usually generate traps, which causes the **lower-level execution environment to take over control right**

---
**Outline**

1. Comparison for Mainstream Processors
2. RISC-V system mode
   - Overview
   - **Privilege**
   - CSR registers
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
5. RISC-V system programming: kernel programming

---

#### RISC-V system mode: multiple privileges
![w:800](figs/rv-privil-arch.png)
-Modern processors generally have multiple privileges
- **U**: User | **S**: Supervisor | **H**: Hypervisor | **M**: Machine

Why **4 modes**? What are their **differences and connections**?




---
#### RISC-V System Mode: Execution Environment
| Execution Environment | Encoding | Meaning | Across Privileges |
| --- | --- | --------------------- | --- |
| APP | 00 | User/Application | ``ecall`` |
| OS | 01 | Supervisor | ``ecall`` ``sret`` |
| VMM | 10 | Hypervisor | --- |
| BIOS | 11 | Machine | ``ecall`` ``mret`` |

- Hardware system containing M, S, U modes is suitable for running UNIX-like OS


---
#### RISC-V system mode: Flexible combination of privileges
![w:800](figs/rv-privil-arch.png)
- **Flexible** and **combinable** hardware structures are required to meet the various **requirements of applications**  
- Therefore, the above 4 modes, as well as a flexible hardware design with different combinations of 4 modes, appeared.

---
#### RISC-V system mode: user mode
![w:800](figs/rv-privil-arch.png)
- U-Mode (User Mode)
   - **Unprivileged** mode : For fundamental computing
   - U-Mode is the CPU execution mode for **application running**
   - Cannot execute privileged instructions and cannot directly affect the execution of other applications


---
<style scoped>
{
  font-size: 28px
}
</style>
#### RISC-V system mode: supervisor mode
![w:800](figs/rv-privil-arch.png)
- S-Mode (Supervisor Mode, Kernel Mode)
   - OS in the S-Mode has sufficient **hardware control capability**
   - Privileged Mode: **Limit the execution and memory access of apps** 
   - S-Mode is the CPU execution mode when **OS is running**
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
   - H-Mode is the CPU execution mode of **Virtual Machine Monitor**, which can execute the privileged instructions of H-Mode and affect OS execution directly


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
   - M-Mode is the CPU execution mode for **Bootloader/BIOS running**
   - Can execute M-Mode privileged instructions, which can directly affect the execution of other software mentioned above

---
**Outline**

1. Comparison for Mainstream Processors
2. RISC-V system mode
   - Overview
   - Privilege
   - **CSR registers**
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
5. RISC-V system programming: kernel programming

---

#### Classification of RISC-V registers

- **General purpose registers** x0-x31
   - Accessed by general instructions
   - The fastest storage units that unprivileged instructions can get access to.
- **Control and Status Registers** (CSR)
   - Accessed via the **CSR Instructions**, there can be 4096 CSRs
   - Apps running in **User Mode** cannot access most CSR registers
   - OS running in **kernel mode** controls the computer by accessing the CSR register

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
OS ensures the security and reliability of the computer through hardware isolation.
- Set CSR to achieve isolation
   -  Access Control: Prevent apps from accessing system-control registers
      - **ADDRESS SPACE CONFIGURATION** register: mstatus/sstatus CSR
   - Time Management: Prevent apps from occupying 100% CPU for long time
     - **interrupt configuration** register: sstatus/stvec CSR
   - Data Protection: Prevent apps from destroying or stealing data
     - **Address space related** registers: sstatus/satp/stvec CSR

---
## Functionalities of different CSR Registers
- Information Registers: extract the current system information, such as chip ID and cpu core ID
- Trap Setting Registers: setting interrupt and exception 
- Trap Handling Registers: handling interrupt and exception 
- Memory Protection Registers: similar to the functionalities of mpu in cortex-m

Interrupts and exceptions are called traps in RISC-V
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

1. Comparison for Mainstream Processors
2. RISC-V system mode
### 3. RISC-V system programming: user mode programming
   - Brief introduction
   - U-Mode programming: system calls
   - Privileged operations
4. RISC-V system programming: M-Mode programming
5. RISC-V system programming: kernel programming

---

#### System Programming
- System programming needs to understand the **privilege architecture** of the processor, and be familiar with the allowed register, memory and peripheral resources of each privilege.
- **Write kernel-level code**, construct OS, and support app execution
   - Memory management, process scheduling
   - Exception handling, interrupt handling
   - System calls, Peripheral control
- There is usually no extensive **libs** and convenient **debugging tools**
- In this course, we mainly focus on S-Mode and U-Mode of RISC-V, and should understand some codes in M-Mode

---
#### RISC-V U-Mode programming: Using System Calls
- Apps in U-Mode cannot directly use the hardware resources
- Environment call exception: occurs when executing ``ecall``, which is equivalent to a system call
- OS can access hardware resources directly
- What to do if an app needs to use hardware resources?
   - print "hello world" to the screen
   - read data from file
- Obtain services from OS through system calls

---
#### U-Mode programming:  "hello world"
[Workflow of printing "hello world" in user mode](https://github.com/chyyuu/os_kernel_lab/tree/v4-kernel-sret-app-ecall-kernel/os/src)

![w:1000](figs/print-app.png)


---
#### Start execution of the first example
[Start and execution flow of printing "hello world" in user mode](https://github.com/chyyuu/os_kernel_lab/blob/v4-kernel-sret-app-ecall-kernel/os/src/main.rs#L302)

![w:1000](figs/boot-print-app.png)



---
#### Example 2: Execute privileged instructions in user mode
[Start and execution flow of executing privileged instructions in user mode](https://github.com/chyyuu/os_kernel_lab/blob/v4-illegal-priv-code-csr-in-u-mode-app-v2/os/src/main.rs#L306)

![w:1000](figs/boot-priv-code-app.png)


<!-- Zifencei extension https://www.cnblogs.com/mikewolf2002/p/11191254.html -->
---
#### Privileged Operations
- Privileged operations: privileged instructions and CSR read/write operations
- Very few instructions:
   - ``mret`` return from M-Mode, ``sret`` return from S-Mode, ``wfi`` wait for interrupt, ``sfence.vma`` virtual address barrier instruction for invalidating virtual memory or data synchronization
- Many other system management functions are implemented by reading/writing CSR
Note: ``fence.i`` is an i-cache barrier instruction, a unprivileged instruction, which belongs to the "Zifencei" extended specification. It is used to maintain consistency of i-cache and d-cache.

<!-- Before executing the fence.i instruction, for the same hardware thread (hart), RISC-V does not guarantee that the data written to the memory instruction area by the store instruction can be fetched by the fetch instruction. After using the fence.i command, for the same hart, you can ensure that the command reads the data that was written to the memory command area recently. However, fence.i will not guarantee that the reading of other riscv hart instructions can also meet the read and write consistency. If you want to make the write instruction memory space meet the consistency requirements for all harts, you need to execute the fence instruction. -->


---
<style scoped>
{
  font-size: 32px
}
</style>
**Outline**

1. Comparison for Mainstream Processors
2. RISC-V system mode
3. RISC-V system programming: user mode programming
### 4. RISC-V system programming: M-Mode programming
   - Interrupt mechanism and exception mechanism
   - Hardware response to interrupt/exception
   - Control shift for interrupt/exception handling
5. RISC-V system programming: kernel programming

---
<style scoped>
{
  font-size: 30px
}
</style>
#### M-Mode programming
- M-Mode is the **highest privilege mode** of hart (hardware thread) in RISC-V
- Hart can **fully access to the underlying functions of the computer system** under M-Mode.
- The most important feature of M-Mode is **intercept and handle interrupt/exception**
   - **Synchronous exceptions**: generated during instruction execution, i.e. accessing an invalid register address, or executing an instruction with an invalid opcode
   - **Asynchronous interrupts**: Asynchronous external events or interrupts, such as timer interrupts
- RISC-V requires the implementation of **precise exception**: to ensure that all instructions before the exception are fully executed, and subsequent instructions have not started to execute
---

#### How hardware handles Interrupt/Exception 
1. The PC of the **exception/interrupt instruction** is saved in sepc, and PC is set to stvec.
2. Set scause according to **interrupt/exception source**, and set stval to error address or other related information.
3. Set sstatus[SIE bit] to zero to **mask interrupts** and **save previous SIE value** into SPIE
4. **Save the privilege mode before the exception occurred** into SPP of sstatus, and then **change the privilege mode** to S. 
5. **Jump** to the address set by stvec CSR 
   - Heap & Stack：Save context；Switch page table.

---
<!--#### How software handles S-Mode Interrupt/Exception 

- **Initialization**
   - Write interrupt/exception handler (such as trap_handler)
   - Set trap_handler address to stvec
- Software execution
   1. The processor jumps to **trap_handler**
   2. trap_handler **handles** interrupt/exception/system call, etc.
   3. **Return** to the previous instruction and previous privilege to continue execution
-->

#### How hardware handles Interrupt/Exception 
- Hardware
  - Set interrupt flag
  - Call corresponding service according to Interrupt vector
- Software
  - Save context
  - Execute the interrupt service
  - Clear interrupt flag
  - Resume saved context
- Interrupt Vector：Interrupt--Interrupt Service，Exception--Exception Service，System call
---
#### Overhead of Interrupt/Exception
1. Finding corresponding services for Interrupt/Exception/System calls
2. Creating the kernel heap & stack
3. Checking the validity of syscall parameters
4. Copying kernel data to user mode. 
5. Change kernel status (overhead of cache/TLB flushing)
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
#### Mstatus register

- mstatus(Machine Status) saves global interrupts and other **status**
   - SIE controls the global interrupt in S-Mode, and MIE controls the global interrupt in M-Mode.
   - SPIE, MPIE saves old values of MIE and SIE before the interrupt occurrs.
   - SPP indicates the previous privilege is S-Mode or U-Mode (1->S-Mode, 0->U-Mode)
   - MPP indicates the previous privilege is S-Mode, U-Mode or M-Mode
   
   PP: Previous Privilege
![w:1000](figs/mstatus.png)


---
#### Mcause register

When an exception occurs, mcause CSR will record a code indicating **the event type**. If the event is caused by an interrupt, the ``Interrupt`` bit will be set. ``Exception Code`` field contains the code indicating the type of the last exception.

![w:1150](figs/rv-cause.png)

---
#### M-Mode Timer Interrupt
- Interrupts happen asynchronously
   - Signals from external I/O devices 
- Timer can generate interrupts in a stable and periodical manner
   - Prevent apps from occupying CPU for a long time, so that the OS Kernel can get control periodically...
   - **Software in higher privilege mode** can control CPU
   - Software in higher privilege mode can **authorize** softwares in lower privilege mode to handle interrupts

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

1. Comparison for Mainstream Processors
2. RISC-V system mode
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
   - Interrupt mechanism and exception mechanism
   - ### hardware response to interrupt/exception
   - control shift for interrupt/exception handling
5. RISC-V system programming: kernel programming

---
<style scoped>
{
  font-size: 25px
}
</style>
#### How hardware does for handling M-Mode interrupts
- The PC of the **exception/interrupt instruction** is saved in mepc, and PC is set to mtvec.
   - For synchronous exceptions, mepc points to the instruction that caused the exception;
   - For asynchronous interrupts, mepc points to the location where execution should be resumed after interrupt handling.
- Set mcause according to **interrupt/exception source**, and set mtval to error address or other reserved information word.
- Set mstatus [MIE bit] to zero to **disable interrupts** and **save previous MIE value** into MPIE
     - SIE controls the global interrupt of S-Mode, MIE controls the global interrupt of M-Mode;
     - SPIE records the value(before interruprt) of SIE, and MPIE records the previous value of MIE
- **Save the privilege mode before the exception occurred** into MPP of mstatus, and then **change the privilege mode** to M. (MPP indicates that the previous privilege is S, M or U mode)

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

#### Classification of M-Mode interrupts
MIE of mcause register indicates the type of interrupt:
- **Software** interrupt: Triggered by writing data to a memory-mapped register, one hart interrupts another hart (interprocessor interrupt)
- **Timer** interrupt: hart's mtime register is greater than mtimecmp register
- **External** interrupt: Triggered by the interrupt controller, from peripherals connected to this interrupt controller

---
#### RISC-V Interrupts/Exceptions
Some bits of mcause register indicates the interrupt source.
Column 1  represents interrupts, Column 2 represents interrupt IDs, and column 3 is the explanation of interrupts.
![w:1000](figs/rv-interrupt.png)


---

#### M-Mode RISC-V exception mechanism
Some bits of mcause register indicates the exception details.
Column 1(all 0) represents exceptions, column 2 represents exception IDs, and column 3 is the explanation of exceptions.
![bg right:45% 100%](figs/rv-exception.png)

---
#### How hardware does for handling M-Mode interrupts
- The PC of the interrupt/exception instruction is stored in mepc, and the PC is set to mtvec.
    - For exceptions, mepc points to the instruction caused the exception
    - For interrupts, mepc points to where execution should be resumed after finishing handling interrupt
- Set mcause based on the **interrupt/exception source** and set mtval to the address of the error or other reserved information word.

---
#### How hardware does for handling M-Mode interrupts
- Set mstatus[MIE bit] to zero to **disable interrupts** and **save previous MIE value** into MPIE
     - SIE controls the global interrupt under S-Mode, MIE controls the global interrupt under M-Mode;
     - SPIE records the value(before interruprt) of SIE, and MPIE records the previous value of MIE
- **Save the privilege mode before the exception occurred** into MPP of mstatus, and then **change the privilege mode** to M. (MPP indicates that the previous privilege is S, M or U mode)
- Jump to the address configured by mtvec CSR.

---
<style scoped>
{
  font-size: 32px
}
</style>
**Outline**

1. Comparison for Mainstream Processors
2. RISC-V system mode
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
   - Interrupt mechanism and exception mechanism
   - Interrupt/exception hardware response
   - ### Control shift for interrupt/exception handling
5. RISC-V system programming: kernel programming


---
<style scoped>
{
  font-size: 30px
}
</style>
####  Handover of control for interrupt/exception handling under M-Mode 
- By default, all interrupts/exceptions will handover CPU control right to interrupt/exception handler in M-Mode.
- The interrupt/exception handler in M-Mode can redirect the interrupt/exception to S-Mode, but this additional operation will slow down the processing speed of interrupts/exceptions.
- RISC-V provides an interrupt/exception delegation mechanism, which selectively delegates interrupts/exceptions to S-Mode for handling, completely bypassing M-Mode.


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Handover of control for interrupt/exception handling under M-Mode
- **mideleg/medeleg** (Machine Interrupt/Exception Delegation) CSR controls which interrupts/exceptions are delegated to S-Mode for processing
- Each bit of mideleg/medeleg corresponds to an interrupt/exception
   - For example, mideleg[5] corresponds to the S-Mode clock interrupt. If it is set, clock interrupt in S-Mode will be delegated to the S-Mode interrupt/exception handler rather than M-Mode interrupt/exception handler.
   - Any interrupts delegated to S-Mode can be masked by S-Mode software. sie (Supervisor Interrupt Enable) and sip (Supervisor Interrupt Pending) CSR are S-Mode control status registers

---

#### Interrupt delegation register mideleg
- Mideleg (Machine Interrupt Delegation) controls which interrupts will be delegated to S-mode for processing
- Each bit in mideleg corresponds to an interrupt/exception
   - mideleg[1] is used to control whether **inter-core interrupt** is delegated to S-Mode for processing
   - mideleg[5] is used to control whether to delegate **timer interrupt** to S-Mode for processing
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
#### Control shift for interrupt/exception handling


- When an interrupt/exception occurs, CPU control **usually** will not be delegated to a lower-level privileged mode
   - eg. medeleg[15] will delegate store page fault to S-Mode
   - Exceptions that occur in M-Mode are always handled in M-Mode
   - Exceptions in S-Mode are always handled in M-Mode, or in S-Mode
   - Exceptions that occur in the above two modes will not be handled by U-Mode
  
**Why?**


---
#### Questions

- How to implement the breakpoint debugging through the breakpoint exception?
- How to implement single-stepping tracking?
---
**Outline**

1. Comparison for Mainstream Processors
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
- sepc(Supervisor Exception PC) points to the **instruction when an interrupt/exception occurrs**
- cause(Supervisor Exception Cause) indicates the **types** of the interrupt/exception 
- sie(Supervisor Interrupt Enable) interrupt **enable** register
- sip (Supervisor Interrupt Pending) interrupt **request** register
- stval(Supervisor Trap Value) saves  **Additional Information** of the trap
- sscratch (Supervisor Scratch) is the **data transfer station** of different modes
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
When an exception occurs, an event number indicating **the source of the interrupt/exception** will be written into the CSR, recorded in the ``Exception Code`` field; if the event is caused by an interrupt, the ``Interrupt`` bit is set.


![w:1150](figs/rv-cause.png)


---
<style scoped>
{
  font-size: 32px
}
</style>
#### Mtvec & stvec registers
The trap-vector base address register (stvec CSR) is used to configure the **trap_handler address**
  - It includes vector base address(BASE) and vector mode (MODE): the value in the BASE field is 4-byte aligned
     - MODE = 0 means one trap_handler handles all interrupts/exceptions
     - MODE = 1 means each interrupt/exception has a corresponding trap_handler

mtvec & stvec registers
![w:1000](figs/rv-tvec.png)



---
**Outline**

1. Comparison for Mainstream Processors
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

**Hardware Execution**

The hart accepts the interrupt/exception and needs to delegate them to S-Mode, then the hardware will undergo the following state transitions atomically

1. **PC of executing instruction when Interrupt/exception occurs** is stored in sepc, and PC is set to stvec
2. scause sets interrupt/exception **type**, stval is set to error address/exception **related information**
3. Set the SIE bit of sstatus to zero, **mask the interrupt**, previous  **SIE** is saved in the SPIE.


---
#### Hardware response to S-Mode interrupts/exceptions

4. **The privilege mode before the exception occurred** is saved in the SPP (previous privilege) field of sstatus, and then set the current privilege mode to S-Mode
5. **Jump** to the address set by stvec CSR 


---
#### Software Handling for S-Mode Interrupt/Exception 


- **Initialization**
   - Write interrupt/exception handler (such as trap_handler)
   - Set trap_handler address to stvec
- Software execution
   1. The processor jumps to **trap_handler**
   2. trap_handler **handles** interrupt/exception/system call, etc.
   3. **Return** to the previous instruction and previous privilege to continue execution


---
**Outline**

1. Comparison for Mainstream Processors
2. RISC-V system mode
3. RISC-V system programming: user mode programming
4. RISC-V system programming: M-Mode programming
5. RISC-V system programming: kernel programming
   - Interrupt/exception mechanism
   - Interrupt/exception handling
   - **Virtual memory mechanism**
---
<style scoped>
{
  font-size: 27px
}
</style>
#### S-Mode virtual memory system

- Virtual addresses divide memory into **fixed-size pages** for **address translation** and **content protection**.
- satp (Supervisor Address Translation and Protection)  **controls paging**. satp has three fields:
   - The MODE field can **turn on paging** and select the number of page table levels
   - ASID (Address Space Identifier) field is optional, it can be used to reduce the overhead of context switching
   - The PPN field records the physical page number of the **root page table**
![w:900](figs/satp.png)

---
#### S-Mode Virtual Memory Mechanism

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
In S, U-Mode, a virtual address will be translated to a physical address by traversing the page table from the root:

- satp.PPN gives the **first-level page table base address**, VA [31:22] gives the first-level page number, and the CPU will read page table entries located at (satp.PPN × 4096 + VA[31: 22 ] × 4) .
- PTE contains **second-level page table base address**, VA[21:12] gives the second-level page number, and CPU will read leaf node page table entries located at (PTE. PPN × 4096 + VA[21: 12] × 4) .
- **The PPN field of the leaf node page table entry** and the page offset (the lowest 12 bits of the original virtual address) form the final result: physical address (LeafPTE.PPN×4096+VA[11: 0])


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

- Understand RISC-V privilege and hardware isolation methods
- Understand the basic characteristics of RISC-V's M-Mode and S-Mode
- Understand how OS accesses and controls the computer in M-Mode and S-Mode
- Learn how different softwares switch between M-Mode<–>S-Mode<–>U-Mode
