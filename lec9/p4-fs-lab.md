---
noteId: "db866d32d86511edb7d82f06d610f713"
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

# Lecture 9 File System

## Section 4 OS with File System

Filesystem OS (FOS)


<br>
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

### 1. Lab objectives and steps
- Lab objectives
- Interface and data structure of file system 
- Practical steps
2. Code structure
3. Application design
4. Kernel programming

![bg right:59% 95%](figs/fsos-fsdisk.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Previous lab objectives:

Improve performance, simplify development, and enhance security
- Process OS: Enhance **process management** and resource management
- Address Space OS: Isolate the memory **address space** accessed by applications, so they do not need to consider their runtime starting address
- Multiprog & time-sharing OS: Enable effective **CPU** sharing among applications to improve overall system performance and efficiency
- BatchOS: **Isolate** applications from the OS to enhance system security and improve execution efficiency
- LibOS: **Isolate** applications from hardware to simplify hardware access and reduce complexity

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Lab objectives

Support **data persistence**
- Persistently store data in **file** format and allow for file data reading and writing
- Allow **processes** to use file resources
- Enable the following **system calls** at the application level:
    - open/read/write/close

![bg right:51% 90%](figs/fs-intro.png)

---
#### History
- 1965: MULTICS describes the future OS
   - Pointed the way forward
     - Described **file** data as an unformatted byte stream
     - Introduced the concept of a **hierarchical file system** for the first time
   - Inspired the design of UNIX
      - The UNIX file system sees **everything as a file**
---

#### Lab requirements

- Understand file system/file **concepts**
- Understand the **design and implementation** of file system
- Understand the entire file **access process** of the application <-> library <->...<-> device driver
- **Be able to write** an OS that supports the file system

<!-- Tyrannosaurus Rex operating system -->

![bg right:40% 100%](figs/rex.png)



---

#### Issues to consider:

- How is the file system organized on the hard disk? What is the layout of hard disk?
- How are free disk blocks managed?
- How are files/directories represented?
   - What is a file? What is a directory?
- How is file/directory data content represented?
- How to access a file?

![bg right:38% 95%](figs/fs-intro.png)



---

#### Lab file types
- Current
   - Regular file
   - Directory file
- Future
   - Link file
   - Device file
   - Pipe file

![bg right:50% 95%](figs/fs-intro.png)

---

#### General idea

![bg 90%](figs/efs-disk.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
**Outline**

1. Lab objectives and steps
- Lab objectives
### Interface and data structure of file system 
- Practical steps
2. Code structure
3. Application design
4. Kernel programming

![bg right:50% 95%](figs/fsos-fsdisk.png)


---

#### File access process

![bg 80%](figs/fs-prog-intro.png)


---

#### Access interface of file system 

![w:950](figs/fs-arch-1.png)

---

#### Access interface of file system 

![w:950](figs/fs-arch-2.png)


---

#### Access interface of file system 

![w:1100](figs/fs-arch-3.png)


---

#### Access interface of file system 

![w:750](figs/fs-arch-4.png)



---

#### Data structure of file system 

![w:950](figs/easyfs-detail.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
**Outline**

1. Lab objectives and steps
- Lab objectives
- Interface and data structure of file system 
### Practical steps
2. Code structure
3. Application design
4. Kernel programming

![bg right:50% 95%](figs/fsos-fsdisk.png)


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Lab steps

- Compilation: The kernel is compiled independently, creating a separate kernel image
- Compilation: After compiling the application programs, a **file system image is formed through organization**
- Construction: The management and initialization of processes, establishing virtual memory space based on the page table mechanism
- Construction: Building the file system
- Execution: Privilege level switching, switching between processes and the OS
- Execution: Switching address space, accessing data across address spaces
- Execution: **Loading applications from the file system to create processes**
- Execution: Data access: memory-disk, **file-based reading and writing**


---

#### Practical steps
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch6
cd os
make run
```

---
#### Practical steps
```
[RustSBI output]
...
filetest_simple
fantastic_text
***************/
Rust user shell
>>
```
After the operating system boots the ``shell``, users can execute applications by typing in the application name in the ``shell``

From the user interface, we cannot see anything about the file system


---
#### Practical steps
Here, we will run the test case "filetest_simple" for this chapter:
```
>> filetest_simple
file_test passed!
Shell: Process 2 exited with code 0
>>
```
It will output "Hello, world!" to another file "filea", and read the contents to confirm the output is correct

---
#### Practical steps

We can also use the command line tool "cat_filea" to view the content of "filea" more intuitively:
```
>> cat_filea
Hello, world!
Shell: Process 2 exited with code 0
>>
```
---

**Outline**

1. Lab objectives and steps
### 2. Code structure
3. Application design
4. Kernel programming

![bg right:50% 95%](figs/fsos-fsdisk.png)


---

#### Software architecture

- File operations
     - open
     - read
     - write
     - close
  
![bg right:60% 90%](figs/fsos-fsdisk.png)

---
#### Code structure
Add easy-fs
```
├── easy-fs (new: an implementation of EasyFileSystem, a simple file system independent from the kernel)
│ ├── Cargo.toml
│ └── src
│ ├── bitmap.rs (bitmap abstraction)
│ ├── block_cache.rs (block cache layer, which caches some blocks in the block device in memory)
│ ├── block_dev.rs (declare the block device abstract interface BlockDevice, which requires the user of the library to provide its implementation)
│ ├── efs.rs (realize the disk layout of the entire EasyFileSystem)
│ ├── layout.rs (memory layout of some data structures stored on disk)
│ ├── lib.rs (necessary information for definition)
│ └── vfs.rs (provides the core abstraction of the virtual file system, that is, the index node Inode)
├── easy-fs-fuse (new: package the application executable file on the current OS in the easy-fs format)
│ ├── Cargo.toml
│ └── src
│ └── main.rs
```

---
#### Code structure
Improve OS
```
├── os
│ ├── build.rs
│ ├── Cargo.toml (Modification: Add Qemu and K210 platform block device drivers to depend on crate)
│ ├── Makefile (Modification: Add file system build process)
│ └── src
│ ├── config.rs (modification: add some MMIO configurations required to access block devices)
│ ├── console.rs
│ ├── drivers (modification: add block device drivers for Qemu and K210 platforms)
│ │ ├── block
│ │ │ ├── mod.rs (globally instantiate block devices on different platforms as BLOCK_DEVICE for other modules to use)
│ │ │ ├── sdcard.rs (microSD block device on K210 platform, Qemu will not use it)
│ │ │ └── virtio_blk.rs (virtio-blk block device for Qemu platform)
│ │ └── mod.rs
```

---
#### Code structure
Improve OS
```
├── os
│ ├── fs (Modification: Add support for regular files in the file system)
│ │ ├── inode.rs (new: encapsulate the Inode abstraction provided by easy-fs into the OSInode seen by the kernel
│ │ and implement the File Trait of the fs submodule)
│ ├── loader.rs (removed: application loader submodule, this chapter begins to load applications from the file system)
│ ├── mm
│ │ ├── memory_set.rs (modification: insert MMIO virtual page when creating address space)
│ ├── syscall
│ │ ├── fs.rs (modification: add sys_open)
│ │ └── process.rs (modification: sys_exec is changed to load ELF from the file system, and supports command line parameters)
│ ├── task
│ │ ├── mod.rs (modify the initialization of the initial process INITPROC)
```


---

#### Storage block devices

![w:800](figs/fsos-fsdisk.png)

---

**Outline**

1. Lab objectives and steps
2. Code structure
### 3. Application design
- Files and directories
- File access system calls
4. Kernel programming

![bg right:50% 95%](figs/fsos-fsdisk.png)


---

#### Understanding files

<!-- **program design** -->
   - Virtualization and abstraction of persistent storage
      - Tape, Disk, SSD...
      - Users use them to store the data they really care about


![bg right:53% 95%](figs/fsos-fsdisk.png)



---
<style scoped>
{
  font-size: 30px
}
</style>
#### Understanding files from an application perspective

- A **file** is a special linear **byte array**, where each byte can be read or written
- Each file has a **string name** that is understandable to the user
- Each file also has a low-level name that is understandable to the programmer - the **file descriptor**
- Displaying the linear byte content of a file
  ```
   $ hexedit os/src/main.rs
  ```
![bg right:35% 95%](figs/hexedit-mainrs.png)



---
<style scoped>
{
  font-size: 30px
}
</style>
### Understanding files from a kernel perspective

- A **file** is **data** on a storage device that needs to be managed through a file system
- The **structure that manages files** is called an inode, which describes various properties of the file and its data location
- Displaying the linear byte content of a file
  ```
   $ cd os ; stat src/main.rs
  ```
![bg right:35% 95%](figs/stat-mainrs.png)

---

#### Understanding directories from an application perspective

- A **directory** is a special file that contains a list of **filenames** under that directory
- Displaying the contents of a directory

```
   $ cd os ; ls -la
```
 
![bg right:50% 95%](figs/ls-os-dir.png)


---

#### Understanding directories from a kernel perspective

- A **directory** is a special file that contains an array of (user-readable filename, inode) pairs
- ``DirEntry`` array

```rust
pub struct DirEntry {
     name: [u8; NAME_LENGTH_LIMIT + 1],
     inode_number: u32,
}
```
 
![bg right:35% 100%](figs/ls-os-dir.png)



---
<style scoped>
{
  font-size: 30px
}
</style>
**Outline**

1. Lab objectives and steps
2. Code structure
3. Application design
- Files and directories
- ###  File access system calls
4. Kernel programming

![bg right:50% 95%](figs/fsos-fsdisk.png)


---

#### open() system call

```
/// Function: Open a regular file and return the file descriptor that can access it.
/// Parameter: path describes the file name of the file to be opened
/// (For simplicity, the file system does not need to support directories, all files are placed under the root directory /),
/// flags describe the flags to open the file, the specific meaning is given below.
/// Return value: -1 if an error occurs, otherwise the file descriptor to open a regular file.
/// The possible cause of the error is: the file does not exist.
/// syscall ID: 56
fn sys_open(path: &str, flags: u32) -> isize
```
---

#### close() system call

```
/// Function: The current process closes a file.
/// Parameter: fd indicates the file descriptor of the file to be closed.
/// Return value: 0 if closed successfully, otherwise -1.
/// Possible cause of error: The incoming file descriptor does not correspond to an open file.

/// syscall ID: 57
fn sys_close(fd: usize) -> isize
```

---

#### read() system call

```
/// Function: The current process reads the file.
/// Parameter: fd indicates the file descriptor to read the file.
/// Return value: If buf is successfully read, the number of bytes read will be returned, otherwise -1 will be returned.
/// Possible cause of error: The incoming file descriptor does not correspond to an open file.

/// syscall ID: 63
sys_read(fd: usize, buf: *const u8, len: usize) -> isize
```


---

#### write() system call

```
/// Function: The current process writes a file.
/// Parameter: fd indicates the file descriptor to write to the file.
/// Return value: If buf is successfully written, the number of bytes written will be returned, otherwise -1 will be returned.
/// Possible cause of error: The incoming file descriptor does not correspond to an open file.

/// syscall ID: 64
fn sys_write(fd: usize, buf: *const u8, len: usize) -> isize
```



---

#### Application examples

```
// user/src/bin/filetest_simple.rs
pub fn main() -> i32 {
     let test_str = "Hello, world!";
     let filea = "filea\0";
     // Create file filea, return file descriptor fd (signed integer)
     let fd = open(filea, OpenFlags::CREATE | OpenFlags::WRONLY);
     write(fd, test_str.as_bytes()); // write test_str to the file
     close(fd); // close the file
     let fd = open(filea, OpenFlags::RDONLY); // open the file in read-only mode
     let mut buffer = [0u8; 100]; // 100 byte array buffer
     let read_len = read(fd, &mut buffer) as usize;// read the file content into the buffer
     close(fd); // close the file
}
```

---

**Outline**

1. Lab objectives and steps
2. Code structure
3. Application design
### 4. Kernel programming
- Core data structure
- File management mechanism

![bg right:50% 100%](figs/fsos-fsdisk.png)


---

#### Core data structure
- Process management files
   - Directories, files
   - Inodes
   - File descriptor
   - File descriptor table

![bg right:50% 95%](figs/fsos-fsdisk.png)


---

#### Core data structure

- Files are located in the root directory  ``ROOT_INODE``
- Directories are represented by an array of ``DirEntry``
- Files/directories are represented by ``inode``
```rust
pub struct DirEntry {
     name: [u8; NAME_LENGTH_LIMIT + 1],
     inode_number: u32,
}
...
let fd = open(filea, OpenFlags::RDONLY);
```
![bg right:42% 95%](figs/fs-fsdisk.png)


---

#### Core data structure

- Opened files are in the process ``fd_table``
- ``fd_table`` is an array of ``OSInode``
```rust
pub struct TaskControlBlockInner {
     pub fd_table: ... //file descriptor table

pub struct OSInode {//process management inode
     readable: bool, writable: bool,
     inner: UPSafeCell<OSInodeInner>,
}

pub struct OSInodeInner {
     offset: usize, //The offset position of file read and write
     inode: Arc<Inode>,//storage device inode
}
```
![bg right:42% 95%](figs/fs-fsdisk.png)



---

#### Core data structures
- Superblock
- inode/data bitmap
- disk_inode
- disk_data
- blk_cache

![bg right:66% 95%](figs/fsos-fsdisk.png)



---

#### Superblock

SuperBlock describes global information about the file system
```rust
pub struct SuperBlock {
     magic: u32,
     pub total_blocks: u32,
     pub inode_bitmap_blocks: u32,
     pub inode_area_blocks: u32,
     pub data_bitmap_blocks: u32,
     pub data_area_blocks: u32,
}
```
![bg right:50% 95%](figs/fs-fsdisk.png)




---
<style scoped>
{
  font-size: 30px
}
</style>
#### Bitmap (bitmap)

Bitmap describes global information about the file system
* There are two types of bitmaps in the easy-fs layout
     * Inode bitmap
     * Data block bitmap
```rust
pub struct Bitmap {
     start_block_id: usize,
     blocks: usize,
}
```
![bg right:50% 95%](figs/fs-fsdisk.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### DiskInode

- ``read_at`` and ``write_at`` convert the file offset and buf length to a series of block numbers, then read and write data blocks through ``get_block_cache``.
- ``get_block_id`` method shows the most important function of DiskInode (i.e., indexing data block). It can find the number of the block used to save the file content, so that subsequent access to this data block can be performed

![bg right:42% 95%](figs/inode-fsdisk.png)


---

#### DiskInode

DiskInode describes file information and data
```rust
pub struct DiskInode {
     pub size: u32,
     pub direct: [u32; INODE_DIRECT_COUNT],
     pub indirect1: u32,
     pub indirect2: u32,
     type_: DiskInodeType,
}
```
![bg right:55% 95%](figs/inode-fsdisk.png)


---

#### Data blocks and directory entries

```rust
type DataBlock = [u8; BLOCK_SZ];

pub struct DirEntry {
     name: [u8; NAME_LENGTH_LIMIT + 1],
     inode_number: u32,
}
```
![bg right:55% 95%](figs/fs-fsdisk.png)


---

#### Block cache 

```rust
pub const BLOCK_SZ: usize = 512;

pub struct BlockCache {
     cache: [u8; BLOCK_SZ], //512 byte array
     block_id: usize, //corresponding block number
     //A reference to the underlying block device, through which blocks can be read and written
     block_device: Arc<dyn BlockDevice>,
     modified: bool, //has it been modified
}
```
``get_block_cache``: get a block cache numbered ``block_id``
![bg right:40% 95%](figs/fs-fsdisk.png)



---

**Outline**

1. Lab objectives and steps
2. Code structure
3. Application design
4. Kernel programming
- Core data structures
### File management mechanism

![bg right:50% 95%](figs/fsos-fsdisk.png)


---

#### Overview of file management mechanism

1. File system initialization
2. Opening and closing files
3. Loading applications based on files
4. Reading and writing files

![bg right:50% 95%](figs/fsos-fsdisk.png)

---

#### File system initialization

1. Open block device BLOCK_DEVICE
2. Open file system from  block device BLOCK_DEVICE
3. Get the inode of the root directory from the file system

```rust
lazy_static! {
     pub static ref BLOCK_DEVICE = Arc::new(BlockDeviceImpl::new());
 …
lazy_static! {
     pub static ref ROOT_INODE: Arc<Inode> = {
         let efs = EasyFileSystem::open(BLOCK_DEVICE.clone());
         Arc::new(EasyFileSystem::root_inode(&efs))
```


---

#### Open (create) a file

```rust
pub fn sys_open(path: *const u8, flags: u32) -> isize {
     //Call the open_file function to obtain an inode of the OSInode structure
     if let Some(inode) = open_file(path.as_str(),
                            OpenFlags::from_bits(flags).unwrap()) {
         let mut inner = task. inner_exclusive_access();
         let fd = inner.alloc_fd(); //Get the idx of a free fd_table item, namely fd
         inner.fd_table[fd] = Some(inode); //fill inode into fd_table[fd]
         fd as isize // return fd
     ...
```
If failed, return `-1`

---

#### Open (create) a file

```rust
fn open_file(name: &str, flags: OpenFlags) -> Option<Arc<OSInode>> {
    …
  ROOT_INODE.create(name) //Create a DireEntry<name, inode> in the root directory
                 .map(|inode| {//Create fd_table[OSInode] in the process
                     Arc::new(OSInode::new(
                         readable,
                         writable,
                         inode, ))
                 })
```
Create a file in the root directory ``ROOT_INODE``, return ``OSInode``



---

#### Open (find) a file

```rust
fn open_file(name: &str, flags: OpenFlags) -> Option<Arc<OSInode>> {
    …
  ROOT_INODE.find(name) //Find DireEntry<name, inode> in the root directory
             .map(|inode| { // fd_table[OSInode] in the creation process
                 Arc::new(OSInode::new(
                     readable,
                     writable,
                     inode ))
             })
```
Find a file in the root directory ``ROOT_INODE``, return ``OSInode``


---

#### Close a file

```rust
pub fn sys_close(fd: usize) -> isize {
     let task = current_task().unwrap();
     let mut inner = task.inner_exclusive_access();
      …
     inner.fd_table[fd].take();
     0
}
```
sys_close : hange the corresponding item in the process control block's file descriptor table to None, indicating that it is already free. This also reduces the reference count of the file. When the reference count is reduced to 0, the resources occupied by the file will be automatically recycled

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Load application based on the file

Load application based on the file (ELF executable file format)
```rust
     pub fn sys_exec(path: *const u8) -> isize {
         if let Some(app_inode) = open_file(path.as_str(), ...) {
             let all_data = app_inode. read_all();
             let task = current_task().unwrap();
             task.exec(all_data.as_slice()); 0
     } else { -1 }
```

When obtaining the ELF file data of the application, first call the ``open_file`` function to open the application file in read-only mode and obtain its corresponding ``OSInode``. Then use ``OSInode::read_all`` to read all the data of the file into a vector ``all_data``



---
<style scoped>
{
  font-size: 30px
}
</style>
#### Reading and writing files

- Based on file abstract interface and file descriptor table
- Can handle basic file reading and writing according to unstructured byte stream
```rust
   pub fn sys_write(fd: usize, buf: *const u8, len: usize) -> isize {
         if let Some(file) = &inner. fd_table[fd] {
             file.write(
                 UserBuffer::new(translated_byte_buffer(token, buf, len))
             ) as isize
```
The operating system finds a file in the file descriptor table of the current process through the file descriptor, and does not need to care about the specific type of the file.

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Read and write files

- Based on the file abstraction interface and file descriptor table
- Basic file reading and writing can be processed according to the unstructured byte stream
```rust
   pub fn sys_read(fd: usize, buf: *const u8, len: usize) -> isize {
         if let Some(file) = &inner. fd_table[fd] {
             file. read(
                 UserBuffer::new(translated_byte_buffer(token, buf, len))
             ) as isize
```
The OS finds a file by file descriptor in the current process's file descriptor table, without worrying about the specific type of the file

---
### Summary
- Concept and implementation of files/directories
- Design and implementation of file system 
- Process and file system
- Can write a Tyrannosaurus OS
![bg right:30% 100%](figs/rex.png)
