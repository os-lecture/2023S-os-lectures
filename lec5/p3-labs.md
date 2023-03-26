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

# Lecture 5 Physical Memory Management
## Section 3 Practice: Building Address Space OS (ASOS)
<br>


Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

Spring 2023

---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

### 1. Lab Objectives and Steps
- Lab Objectives
- Steps
2. System Architecture
3. Address Space from User Perspective
4. Kernel-managed Address Space
5. Implementing ASOS

![bg right:50% 100%](figs/addr-space-os-detail.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Previous Objectives
Improve performance, simplify development, enhance security
- Multiprog & time-sharing OS objective
   - Lets APP effectively share the CPU to improve the overall performance and efficiency of the system
- BatchOS objective
   - Isolates APP from OS to strengthen system security and improve execution efficiency
- LibOS objective
   - Isolates APP from hardware to simplify the difficulty and complexity of the application accessing the hardware
---
#### Lab Objectives

~~Improve performance~~, simplify development, enhance security
- Simplify programming, APP does not need to consider the initial execution address of its runtime
   - Reach a consensus with the compiler to set a fixed start execution address for each APP
- **Isolate the memory address space accessed by APP**
   - Set the memory space boundary for each APP to make it cannot access the OS and other APPs beyond the boundary

---

#### Lab Requirements
- Understand address space
- Master paging mechanism
- Know how to handle page access exceptions
- Can write an OS that supports paging mechanism

<!-- Ankylosauridae Late Cretaceous -->

![bg right 80%](figs/ankylosauridae.png)



---

#### General Idea

![bg 56%](figs/addr-space-os-detail.png)

---

#### General Idea
- Compilation: The application program and the kernel are compiled independently and merged into one image
- Compilation: different applications can use **uniform start address**
- Structure: system call service, task management and initialization
- Construction: Establish a virtual memory space based on **page table mechanism**
- Run: privilege level switching, task and OS switching
- Run: **switch address space**, access data across address spaces


---

#### History Background

- In 1940 **two-level storage** system appeared
   - Main memory: Magnetic core; Secondary: Magnetic drum
- Proposed **Virtual memory** technology concept
   - Fritz-Rudolf Güntsch, Ph.D. student, TU Berlin, Germany
- Atlas Supervisor OS in 1959
   - Professor Tom Kilburn's team at the University of Manchester, UK, demonstrating the Atlas computer and the Atlas Supervisor operating system
   - **Proposed** paging technology and virtual memory technology (called one-level storage system at that time)
---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

1. Lab Objectives and Steps
- Lab Objectives
- ### Steps
2. System Architecture
3. Address Space from User Perspective
4. Kernel-managed Address Space
5. Implementing ASOS

![bg right:50% 100%](figs/addr-space-os-detail.png)

---

#### Lab Steps
- Modify the APP link script (custom start address)
- Load & execute application
- Switch tasks and **task's address space**


---
#### Compilation
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch4
cd os
make run
```

---

#### Output

```
Into Test load_fault, we will insert an invalid load operation...  
Kernel should kill this application!
[kernel] PageFault in application, bad addr = 0x0, bad instruction = 0x1009c, kernel killed it.

store_fault APP running...

Into Test store_fault, we will insert an invalid store operation...  
Kernel should kill this application!
[kernel] PageFault in application, bad addr = 0x0, bad instruction = 0x1009c, kernel killed it.
power_3 [130000/300000]
```

---
#### Test Applications

It contains two applications `04load_fault`, `05store_fault`
```
//usr/src/bin/04load_fault.rs
 …
     unsafe {
         let _i=read_volatile(null_mut::<u8>());
     }

//usr/src/bin/05store_fault.rs
 …
     unsafe {
        null_mut::<u8>().write_volatile(1);
     }
```

---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

1. Lab Objectives and Steps
### 2. System Architecture
- Code Structure
- RISC-V SV39 Paging Mechanism
3. Address Space from User Perspective
4. Kernel-managed Address Space
5. Implementing ASOS

![bg right:50% 100%](figs/addr-space-os-detail.png)

---

#### Software Architecture
- Simplified application
- Create Paging
- Kernel page tables
- Application page table
- Information transfer
- Trampoline mechanism
- Extended TCBs
- Extended exceptions
  
![bg right:50% 100%](figs/addr-space-os-detail.png)

---

#### Building the application
```
└── user
     ├── build.py (removed: the script that sets the unique starting address for the application)
     └── src (user library and application)
         ├── bin (each application)
         ├──...
         └── linker.ld (modified: place all applications in fixed locations in their respective address spaces)
```


---

#### Address space
```
├── os
     └── src
   ├── config.rs (modified: add some memory management related configuration)
   ├── linker-k210.ld (modified: introduce trampoline page into memory layout)
     ├── linker-qemu.ld (modified: introduce the trampoline page into the memory layout)
   ├── loader.rs (modified: only keep the function of obtaining the number of applications and application data)
   ├── main.rs (modified)
```

---

#### MM (Memory Management) submodule
```
├── os
     └── src
   ├── mm (new: mm submodule of memory management)
   ├──address.rs (Rust abstraction of physical/virtual addresses/page numbers)
   ├──frame_allocator.rs (physical page frame allocator)
   ├──heap_allocator.rs (kernel dynamic memory allocator)
   ├──memory_set.rs (introduce address space MemorySet and logical segment MemoryArea, etc.)
   ├──mod.rs (defining the mm module initialization method init)
           └──page_table.rs (multi-level page table abstract PageTable and other content)
```

---

#### Improve OS
```
├── os
     └── src
   ├── syscall
   ├──fs.rs (modified: sys_write implementation based on address space)
   ├── task
   ├──context.rs (modified: Construct an initial task context to jump to different locations)
   ├──mod.rs (modified)
           └──task.rs (modified)
          └── trap
              ├── context.rs (modified: added more content in Trap context)
              ├── mod.rs (modified: modify the trap mechanism based on the address space)
              └── trap.S (modified: modify the assembly codes of saving and restoring trap context based on address space)
```


---
<style scoped>
{
  font-size: 28px
}
</style>

**Outline**


1. Lab Objectives and Steps
2. System Architecture
- Code Structure
- ### RISC-V SV39 Paging Mechanism
3. Address Space from User Perspective
4. Kernel-managed Address Space
5. Implementing ASOS

![bg right:50% 100%](figs/addr-space-os-detail.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### RISC-V SATP-based virtual memory system

- Virtual addresses divide memory into fixed-size pages for address translation and content protection.
- Page table base address register satp: A CSR in the kernel mode controls paging. It has three fields:
   - MODE field: enable paging and select page table levels
   - ASID (Address Space Identifier) field: optional, used to reduce the overhead of context switching
   - PPN field: saves the physical address of the root page table
![w:800](figs/satp.png)



---
#### Page table base address register satp
- satp CSR: (S-Mode)
   Supervisor Address Translation and Protection
- Control hardware paging mechanism

![bg right:50% 100%](figs/sv39-full.png)

---
<style scoped>
{
  font-size: 32px
}
</style>

#### Initialize & enable page mechanism

- M-mode RustSBI writes 0 to satp before first entering S-Mode to disable paging
- Then the OS in S-Mode will write satp again after initializing the page table
   - Enable page table: `MODE`=8
   - Set the page number of the starting physical address of the page table `PPN`

![bg right:50% 90%](figs/sv39-full.png)

---
<style scoped>
{
  font-size: 32px
}
</style>

#### Item Attributes

- V: valid bit
- R, W, X: read/write/execute bits
- U: Whether U-Mode can be accessed
- G: Whether it is valid for all addresses
- A: Accessed bit
- D: Modified bit
- RSW: reserved bit
- PPN: Physical Page Number

![bg right:50% 100%](figs/sv39-full.png)

---
<style scoped>
{
  font-size: 28px
}
</style>

**Outline**

1. Lab Objectives and Steps
2. System Architecture
### 3. Address Space from User Perspective
* ASOS Address Space
* Trampoline Page
* Application Address Space 
4. Kernel-managed Address Space
5. Implementing ASOS


![bg right:50% 100%](figs/addr-space-os-detail.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Application address space
- **Address space**: a series of logical segments that are **related** and **not necessarily contiguous**
- The virtual/physical memory space consists of several logical segments bounded to a running program (currently a running program is called **task** and **process**)
- The memory space where a process can direct access is limited by the associated address space of its code and data
![bg right:50% 100%](figs/seg-addr-space.png)

---
<style scoped>
{
  font-size: 26px
}
</style>

#### Application address space and kernel address space
- Application address space
    - The address space generated by the compiler for the application, the kernel constrains the application address space through the page table, and the application cannot access the address space outside it
- Kernel address space
    - The address space generated by the compiler for the kernel, the kernel adjusts the application/kernel address space through the page table, and manages the entire physical memory
![bg right:40% 100%](figs/addr-space-os-detail.png)


---
<style scoped>
{
  font-size: 28px
}
</style>

**Outline**

1. Lab Objectives and Steps
2. System Architecture
3. Address Space from User Perspective
* ASOS Address Space
* ### Trampoline Page
* Application Address Space 
4. Kernel-managed Address Space
5. Implementing ASOS

![bg right:50% 100%](figs/addr-space-os-detail.png)

---
#### trampoline page
- The virtual address of the trampoline page of the application and the kernel is the same and mapped to the same physical page
- The content placed in it is the execution codes of ``trap.S``
<!-- - But user mode cannot access this memory area
- When an exception/interrupt occurs, it will jump to the ``_all_traps`` entry of the trampoline page
- and after switching page tables, continue execution smoothly -->

![bg right:55% 100%](figs/trampoline.png)


---

#### Smooth transition based on trampoline page

- **Privilege level transition**: When an exception/interrupt occurs, the CPU will jump to the ``_all_traps`` entry on the trampoline page
- **Address Space Transition**: After switching page tables, the execution of kernel code can continue smoothly

![bg right:50% 100%](figs/trampoline.png)


---

#### Trap Context 
- The `_all_traps` assembly function of the trampoline page will **save** the relevant registers to the trapping context
- The `_restore` assembly function of the trampoline page will **restore** the relevant registers from the trapped context


![bg right:50% 100%](figs/trampoline.png)


---

#### Review: OS Without Paging
The trapping context is saved on the top of the kernel stack, ``sscratch`` saves the application's kernel stack
- Only transfer **user/kernel stack pointer** through the ``sscratch`` register
- When an application traps to the kernel, sscratch has already pointed to the top of the application's kernel stack, and a command can be used to switch from the user stack to the kernel stack, and then directly push the trap context to the top of the kernel stack.

<!--
![bg right:50% 100%](figs/trampoline.png)
-->

---
#### Compare to the OS with Paging

How to transfer **stack pointer** and **page table base address** only through the `sscratch` register?
- Can we use the previous method?
- Option 1: transfer **user/kernel stack pointers** through the `sscratch` register
- Scheme 2: transfer **user stack pointer/page table base address** through the `sscratch` register

![bg right:30% 100%](figs/kernel-stack.png)


---
#### Solution 1: transfer the user/kernel stack pointers through `sscratch` register
- Transfer **user/kernel stack pointer** through ``sscratch`` register
    - Currently, the sp pointer points to the kernel address space
    - While the user mode page table is still being used at this time
- It causes an exception in the kernel mode, leading to the **system crash**

![bg right:30% 100%](figs/kernel-stack.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Solution 2: Transfer the user stack pointer/page table base address through `sscratch` register
- Transfer **user stack pointer/page table base address** through `sscratch` register
- Currently using kernel mode page table to access kernel address space
- Next, we need to obtain the application's kernel stack pointer to save the current user mode general registers to the trap context
- Modifying (**destroy**) general registers is required to obtain the kernel stack pointer, making it impossible to **save them correctly**

![bg right:25% 100%](figs/kernel-stack.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Solution 3: ``sscratch`` - Application's trap context address
- The user mode stack pointer <-> trap context address switch is performed through ``sscratch``
- Save user mode registers to the trap context
- Read the page table base address/application's kernel stack pointer/**trap_handler** address from the trap context
- Switch page tables and jump to **trap_handler**

![bg right:42% 100%](figs/trampoline.png)



---
<style scoped>
{
  font-size: 28px
}
</style>

**Outline**

1. Lab Objectives and Steps
2. System Architecture
3. Address Space from User Perspective
* ASOS Address Space
* Trampoline Page
* ### Application Address Space 
4. Kernel-managed Address Space
5. Implementing ASOS

![bg right:50% 100%](figs/addr-space-os-detail.png)

---
<style scoped>
{
  font-size: 32px
}
</style>
#### App Design

- App
   - **Memory layout** has been adjusted
- No update
   - Project structure
   - Application code
   - Function call
   - System call

![bg right:60% 100%](figs/app-as-full.png)

---
<style scoped>
{
  font-size: 32px
}
</style>
#### Memory Layout of App
* Since each application is loaded at the same location, they share the same link script linker.ld
   * **`BASE_ADDRESS`** = 0x10000

![bg right:60% 100%](figs/app-as-full.png)


---
<style scoped>
{
  font-size: 28px
}
</style>

**Outline**

1. Lab Objectives and Steps
2. System Architecture
3. Address Space from User Perspective
### 4. Kernel-managed Address Space
* Manage Physical Memory
* Build Kernel/Application Page Tables
* Manage Address Space
5. Implementing ASOS


![bg right:50% 100%](figs/addr-space-os-detail.png)

---
#### Address Space from the Perspective of the Kernel
- Understanding Address Space
- Understanding trap context 


![bg right:55% 100%](figs/addr-space-os-detail.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
 
#### Address Space from the Perspective of the Kernel

- **address space from kernel's perspective**
   - Create & sense virtual/physical addresses
   - Traverse between kernel/application virtual address spaces
- Application page table
   - Represents the real address space of an application managed by the kernel
   - The address space that the CPU can access when running the application

![bg right:40% 100%](figs/trampoline.png)

---

#### Page Table Mechanism
- **Manage Physical Memory**
- Build Kernel/Application Page Tables
- Enable Paging Mechanism

![bg right:62% 100%](figs/page-overview.png)



---
#### Physical Memory

- Physical memory (RAM is set to 8MB)
   - Starting address of Physical memory: ``0x80000000``
   - Starting address of available physical memory: ``ekernel`` address in ``os/src/linker.ld``
   - End address of physical memory: ``0x80800000``
- What is in physical memory?


---
#### Physical Memory

- Physical memory (RAM is set to 8MB), including:
   - data/code/stack/heap of Application/Kernel  
   - free space
- Especially various management data
   - Task Control Block (TCB)
      - MemorySet
          - Application/kernel multi-level page tables, etc.
      - Application core stack
      - Application's TrapContext page ....

---

#### Manage Physical Memory

- A **portion** of the physical memory is already reserved for storing the code and data of the kernel
- The **remaining free memory** needs to be managed in units of a single physical page frame
   - **Allocate** free physical page frames when there is a need to store application data or to expand the multilevel page table
   - **Reclaim** all physical page frames occupied by the application when it crashes or exits.


---
#### Manage Physical Memory
- **Dynamic Allocation Strategy** with contiguous memory
- Interface for **allocating/reclaiming physical page frames**
   - Provide ``alloc()`` and ``dealloc()`` function interfaces

![bg right:49% 100%](figs/addr-space-os-detail.png)

---
<style scoped>
{
  font-size: 26px
}
</style>

**Outline**

1. Experimental objectives and steps
2. System Architecture
3. Address space from user perspective
4. The kernel manages the address space
* Manage Physical Memory
* ### Build Kernel/Application Page Tables
* Manage Address Space
5. Implement ASOS

![bg right:50% 100%](figs/addr-space-os-detail.png)

---

#### SV39 multi-level page table

- managed in units of page-sized nodes. 
- Each node is stored in exactly one physical page frame
- ues a  physical page number to represent its position
- satp CSR
![bg right:45% 100%](figs/sv39-full.png)


---
<style scoped>
{
  font-size: 32px
}
</style>

#### Build Kernel/Application Page Tables
- Starting physical address of the page table
- Page table content: virtual address <-> physical address mapping
   - Identity Mapping 
   - Framed Mapping

VPN: Virtual Page Number
PPN: Physical Page Number
satp: CSR containing the PPN of the staring address of page table
![bg right:40% 100%](figs/sv39-full.png)


---

#### Create and Remove Virtual and Physical Address Mapping 

* Find a **page table entry** corresponding to a virtual address in the multi-level page table.
* By **modifying page table entries** , **key-value pairs** can be inserted and deleted, thereby creating and removing **mapping relationships**.

![bg right:40% 100%](figs/sv39-full.png)

---
#### Enable Paging Mechanism

- set ``satp=root_ppn``

Containment relationship of core data structures
```
TCB-->MemorySet-->PageTable-->root_ppn
Task Control Block ------> the base address of a task's page table
```

![bg right:50% 100%](figs/kernel-as-low.png)
![bg right 100%](figs/kernel-as-high.png)


---
<style scoped>
{
  font-size: 28px
}
</style>

**Outline**

1. Experimental objectives and steps
2. System Architecture
3. Address space from user perspective
4. The kernel manages the address space
* Manage Physical Memory
* Build Kernel/Application Page Tables
* ### Manage Address Space
5. Implement ASOS

![bg right:50% 100%](figs/addr-space-os-detail.png)

---

#### Application Address Space
![w:950](figs/app-as-full.png)

---
#### Logic Segment

- Logical Segment: a segment of virtual memory with contiguous addresses used by the kernel/application
- The virtual address space where the kernel/application runs: consists of multiple logical segments

**Ideal: Plump vs. Reality: Skinny**
- Application's **page table**
   - Realistic address space of the application
- The **logical segment** of the application
   - Ideal address space of the application 

![bg right:48% 100%](figs/seg-addr-space.png)


---

#### The Data Structure of the Logical Segment ``MapArea``
- Logical Segment: a segment of virtual memory with continuous addresses
```rust
// os/src/mm/memory_set.rs

pub struct MapArea {
     vpn_range: VPNRange, // a continuous range of virtual page numbers
     data_frames: BTreeMap<VirtPageNum, FrameTracker>, //VPN<-->PPN mapping relationship
     map_type: MapType, //map type
     map_perm: MapPermission, //readable/writable/executable attribute
}
```
``data_frames`` is a key-value pair container：stores each virtual page in the logical segment and the physical page FrameTracker it is mapped to


---
<style scoped>
{
  font-size: 30px
}
</style>

#### The Data Structure of the Address Space ``MemorySet``
- **Address Space**: a series of associated logical segments that are not necessarily continuous in physical memory
```rust
// os/src/mm/memory_set.rs

pub struct MemorySet {
     page_table: PageTable, //page table
     areas: Vec<MapArea>, //A series of associated logical segments that are not necessarily continuous
}
```
* Data structure field of **address space**
   - Multi-level page table: variable ``page_table`` based on the data structure ``PageTable``
   - Collection of logical segments: vector ``areas`` based on the data structure ``MapArea``

---
#### The timing for kernel to manage task  address space
- **Address Space** managed by OS  ``MemorySet``=``PageTable``+``MapAreas``
    - **Create** tasks: create a ``MemorySet`` of the task
    - **Clear** tasks: reclaim the memory occupied by ``MemorySet`` of tasks
    - **Adjust** the memory space size of the application: Modify the ``MemorySet`` of the task
    - **Switch** from user mode to kernel mode: switch the ``MemorySet`` of the task to the ``MemorySet`` of the kernel
    - **Switch**  from kernel mode to user mode: switch the ``MemorySet`` of the kernel to the ``MemorySet`` of the task

---
#### The Process of Creating a New Task Address Space `MemorySet`
- create page table
- Create logical segment vector

![bg right:64% 100%](figs/app-as-full.png)


---

#### Insert/Delete a Logical Segment in the Address Space
- Update the corresponding page table entry in the page table
- Update the physical page frame corresponding to the logical segment
![bg right:50% 100%](figs/app-as-full.png)



---
<style scoped>
{
  font-size: 28px
}
</style>

**Outline**

 …
3. Address space from user perspective
4. The kernel manages the address space
### 5. Implement ASOS
* Start Paging Mode
* Implement trampoline
* Load and Execute the Application
* Improve the Implementation of Trap Handling
* Improve the Implementation of sys_write

![bg right:45% 100%](figs/addr-space-os-detail.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Extensions to Time-Sharing Multitask OS

1. Create **kernel page table**, enable the paging mechanism,  establish the virtual address space of the kernel;
2. Extend **Trap Context**, switch page table (i.e., switch virtual address space) during the process of saving and restoring Trap context;
3. Establish the **trampoline space** required for switching between the kernel address space and the application address space;
4. Extend  **Task Control Block** to include virtual memory-related information, and when loading and executing a task based on an application, establish the virtual address space of the application;
5. Improve the implementation of **system calls** such as trap processing and sys_write to support separating application address space and kernel address space.

---
#### Start paging mode
1. Create a kernel address space
2. Initialize the memory management (MM) submodule

![bg right:63% 100%](figs/trampoline.png)

---

#### Create a Global Instance of the Kernel Address Space
- Kernel Address Space ``KERNEL_SPACE``
```rust
pub static ref KERNEL_SPACE: MemorySet = MemorySet::new_kernel()
```


---
#### Initialization of the Memory Management Subsystem
1. Initialize the **free physical memory** according to the heap for dynamic continuous memory management
2. Implement the initialization of **physical page frame allocation** management based on the heap
3. Set **satp**, start the paging mechanism, activate the kernel address space ``KERNEL_SPACE``,

```rust
// os/src/mm/mod.rs
pub fn init() {
     heap_allocator::init_heap();
     frame_allocator::init_frame_allocator();
     KERNEL_SPACE. exclusive_access(). activate();
}
```
---
<style scoped>
{
  font-size: 29px
}
</style>

**Outline**

 …
3. Address space from user perspective
4. The kernel manages the address space
5. Implement ASOS
* Start pagination mode
### Implement trampoline 
* Load and Execute the Application
* Improve  the Implementation of Trap Handling
* Improve the Implementation of sys_write

![bg right:40% 100%](figs/addr-space-os-detail.png)

---

#### Why use trampoline?

After the paging mechanism is enabled, applications and kernels in different address spaces should be able to perform normal **privilege level switching** operations and **data interaction**.

![bg right:50% 100%](figs/trampoline.png)

---
<style scoped>
{
  font-size: 32px
}
</style>

#### How to use Trampoline?

- The **highest virtual page** in the virtual address space of the kernel and application is a trampoline page
- After the **privilege level** switch,  complete the **address space** switch, **kernel stack** switch quickly, and **smoothly continue to execute** the kernel code

![bg right:50% 100%](figs/trampoline.png)

---

#### How to use Trampoline?

- The **second highest virtual page** of the application address space is set to store the trap context of the application

![bg right:54% 100%](figs/trampoline.png)

---

#### How to use Trampoline?

- Q: Why not put the Trap context directly in the kernel stack of the application?

![bg right:54% 100%](figs/trampoline.png)


---

#### How to use Trampoline?

- Q: Why not put the Trap context directly in the kernel stack of the application?
   - Accessing the Trap context address in the kernel stack requires switching **page table**;
   - Page table information is placed in **Trap context**, forming mutual dependence.


![bg right:40% 100%](figs/trampoline.png)

---

#### Create a Trampoline Page
- Place the entire assembly code of trap.S in the .text.trampoline segment
- Align it to one page of the code segment when adjusting the memory layout
```
# os/src/linker.ld
     text = .;
         .text : {
         *(.text.entry)
         . = ALIGN(4K);
         strampoline = .;
         *(.text.trampoline);
         . = ALIGN(4K);
         *(.text .text.*)
     }
```
<!-- ---
RISC-V only provides a sscratch register which can be used for temporary turnaround

1. We must first switch to the kernel address space, which requires writing the kernel address space token into the satp register.
2. Afterwards, we also need to save the location of the top of the application's kernel stack so that we can use it as a base address to save the Trap context.
3. These two steps require using general-purpose registers as temporary storage, but we cannot accomplish this without disrupting any of them.
4. Therefore, we have to save the Trap context in a virtual page in the application address space instead of switching to the kernel address space to save it.
-->





---

#### Extend the Trap Context Data Structure
```rust
  // os/src/trap/context.rs
  pub struct TrapContext {
      pub x: [usize; 32],
      pub sstatus: Sstatus,
      pub sepc: usize,
      pub kernel_satp: usize, //The starting physical address of the kernel page table
      pub kernel_sp: usize, //The virtual address of the top of the current application kernel stack
      pub trap_handler: usize,//The virtual address of the trap handler entry point in the kernel
}
```
---

#### Switch Trap Context
* **Save Trap Context**
   - Switch **User Stack Pointer** to TrapContext in user address space
   - **Save** general registers, sstatus, sepc in TrapContext
   - **Read** out kernel_satp, kernel_sp, trap_handler in TrapContext
   - **Switch** kernel address space, switch to kernel stack
   - **Jump** to trap_handler to continue execution
* **Restore Trap Context**
   - The inverse of the above process

---

#### Jump to trap_handler to Continue Execution

Q: Why use ``jr t1`` instead of ``call trap_handler`` to complete the jump?

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Save Trap context

Q: Why use ``jr t1`` instead of ``call trap_handler`` to complete the jump?
- In the memory layout, both the jump instruction and trap_handler in the .text.trampoline section are located within the code segment. The Assembler and Linker will set the address of the jump instruction and calculate the offset between the two addresses based on the address layout description in linker-qemu.ld. This will cause the actual effect of the jump instruction to be the current PC incremented by this offset.
- When this jump instruction is executed, its virtual address is set to the highest page in the address space by the kernel, so adding this offset cannot correctly get the entry address of trap_handler.


---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

 …
3. Address space from user perspective
4. The kernel manages the address space
5. Implement ASOS
* Start Paging Mode
* Implement Trampoline Mechanism
* ### Load and Execute the  Application
* Improve Implementation of trap handling
* Improve Implementation of sys_write

![bg right:40% 100%](figs/addr-space-os-detail.png)

---

#### Load and Execute the Application
1. Extend TCB(task control block)
2. Update task management

![bg right:61% 100%](figs/addr-space-os-detail.png)

---

#### Extend task control block
- memory_set: App's **address space** 
- trap_cx_ppn: The physical page number of the physical page frame where **trap context** located
- base_size: **Application Data Size** 
```
// os/src/task/task.rs
pub struct TaskControlBlock {
     pub task_cx: TaskContext,
     pub task_status: TaskStatus,
     pub memory_set: MemorySet,
     pub trap_cx_ppn: PhysPageNum,
     pub base_size: usize,
}
```


---

#### Update Task Management
- Create task control block 
   1. Form the virtual address space of the application according to the **ELF  file** of the application
   2. Create the **kernel stack** used for the application after switching to kernel mode
   3. Create the **TCB** of the application in the kernel address space
   4. Construct a **trap context** in the user address space
---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

 …
3. Address space from user perspective
4. The kernel manages the address space
5. Implement ASOS
* Start Paging Mode
* Implement Trampoline Mechanism
* Load and Execute the Application
 * ### Improve the Implementation of trap handling
* Improve the Implementation of sys_write

![bg right:40% 100%](figs/addr-space-os-detail.png)

---

#### Improve the Implementation of trap handling
Since the application's Trap context is not in the kernel address space, call current_trap_cx to **obtain the variable reference of the current application's trap context** instead of passing it as a parameter to trap_handler as before. 
However, nothing is changed for trap handling.

For simplicity, the trap handling process of S state -> S state is weakened: **panic** directly.
Note: ch9 will support S state -> S state trap handling

---

#### Improve the Implementation of trap handling


```rust
let restore_va = __restore as usize - __alltraps as usize + TRAMPOLINE;
unsafe {
   asm!(
      "fence.i",
      "jr {restore_va}",
   )
}
```

---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

 …
3. Address space from user perspective
4. The kernel manages the address space
5. Implement ASOS
* Start pagining mode
* Implement Trampoline Mechanism
* Load and Execute the Application
* Improved the Implementation of trap handling
* ### Improve the Implementation of sys_write

![bg right:40% 100%](figs/addr-space-os-detail.png)

---

#### Improve the Implementation of sys_write

- sys_write can no longer directly access data located in **application space** because of the separation of kernel and application address spaces
- **manually look up the page table** to know which physical page frames the data is placed on, then access them.


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Auxiliary Functions for Accessing Application Data

Auxiliary Function: provided by page_table, converts a buffer in the application address space into a form that can be directly accessed in the kernel space:
```rust
// os/src/mm/page_table.rs
pub fn translated_byte_buffer
```
1. Search the page table of the application,  find the physical address according to the virtual address of the application
2. Search the page table of the kernel,  find the virtual address of the kernel according to the physical address
3. Complete the reading and writing of application data based on the kernel virtual address

---
### Summary
- Address Space
- Contiguous Memory Allocation
- Segment Mechanism
- Page Table Mechanism
- Page Access Exception
- Can Write Ankylosaurus OS
![bg right:50% 100%](figs/addr-space-os-detail.png)
