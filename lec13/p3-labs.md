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

# Lecture 13 Device Management
## The third section supports device OS (DOS)
---
### Practice: DOS
- **Evolution Goal**
- History background
- Related hardware
- General idea
- Practical steps
- Software Architecture
- Programming

![bg right:50% 100%](figs/device-os-detail.png)


---
<style scoped>
{
  font-size: 28px
}
</style>

### Practice: DOS -- past goals
- SMOS: Supports synchronized mutex access to shared resources in multithreading
- TCOS: support threads and coroutines
- IPC OS: interprocess interaction
- Filesystem OS: support for persistent data storage
- Process OS: Enhanced process management and resource management
- Address Space OS: Isolate the memory address space accessed by APP
- Multiprog & time-sharing OS: Let APP share CPU resources
- BatchOS: isolate APP from OS, strengthen system security, and improve execution efficiency
- LibOS: Isolate APP from HW, simplifying the difficulty and complexity of applications accessing hardware

---
### Practice: DOS -- Evolution goals
Supports efficient access to a wide variety of peripherals
- Support for responding to peripheral interrupts in the kernel
- Support for guaranteed exclusive access to global variables in the kernel
- Serial device driver based on interrupt mechanism
- Virtio-Block device driver based on interrupt mechanism

---
### Practice: DOS
### Classmate's evolutionary goal
- Understand how to respond to interrupts in the kernel
- Understand the basic management process of peripheral drivers
- Can write an OS that supports a variety of peripherals
![bg right:40% 100%](figs/juravenator.png)
<!-- The genus name of Juravenator (Juravenator) comes from "Jura" (meaning "Jurassic") and "Venator" (meaning "hunter") in Latin, meaning "Jurassic" hunter". -->


---
### Practice: DOS
- Evolution goals
- History background
- **Related hardware**
- General idea
- Practical steps
- Software Architecture
- Programming

![bg right:55% 100%](figs/plic-clint-riscv.png)

---
### Practice: DOS
<!-- https://blog.csdn.net/weixin_40604731/article/details/109279426 2020.10.25 RISC-V --PLIC platform-level interrupt controller -->
**PLIC interrupt source**
PLIC supports multiple interrupt sources, each interrupt source can be a different trigger type, level trigger or edge trigger, PLIC assigns each interrupt source
- Gateway and IP
- Number (ID)
- Priority
- Enable


---
### Practice: DOS
<!-- https://blog.csdn.net/weixin_40604731/article/details/109279426 2020.10.25 RISC-V --PLIC platform-level interrupt controller -->
**PLIC interrupt source**
- Gate (Gateway) converts different types of external interrupts into unified internal interrupt requests
- The gate guarantees that only one interrupt request is sent. After the interrupt request is sent through the gate, the hardware automatically sets the corresponding IP register to high
- After the gate sends an interrupt request, it starts masking. If the interrupt is not processed, subsequent interrupts will be masked by the gate

---
<style scoped>
{
  font-size: 30px
}
</style>

### Practice: DOS
<!-- https://blog.csdn.net/weixin_40604731/article/details/109279426 2020.10.25 RISC-V --PLIC platform-level interrupt controller -->
**PLIC interrupt source**
- The PLIC assigns a number (ID) to each interrupt source. ID number 0 is reserved as a "non-existent interrupt", so valid interrupt IDs start from 1
- The priority register of each interrupt source should be a readable and writable register of memory address mapping, so that software can program and configure different priorities
- PLIC supports multiple priorities, the larger the priority number, the higher the priority
- Priority 0 means "interrupt impossible", which is equivalent to interrupt source masking

---

### Practice: DOS
<!-- https://blog.csdn.net/weixin_40604731/article/details/109279426 2020.10.25 RISC-V --PLIC platform-level interrupt controller -->
**PLIC interrupt source**
The interrupt source of each interrupt target is assigned an interrupt enable (IE) register, and the IE register is a readable and writable register, allowing software to program it
- If the IE register is configured as 0, it means that the corresponding interrupt target of this interrupt source is masked
- If the IE register is configured as 1, it means that the corresponding interrupt target of this interrupt source is enabled
---
### Practice: DOS
- Evolution goals
- History background
- Related hardware
- **General idea**
     - **Peripheral Interrupt**
- Practical steps
- Software Architecture
- Programming
![bg right:55% 100%](figs/device-os-detail.png)

---
<style scoped>
{
  font-size: 28px
}
</style>

### Practice: DOS -- **General idea**
  
- Why support peripheral interrupt
    - Improve the overall execution efficiency of the system
- Why respond to peripheral interrupts in kernel mode
    - Improve OS response speed to peripheral IO requests
- Potential problems
   - After the kernel mode can respond to interrupts, mutual exclusive access to global variables cannot be guaranteed
   - Reason: interrupt will interrupt the current execution and switch to another control flow to access global variables
- Solution
   - Mask interrupts before the access to global variables starts, and enable interrupts after the end

---
### Practice: DOS
- Evolution goals
- History background
- Related hardware
- **Practical Steps**
- Software Architecture
- Programming
![bg right:55% 100%](figs/device-os-detail.png)

---
### Practice: DOS -- **Practice Steps**
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch9
```
The application program has not changed, but the IO operations of serial port input and output, block device read and write are implemented based on interrupt mode.

---
### Practice: DOS
- Evolution goals
- History background
- Related hardware
- Practical steps
- **Software Architecture**
- Programming
![bg right:55% 100%](figs/device-os-detail.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### Practice: DOS -- **Software Architecture**
Major changes to the kernel
```
os/src/
├── boards
│ └── qemu.rs // MMIO address of UART, VIRTIO, PLIC
├── console.rs //UART-based STDIO
├── drivers
│ ├── block
│ │ └── virtio_blk.rs //VIRTIO-BLK driver based on interrupt/DMA
│ ├── chardev
│ │ └── ns16550a.rs //Interrupt-based serial driver
│ └── plic.rs //PLIC driver
├── main.rs //Peripheral interrupt related initialization
└── trap
     ├── mod.rs //Support processing peripheral interrupts
     └── trap.S //Support kernel state response to peripheral interrupt
```

---
### Practice: DOS
- Evolution goals
- History background
- Related hardware
- Practical steps
- Software Architecture
- **Programming**
![bg right:55% 100%](figs/device-os-detail.png)

---
###  Programming

1. Peripheral initialization
2. Peripheral interrupt processing
3. Peripheral I/O read and write operations


---
###  Programming
- Peripheral initialization
    - PLIC initialization
    - Serial device initialization
    - virtio-blk device initialization


---
###  Programming
- Peripheral interrupt handling

---
###  Programming
- Peripheral I/O read and write operations
    - Serial device read and write
    - virtio-blk device read and write