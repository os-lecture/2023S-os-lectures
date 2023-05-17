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
## Section 3. OS with device support(DOS)
<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

<br>
<br>

Spring 2023

---
### Content
- **Evolution Goals**
- Historical background
- Related hardware
- Overall idea
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

### Evolution Goals vs. Previous Goals
- SMOS: Support synchronization and mutual exclusion of shared resources in multithreading.
- TCOS: support threads and coroutines
- IPC OS: Interprocess Communication.
- Filesystem OS: support for persistent data storage
- Process OS: Enhanced process management and resource management
- Address Space OS: Isolate the memory address space accessed by APP
- Multiprog & time-sharing OS: Sharing of CPU resources among applications
- BatchOS: isolate APP from OS, strengthen system security, and improve execution efficiency
- LibOS: Isolate APP from HW, simplifying the difficulty and complexity of accessing hardware

---
<style scoped>
{
  font-size: 30px
}
</style>
### Evolution Goals vs. Previous Goals
DOS needs to support efficient access to various peripherals:
- Responding to peripheral interrupts in the kernel.
- Ensuring mutual exclusion access to global variables in the kernel.
- Serial port driver based on interrupt mechanism.
- Virtio-Block driver based on interrupt mechanism.
- Other peripheral drivers.
![bg right:40% 100%](figs/device-os-detail.png)
---
<style scoped>
{
  font-size: 28px
}
</style>
### Practice: DOS
### Your goal:
- Understand the interaction between devices and the CPU.
- Understand how to respond to interrupts in the kernel.
- Understand the basic management process of drivers.
- Understand the basic design principles of drivers.
- Understand the interaction between drivers and other parts of the kernel.
- Be able to develop an OS that supports various peripherals.
![bg right:40% 100%](figs/juravenator.png)
<!-- The genus name of Juravenator (Juravenator) comes from "Jura" (meaning "Jurassic") and "Venator" (meaning "hunter") in Latin, meaning "Jurassic" hunter". -->

---
### Content
- Evolution Goals
- **Historical background**
- Related hardware
- Overall idea
- Practical steps
- Software Architecture
- Programming
![bg right:50% 100%](figs/device-os-detail.png)

---
### Historical Background
The birth of UNIX began with a disk driver program.

Ken Thompson, at Bell Labs, wrote a disk driver program with a disk scheduling algorithm for an idle PDP-7 computer's disk drive, aiming to improve disk I/O read/write speed. To test the disk access performance, he spent three weeks developing an operating system, and that's how Unix was born.
![bg right:30% 80%](figs/ken.png)

---
<style scoped>
{
  font-size: 29px
}
</style>
### Historical Background
Writing a disk driver program involves the following operations:

1. Data structures: Including device information, status, operation flags, etc.
2. Initialization: Configuring the device, allocating memory for I/O, completing device initialization.
3. Interrupt response: If the device generates an interrupt, responding to the interrupt and completing post-I/O operation tasks.
4. Device operations: Sending commands to I/O devices based on the requirements of kernel modules (e.g., file systems) such as reading/writing disk data.
5. Internal interaction: Interacting with higher-level modules or applications in the operating system, fulfilling the requests of upper-level modules or applications (e.g., accepting I/O requests issued by the file system, uploading read disk data).

---
### Content
- Evolution Goals
- Historical background
- **Related hardware**
- Overall idea
- Practical steps
- Software Architecture
- Programming
---
### Related hardware
- PLIC (Platform-Level Interrupt Controller)
  - Handles various peripheral interrupts.
- CLINT (Core Local Interruptor)
  - Software Interrupts
  - Timer Interrupts
![bg right:55% 100%](figs/plic-clint-riscv.png)
---
### Related Hardware - Peripheral Devices in the System
```
$ qemu-system-riscv64 -machine virt -machine dumpdtb=riscv64-virt.dtb -bios default
   qemu-system-riscv64: info: dtb dumped to riscv64-virt.dtb. Exiting.
$ dtc -I dtb -O dts -o riscv64-virt.dts riscv64-virt.dtb
$ less riscv64-virt.dts
```
PLIC Device
```
                plic@c000000 {
                        phandle = <0x03>;
                        riscv,ndev = <0x35>;
                        reg = <0x00 0xc000000 0x00 0x600000>;
                        interrupts-extended = <0x02 0x0b 0x02 0x09>;
                        interrupt-controller;
                        ...
                };
```
---
### Related Hardware - Peripheral Devices in the System
virtio-blk Block Device
```
                virtio_mmio@10008000 {
                        interrupts = <0x08>;
                        interrupt-parent = <0x03>;
                        reg = <0x00 0x10008000 0x00 0x1000>;
                        compatible = "virtio,mmio";
                };
```
---
### Related Hardware - Peripheral Devices in the System
UART Serial Device
```
                uart@10000000 {
                        interrupts = <0x0a>;
                        interrupt-parent = <0x03>;
                        clock-frequency = <0x384000>;
                        reg = <0x00 0x10000000 0x00 0x100>;
                        compatible = "ns16550a";
                };
```
---
### Related Hardware - Peripheral Devices in the System
virtio-input Keyboard Device
```
                virtio_mmio@10005000 {
                        interrupts = <0x05>;
                        interrupt-parent = <0x03>;
                        reg = <0x00 0x10005000 0x00 0x1000>;
                        compatible = "virtio,mmio";
                };
```
---
### Related Hardware - Peripheral Devices in the System
virtio-input Mouse Device
```
                virtio_mmio@10006000 {
                        interrupts = <0x06>;
                        interrupt-parent = <0x03>;
                        reg = <0x00 0x10006000 0x00 0x1000>;
                        compatible = "virtio,mmio";
                };
```
---
### Related Hardware - Peripheral Devices in the System
virtio-gpu Display Device
```
                virtio_mmio@10007000 {
                        interrupts = <0x07>;
                        interrupt-parent = <0x03>;
                        reg = <0x00 0x10007000 0x00 0x1000>;
                        compatible = "virtio,mmio";
                };
```         

---
### Related Hardware - PLIC
**PLIC interrupt source**
The PLIC supports multiple interrupt sources, and each source can have different trigger types, such as level-triggered or edge-triggered. The PLIC assigns a unique ID to each interrupt source.
![bg right:55% 100%](figs/plicarch.jpg)


---
### Related Hardware - PLIC
PLIC Interrupt Handling Process
![bg right:55% 100%](figs/plicintrflow.jpg)

---
### Related Hardware - PLIC
- Gateway and IP Register(Interrupt Source Pending Register)
- Number (ID)
- Priority
- Enable
![bg right:45% 100%](figs/plicarch.jpg)

---
### Related Hardware - PLIC
**PLIC interrupt source**
- The gateways convert different types of external interrupts into a unified internal interrupt request.
- The gateways ensure that only one interrupt request is sent. Once the interrupt request is sent through the gateways, the hardware automatically sets the corresponding IP register high.
- After a gateway sends an interrupt request, it activates masking. If this interrupt is not processed, subsequent interrupts will be masked by the gateway.

---
<style scoped>
{
  font-size: 30px
}
</style>

### Practice: DOS
<!-- https://blog.csdn.net/weixin_40604731/article/details/109279426 2020.10.25 RISC-V --PLIC platform-level interrupt controller -->
**PLIC interrupt source**
- PLIC assigns an ID number to each interrupt source. ID number 0 is reserved as a representation of "non-existent interrupt," so valid interrupt IDs start from 1.
- The priority register of each interrupt source should be a readable and writable register mapped to the memory address, allowing software to program different priorities.
- PLIC supports multiple priorities, where higher priority numbers indicate higher priority levels.
- Priority 0 signifies "impossible interrupt" and effectively masks the interrupt source.

---

### Practice: DOS
<!-- https://blog.csdn.net/weixin_40604731/article/details/109279426 2020.10.25 RISC-V --PLIC platform-level interrupt controller -->
**PLIC interrupt source**
Each interrupt target in the PLIC is assigned an Interrupt Enable (IE) register, which is a readable and writable register, allowing software programming.
- If the IE register is configured as 0, it means that the corresponding interrupt source for this interrupt target is masked.
- If the IE register is configured as 1, it means that the corresponding interrupt source for this interrupt target is enabled.

---
### Content
- Evolution Goals
- Historical background
- Related hardware
- **Overall idea**
  - **Peripheral Interrupt**
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

### Practice: DOS -- **Overall idea**
  
- Why support peripheral interrupt
    - To improve the overall system performance.
- Why respond to peripheral interrupts in kernel mode
    - To enhance the OS's responsiveness to peripheral I/O requests.
- Potential problems
   - After the kernel responds to an interrupt, mutual exclusion of global variables cannot be guaranteed.
   - Reason: Interrupts interrupt the current execution and switch to another control flow to access global variables.
- Solution
   - Mask interrupts before accessing global variables and enable them after finishing.

---
### Content
- Evolution Goals
- Historical background
- Related hardware
- Overall idea
- **Practical steps**
- Software Architecture
- Programming
![bg right:50% 100%](figs/device-os-detail.png)

---
### Practice: DOS -- **Practical Steps**
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch9
```
The application program remains unchanged, but the input/output operations for serial port and block device reading/writing are implemented using interrupt-based methods.

---
### Content
- Evolution Goals
- Historical background
- Related hardware
- Overall idea
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

Device-related (Provided):

1. Device initialization operations
2. Device interrupt handling operations
3. Device I/O read/write (or configuration) operations

OS Interaction-related (Requirements):

1. Memory allocation/mapping services
2. Interrupt/scheduling/synchronization/file system services

---
### System Device Management
- Understand the basic information of each device
  - Control register address range
  - Device interrupt number
- Configure the PLIC
  - Enable interrupts
  - Set interrupt priorities

---
### System Device Management
- Understand the basic information of each device(control register base address, interrupt number)
```
const VIRT_PLIC: usize = 0xC00_0000;   // PLIC 
const VIRT_UART: usize = 0x1000_0000;  // UART
const VIRTIO0: usize = 0x10008000;     // VIRTIO_BLOCK
const VIRTIO5: usize = 0x10005000;     // VIRTIO_KEYBOARD
const VIRTIO6: usize = 0x10006000;     // VIRTIO_MOUSE
const VIRTIO7: usize = 0x10007000;     // VIRTIO_GPU
// Respond to different device interrupts in the main interrupt handling routine
match intr_src_id {
   5 => KEYBOARD_DEVICE.handle_irq(),
   6 => MOUSE_DEVICE.handle_irq(),
   8 => BLOCK_DEVICE.handle_irq(),
   10 => UART.handle_irq(),
```

---
<style scoped>
{
  font-size: 32px
}
</style>
### System Device Management
Initialization of System Device Management:

- Configure PLIC:
  - Set the lower limit for interrupt priority reception
  - Enable the response to peripheral interrupts (5/6/8/10) in S-Mode
  - Set the priority of peripheral interrupt numbers
- Configure CPU:
  - Set the `sie` CSR register to enable response to external interrupts
Located in `os/src/drivers/plic.rs` and `os/src/boards/qemu.rs::devices_init()`

---
### UART Device Driver
Core data structures for the UART device driver:
```
pub struct NS16550a<const BASE_ADDR: usize> {
    inner: UPIntrFreeCell<NS16550aInner>,
    condvar: Condvar, // Used for suspending/resuming the read character routine
}
struct NS16550aInner {
    ns16550a: NS16550aRaw,
    read_buffer: VecDeque<u8>, // Used for caching read characters
}
pub struct NS16550aRaw {
    base_addr: usize, // Control register base address
}
```

---
### UART Device Driver
1. UART initialization operation
2. UART interrupt handling operation
3. UART I/O read/write (or configuration) operation
- Interface to be implemented by character devices:
```
pub trait CharDevice {
    fn init(&self);
    fn read(&self) -> u8;
    fn write(&self, ch: u8);
    fn handle_irq(&self);
}
```
---
### UART Device Driver
1. UART initialization operation
```
impl<const BASE_ADDR: usize> CharDevice for NS16550a<BASE_ADDR> {
    fn init(&self) {
        let mut inner = self.inner.exclusive_access(); // Exclusive access
        inner.ns16550a.init(); // Call the UART initialization function of ns16550a
        drop(inner);
    }

```

---
### UART Device Driver
2. UART interrupt handling operation
```
fn handle_irq(&self) {
   let mut count = 0;
   self.inner.exclusive_session(|inner| {
      // Call the read character function of ns16550a
      while let Some(ch) = inner.ns16550a.read() { 
            count += 1;
            inner.read_buffer.push_back(ch);
   ...
   if count > 0 {
      // Wake up the process waiting to read characters
      self.condvar.signal();
   ...
```
---
### UART Device Driver
3. UART I/O read/write (or configuration) operation
```
fn read(&self) -> u8 {
   loop {
      let mut inner = self.inner.exclusive_access();
      if let Some(ch) = inner.read_buffer.pop_front() {
            return ch;
      } else {
            let task_cx_ptr = self.condvar.wait_no_sched();
            drop(inner);
            schedule(task_cx_ptr);
    ...
```

---
### UART Device Driver
3. UART I/O read/write (or configuration) operation
```
    fn write(&self, ch: u8) {
        let mut inner = self.inner.exclusive_access();
        inner.ns16550a.write(ch);
    }
```

---
### virtio_blk Block Device Driver
Core data structure for the virtio_blk device driver:
```
pub struct VirtIOBlock {
    virtio_blk: UPIntrFreeCell<VirtIOBlk<'static, VirtioHal>>,
    condvars: BTreeMap<u16, Condvar>, // Mapping of <virtual queue number, condition variable>
}
```

---
### virtio_blk Block Device Driver
1. virtio_blk initialization operation
2. virtio_blk interrupt handling operation
3. virtio_blk I/O read/write (or configuration) operation
- Interface to be implemented by storage devices:
```
pub trait BlockDevice: Send + Sync + Any {
    fn read_block(&self, block_id: usize, buf: &mut [u8]);
    fn write_block(&self, block_id: usize, buf: &[u8]);
    fn handle_irq(&self);
}
```

---
### virtio_blk Block Device Driver
1. virtio_blk initialization operation
```
pub fn new() -> Self {
   let virtio_blk = unsafe {
      UPIntrFreeCell::new(
            // Initialize the VirtIOBlk block device in vritio_drivers
            VirtIOBlk::<VirtioHal>::new(&mut *(VIRTIO0 as *mut VirtIOHeader)).unwrap(),)
   let mut condvars = BTreeMap::new();
   let channels = virtio_blk.exclusive_access().virt_queue_size();
   // Establish a mapping of virtual queue numbers to condition variables
   for i in 0..channels {  
      let condvar = Condvar::new();
      condvars.insert(i, condvar);
   }
   ...
```
---
### virtio_blk Block Device Driver
2. virtio_blk interrupt handling operation
```
fn handle_irq(&self) {
   self.virtio_blk.exclusive_session(|blk| {
      // Get the virtual queue number for completed block accesses
      while let Ok(token) = blk.pop_used() {
            // Wake up the suspended processes waiting for block accesses to finish based on the queue number
            self.condvars.get(&token).unwrap().signal();
      }
   ...
```
---
### virtio_blk Block Device Driver
3. virtio_blk I/O read/write (or configuration) operation
```
fn read_block(&self, block_id: usize, buf: &mut [u8]) {
   ...
      let mut resp = BlkResp::default(); // Generate a block access command
      let task_cx_ptr = self.virtio_blk.exclusive_session(|blk| {
            // Call the read_block_nb function in the VirtIOBlk of the virtio_drivers library to send the read block command
            let token = unsafe { blk.read_block_nb(block_id, buf, &mut resp).unwrap() };
            // Suspend the current process using the condition variable and wait for the block access to finish
            self.condvars.get(&token).unwrap().wait_no_sched()
      });
      // Wake up the processes waiting for block access to finish
      schedule(task_cx_ptr);
   ...
}
```

---
### virtio_blk Block Device Driver
3. virtio_blk I/O read/write (or configuration) operation
```
fn write_block(&self, block_id: usize, buf: &[u8]) {
   ...
      let mut resp = BlkResp::default(); // Generate a block access command
      let task_cx_ptr = self.virtio_blk.exclusive_session(|blk| {
            // Call the write_block_nb function in the VirtIOBlk of the virtio_drivers library to send the write block command
            let token = unsafe { blk.write_block_nb(block_id, buf, &mut resp).unwrap() };
            // Suspend the current process using the condition variable and wait for the block access to finish
            self.condvars.get(&token).unwrap().wait_no_sched()
      });
      // Wake up the processes waiting for block access to finish
      schedule(task_cx_ptr);
   ...
```