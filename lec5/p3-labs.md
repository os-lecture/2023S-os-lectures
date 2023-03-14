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
- Practical Steps
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
   - Demarcate the memory address space of the APP, and cannot access the OS and other APPs beyond the boundary

---

#### Lab Requirements
- Understand address space
- master page mechanism
- Will handle page access exceptions
- Can write an operating system that supports page mechanism

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
- Proposed **Virtual memory (Virtual memory)** technology concept
   - Fritz-Rudolf Güntsch, Ph.D. student, TU Berlin, Germany
- Atlas Supervisor operating system in 1959
   - Professor Tom Kilburn's team at the University of Manchester, UK, demonstrating the Atlas computer and the Atlas Supervisor operating system
   - **created** paging technology and virtual memory technology (virtual memory, then called one-level storage system)
---
<style scoped>
{
  font-size: 30px
}
</style>

**Outline**

1. Lab Objectives and Steps
- Lab Objectives
- ### Practical Steps
2. System Architecture
3. Address Space from User Perspective
4. Kernel-managed Address Space
5. Implementing ASOS

![bg right:50% 100%](figs/addr-space-os-detail.png)

---

#### Practical steps
- Modify the APP link script (custom start address)
- load & execute application
- switch tasks and **task's address space**


---
#### Compilation steps
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch4
cd os
make run
```

---

#### Output result

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
#### Test Application

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
- RISC-V SV39 Page Mechanism
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
- springboard mechanism
- Extended TCBs
- Extended exceptions
  
![bg right:50% 100%](figs/addr-space-os-detail.png)

---

#### Building the application
```
└── user
     ├── build.py (removed: the script that sets the unique starting address for the application)
     └── src (userland library and application)
         ├── bin (each application)
         ├──...
         └── linker.ld (modification: place all applications in fixed locations in their respective address spaces)
```


---

#### Address space
```
├── os
     └── src
   ├── config.rs (modification: add some memory management related configuration)
   ├── linker-k210.ld (Modification: Introduce springboard page into memory layout)
     ├── linker-qemu.ld (modification: introduce the springboard page into the memory layout)
   ├── loader.rs (modification: only keep the function of obtaining the number of applications and data)
   ├── main.rs (modified)
```

---

#### Mm submodule
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
   ├──fs.rs (modification: sys_write implementation based on address space)
   ├── task
   ├──context.rs (Modification: Construct an initial task context to jump to different locations)
   ├──mod.rs (modified)
           └──task.rs (modified)
          └── trap
              ├── context.rs (modified: added more content in Trap context)
              ├── mod.rs (modification: modify the trap mechanism based on the address space)
              └── trap.S (Modified: Trap context save and restore assembly code based on address space)
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
- ### RISC-V SV39 Page Mechanism
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
- Page table base address register satp: The kernel mode control status register controls paging. satp has three domains:
   - MODE field: enable paging and select page table levels
   - ASID (Address Space Identifier, address space identifier) field: optional, used to reduce the overhead of context switching
   - PPN field: saves the physical address of the root page table
![w:800](figs/satp.png)



---
#### Page table base address register satp
- satp CSR:(S-Mode)
   Supervisor Address Translation and Protection, supervisor address translation and protection
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
- Then the S-Mode OS will write satp again after initializing the page table
   - Enable page table: `MODE`=8
   - Set page table starting physical address page number `PPN`

![bg right:50% 90%](figs/sv39-full.png)

---
<style scoped>
{
  font-size: 32px
}
</style>

#### Page Table Item Attributes

- V: valid bit
- R, W, X: read/write/execute bits
- U: Whether U-Mode can be accessed
- G: Whether it is valid for all addresses
- A: Access, access bit
- D: Dirty, modify bit
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
* Springboard Page
* Aplication Address Space 
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
- The virtual/physical memory space composed of several logical segments is bound to a running program (currently a running program is called **task** and **process**)
- A process's direct access to code and data is limited to its associated address space
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
* ### Springboard Page
* Aplication Address Space 
4. Kernel-managed Address Space
5. Implementing ASOS

![bg right:50% 100%](figs/addr-space-os-detail.png)

---
#### Springboard page
- The virtual address of the springboard Trampoline page of the application and the kernel is the same and mapped to the same physical page
- Placed is the execution code in ``trap.S``
<!-- - But user mode cannot access this memory area
- When an exception/interrupt occurs, it will jump to the ``_all_traps`` entry of the springboard page
- and after switching page tables, continue execution smoothly -->

![bg right:55% 100%](figs/trampoline.png)


---

#### Smooth transition based on springboard page

- **Privilege level transition**: When an exception/interrupt occurs, the CPU will jump to the ``_all_traps`` entry on the springboard page
- **Address Space Transition**: After switching page tables, the execution of kernel code can continue smoothly

![bg right:50% 100%](figs/trampoline.png)


---

#### Trapped in (Trap) context page
- The `_all_traps` assembly function on the springboard page will **save** the relevant registers to the trapping context
- The `_restore` assembly function on the springboard page will **restore** the relevant registers from the trapped context


![bg right:50% 100%](figs/trampoline.png)


---

#### Recap: OS Without Pages
The trapping context is saved on the top of the kernel stack, ``sscratch`` saves the application's kernel stack
- Only transfer **user/kernel stack pointer** through the ``sscratch`` register
- When an application traps to the kernel, sscratch has already pointed to the top of the application's kernel stack, and a command can be used to switch from the user stack to the kernel stack, and then directly push the Trap context to the top of the kernel stack.

<!--
![bg right:50% 100%](figs/trampoline.png)
-->

---
#### Comparing the OS with the page mechanism enabled

How to transfer **stack pointer** and **page table base address** only through the `sscratch` register?
- Can I use the previous method?
- Option 1: transfer **user/kernel stack pointer** through the `sscratch` register
- Scheme 2: transfer **user stack pointer/page table base address** through the `sscratch` register

![bg right:30% 100%](figs/kernel-stack.png)


---
#### Solution 1: transfer the user/kernel stack pointer through `sscratch` register
- Transfer **user/kernel stack pointer** through ``sscratch`` register
    - Currently, the sp pointer points to the kernel address space
    - While the user mode page table is still being used at this time
- This causes an exception to be generated in kernel mode, leading to the **system crash**

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
* Springboard Page
* ### Aplication Address Space 
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
* Build Kernel/application Page Tables
* Manage Address Space
5. Implementing ASOS


![bg right:50% 100%](figs/addr-space-os-detail.png)

---
#### Address space from the perspective of the kernel
- Understand address space
- Understanding falling into context pages


![bg right:55% 100%](figs/addr-space-os-detail.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Address space from the perspective of the kernel

- **Kernel understands address space**
   - Create & sense virtual/physical addresses
   - Traversing between kernel/application virtual address spaces
- Application page table
   - Represents the real-world application address space managed by the kernel
   - Let the CPU "see" the application address space

![bg right:40% 100%](figs/trampoline.png)

---

#### Page Table Mechanism
- **Manage physical memory**
- Build kernel/application page tables
- Enable page mechanism

![bg right:62% 100%](figs/page-overview.png)



---
#### Physical Memory

- Physical memory (RAM is set to 8MB)
   - Physical memory starting address: ``0x80000000``
   - Starting address of available physical memory: ``ekernel`` address in ``os/src/linker.ld``
   - End address of physical memory: ``0x80800000``
- What is in physical memory?


---
#### Physical Memory

- Physical memory (RAM is set to 8MB), including:
   - Application/kernel data/code/stack/heap
   - free space
- Especially various management data
   - Mission control block
      - MemorySet
          - Application/kernel multi-level page tables, etc.
      - Application core stack
      - Application's TrapContext page ....

---

#### Manage physical memory

- There is already a **part** on the physical memory for code and data of the kernel
- Need to manage the **remaining free memory** in units of a single physical page frame
   - When it is necessary to store application data or expand the multi-level page table of the application **allocate** free physical page frames
   - When the application fails or exits **recycle** all physical page frames occupied by the application


---
#### Manage physical memory
- **Dynamic allocation strategy** with contiguous memory
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
* Manage physical memory
### Build Kernel/Application Page Tables
* Manage address space
5. Implement ASOS

![bg right:50% 100%](figs/addr-space-os-detail.png)

---

#### SV39 multi-level page table

- SV39 multilevel page tables are managed in units of page-sized nodes. Each node is stored in exactly one physical page frame, and its position can be represented by a physical page number
- Satp CSR
![bg right:45% 100%](figs/sv39-full.png)


---
<style scoped>
{
  font-size: 32px
}
</style>

#### Build Kernel/Application Page Tables
- Page table starting physical address
- Page table content: virtual address <-> physical address mapping
   - Identity Mapping Identical Mapping
   - Random mapping Framed Mapping

VPN: Virtual Page Number
PPN: Physical Page Number
satp: CSR containing the PPN at the beginning of the page table
![bg right:40% 100%](figs/sv39-full.png)


---

#### Establish and remove virtual and real address mapping relationship

* Find a **page table entry** corresponding to a virtual address in the multi-level page table.
* The insertion and deletion of **key-value pairs** can be completed by **modifying the content of the page table entry**, so as to realize the establishment and removal of **mapping relationship**.
![bg right:40% 100%](figs/sv39-full.png)

---
#### Enable page mechanism

- set ``satp=root_ppn``

Containment relationship of core data structures
```
TCB-->MemorySet-->PageTable-->root_ppn
Task Control Block ---------------> Task's page table base address
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
* Manage physical memory
* Build kernel/application page tables
### Manage address space
5. Implement ASOS

![bg right:50% 100%](figs/addr-space-os-detail.png)

---

#### Application address space
![w:950](figs/app-as-full.png)

---
#### Logic section

- Logical segment: a segment of virtual memory with contiguous addresses used by the kernel/application
- The virtual address space where the kernel/application runs: consists of multiple logical segments

**Ideal: Plump vs. Reality: Skinny**
- Application's **page table**
   - Application address space in real world
- The **logical segment** of the application
   - ideally the application address space

![bg right:48% 100%](figs/seg-addr-space.png)


---

#### The data structure of the logical segment ``MapArea``
- Logical segment: a segment of virtual memory with continuous addresses
```rust
// os/src/mm/memory_set.rs

pub struct MapArea {
     vpn_range: VPNRange, // a continuous range of virtual page numbers
     data_frames: BTreeMap<VirtPageNum, FrameTracker>, //VPN<-->PPN mapping relationship
     map_type: MapType, //map type
     map_perm: MapPermission, //readable/writable/executable attribute
}
```
``data_frames`` is a key-value pair container that holds each virtual page in the logical segment and the physical page FrameTracker it is mapped to


---
<style scoped>
{
  font-size: 30px
}
</style>

#### The data structure of the address space ``MemorySet``
- **Address space**: a series of associated logical segments that are not necessarily continuous
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
#### When the kernel manages the task address space
- Operating system managed **address space** ``MemorySet``=``PageTable``+``MapAreas``
    - **CREATE** TASK: create a ``MemorySet`` of the task
    - **Clear** tasks: recycle the memory occupied by ``MemorySet`` of tasks
    - **Adjust** the memory space size of the application: Modify the ``MemorySet`` of the task
    - **Switch** from user mode to kernel mode: switch the ``MemorySet`` of the task to the ``MemorySet`` of the kernel
    - Kernel mode **switch** to user mode: switch the ``MemorySet`` of the kernel to the ``MemorySet`` of the task

---
#### The process of creating a new task address space `MemorySet`
- create page table
- Create logical segment vector

![bg right:64% 100%](figs/app-as-full.png)


---

#### Insert/delete a logical segment in the address space
- Update the corresponding page table entry in the page table
- Update the content of the physical page frame corresponding to the logical segment
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
* Start pagination mode
* Implement springboard mechanism
* Load and execute the application
* Improved implementation of trap handling
* Improved implementation of sys_write

![bg right:45% 100%](figs/addr-space-os-detail.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Extensions to time-sharing multitasking operating systems

1. Create **kernel page table**, enable the paging mechanism, and establish the virtual address space of the kernel;
2. Extend **Trap Context**, switch page table (ie switch virtual address space) during the process of saving and restoring Trap context;
3. Establish the **springboard space** required for switching between the kernel address space and the application address space;
4. Extended **Task Control Block** to include virtual memory-related information, and when loading and executing a task based on an application, establish the virtual address space of the application;
5. Improve the implementation of **system calls** such as trap processing and sys_write to support separate application address space and kernel address space.

---
#### Start pagination mode
1. Create a kernel address space
2. Initialization of the memory management subsystem

![bg right:63% 100%](figs/trampoline.png)

---

#### Create a global instance of the kernel address space
- Kernel address space ``KERNEL_SPACE``
```rust
pub static ref KERNEL_SPACE: MemorySet = MemorySet::new_kernel()
```


---
#### Initialization of the memory management subsystem
    1. Initialize the **free physical memory** according to the heap (heap) for dynamic continuous memory management
    2. Implement **physical page frame allocation** management initialization based on the heap
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
### Implement the springboard mechanism
* Load and execute the application
* Improved implementation of trap handling
* Improved implementation of sys_write

![bg right:40% 100%](figs/addr-space-os-detail.png)

---

#### Motivation for implementing the springboard mechanism

After the paging mechanism is started, applications and kernels in different address spaces can perform normal **privilege level switching** operations and **data interaction**.

![bg right:50% 100%](figs/trampoline.png)

---
<style scoped>
{
  font-size: 32px
}
</style>

#### The idea of springboard mechanism

- The **highest virtual page** in the virtual address space of the kernel and application is a trampoline page
- After the **privilege level** switch, it is necessary to quickly complete the **address space** switch, **kernel stack** switch, and **smoothly continue to execute** the kernel code

![bg right:50% 100%](figs/trampoline.png)

---

#### The idea of springboard mechanism

- The **second highest virtual page** of the application address space is set to store the trap context of the application

![bg right:54% 100%](figs/trampoline.png)

---

#### The idea of springboard mechanism

- Q: Why not put the Trap context directly in the kernel stack of the application?

![bg right:54% 100%](figs/trampoline.png)


---

#### The idea of springboard mechanism

- Q: Why not put the Trap context directly in the kernel stack of the application?
   - To access the Trap context address in the kernel stack, you need to switch **page table** first;
   - Page table information is placed in **Trap context**, forming mutual dependence.


![bg right:40% 100%](figs/trampoline.png)

---

#### Create a springboard page
Place the entire assembly code in trap.S in the .text.trampoline segment and align it to one page of the code segment when adjusting the memory layout
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

1. You must switch to the kernel address space first, which requires writing the token of the kernel address space into the satp register;
2. Afterwards, it is also necessary to save the position of the top of the kernel stack of the application, so that the Trap context can be saved based on it.
3. These two steps require the use of general-purpose registers as a temporary turnaround, however we cannot do this without corrupting any of the general-purpose registers.
4. Therefore, we have to save the Trap context in a virtual page in the application address space instead of switching to the kernel address space to save it. -->





---

#### Extend the Trap context data structure
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

#### Switch Traps context
* **Save Trap context**
   - Switch **User Stack Pointer** to TrapContext in user address space
   - Save general registers, sstatus, sepc in TrapContext**
   - Read out **kernel_satp, kernel_sp, trap_handler in TrapContext**
   - **switch** kernel address space, switch to kernel stack
   - **jump** to trap_handler to continue execution
* **Restore Trap Context**
   - The inverse of the above process

---

#### Jump to trap_handler to continue execution

Q: Why use ``jr t1`` instead of ``call trap_handler`` to complete the jump?

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Save Trap context

Q: Why use ``jr t1`` instead of ``call trap_handler`` to complete the jump?
- In the memory layout, the jump instruction and trap_handler in this .text.trampoline segment are within the code segment, and the assembler (Assembler) and linker (Linker) will describe it according to the address layout of linker-qemu.ld, Set the address of the jump instruction, and calculate the offset between the two addresses, so that the actual effect of the jump instruction is that the current pc will automatically increase the offset.
- When this jump instruction is executed, its virtual address is set by the operating system kernel within the highest page in the address space, so adding this offset cannot correctly get the entry address of trap_handler.


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
* Start pagination mode
* Implement springboard mechanism
### Load and execute the application
* Improved implementation of trap handling
* Improved implementation of sys_write

![bg right:40% 100%](figs/addr-space-os-detail.png)

---

#### Load and execute the application
1. Extended task control block
2. Update task management

![bg right:61% 100%](figs/addr-space-os-detail.png)

---

#### Extended task control block TCB
- App's **address space** memory_set
- The physical page number trap_cx_ppn of the physical page frame where **Trap context** is located
- **application data size** base_size
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

#### Update task management
- Create mission control block TCB
   1. Form the virtual address space of the application according to the **ELF execution file** content of the application
   2. Establish the **kernel stack** used after the application is converted to the kernel state
   3. Create the **TCB** of the application in the kernel address space
   4. Construct a **Trap context**TrapContext in the user address space
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
* Start pagination mode
* Implement springboard mechanism
* Load and execute the application
### Improve the implementation of trap handling
* Improved implementation of sys_write

![bg right:40% 100%](figs/addr-space-os-detail.png)

---

#### Improve the implementation of trap handling
Since the application's Trap context is not in the kernel address space, call current_trap_cx to **obtain the variable reference of the current application's Trap context** instead of passing it as a parameter to trap_handler as before. As for the process of trap processing, nothing has changed.

For the sake of simplicity, the trap processing process of S state -> S state is weakened: direct panic.
Note: ch9 will support S state -> S state trap processing

---

#### Improve the implementation of trap handling


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
* Start pagination mode
* Implement springboard mechanism
* Load and execute the application
* Improved implementation of trap handling
### Improve the implementation of sys_write

![bg right:40% 100%](figs/addr-space-os-detail.png)

---

#### Improve the implementation of sys_write

- Due to the separation of kernel and application address spaces, sys_write can no longer directly access data located in **application space**
- It is necessary to **manually look up the page table** to know which physical page frames the data is placed on and accessed.


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Helper functions for accessing application spatial data

The page table module page_table provides a helper function that converts a buffer in the application address space into a form that can be directly accessed in the kernel space:
```rust
// os/src/mm/page_table.rs
pub fn translated_byte_buffer(
```
1. Search the page table of the application, and find the physical address according to the virtual address of the application
2. Find the page table of the kernel and find the virtual address of the kernel according to the physical address
3. Complete the reading and writing of application data based on the kernel virtual address

---
### Summary
- address space
- contiguous memory allocation
- segment mechanism
- Page table mechanism
- Page access exception
- Ability to write Ankylosaurus OS
![bg right:50% 100%](figs/addr-space-os-detail.png)