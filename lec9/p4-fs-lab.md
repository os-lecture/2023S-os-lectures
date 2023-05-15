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
- Execution: Data access: memory--disk, **file-based reading and writing**


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
├── easy-fs (新增：从内核中独立出来的一个简单的文件系统 EasyFileSystem 的实现)
│ ├── Cargo.toml
│ └── src
│ ├── bitmap.rs (位图抽象)
│ ├── block_cache.rs (块缓存层，将块设备中的部分块缓存在内存中)
│ ├── block_dev.rs (声明块设备抽象接口 BlockDevice，需要库的使用者提供其实现)
│ ├── efs.rs (实现整个 EasyFileSystem 的磁盘布局)
│ ├── layout.rs (一些保存在磁盘上的数据结构的内存布局)
│ ├── lib.rs (定义的必要信息)
│ └── vfs.rs (提供虚拟文件系统的核心抽象，即索引节点 Inode)
├── easy-fs-fuse (新增：将当前 OS 上的应用可执行文件按照 easy-fs 的格式进行打包)
│ ├── Cargo.toml
│ └── src
│       └─ main.rs
```

---
#### Code structure
Improve OS
```
├── os
│ ├── build.rs
│ ├── Cargo.toml (修改：新增 Qemu 和 K210 两个平台的块设备驱动依赖 crate)
│ ├── Makefile (修改：新增文件系统的构建流程)
│ └── src
│ ├── config.rs (修改：新增访问块设备所需的一些 MMIO 配置)
│ ├── console.rs
│ ├── drivers (修改：新增 Qemu 和 K210 两个平台的块设备驱动)
│ │ ├── block
│ │ │ ├── mod.rs (将不同平台上的块设备全局实例化为 BLOCK_DEVICE 提供给其他模块使用)
│ │ │ ├── sdcard.rs (K210 平台上的 microSD 块设备, Qemu不会用)
│ │ │ └── virtio_blk.rs (Qemu 平台的 virtio-blk 块设备)
│ │ └── mod.rs
```

---
#### Code structure
Improve OS
```
├── os
│ ├── fs (修改：在文件系统中新增常规文件的支持)
│ │ ├── inode.rs (新增：将Inode抽象封装为OSInode，并实现fs子模块的 File Trait)
│ ├── loader.rs (移除：应用加载器 loader 子模块，本章开始从文件系统中加载应用)
│ ├── mm
│ │ ├── memory_set.rs (修改：在创建地址空间的时候插入 MMIO 虚拟页面)
│ ├── syscall
│ │ ├── fs.rs (修改：新增 sys_open)
│ │ └── process.rs (修改：sys_exec 改为从文件系统中加载 ELF，并支持命令行参数)
│ ├── task
│ │ ├── mod.rs (修改初始进程 INITPROC 的初始化)
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
/// 功能：打开一个常规文件，并返回可以访问它的文件描述符。
/// 参数：path 描述要打开的文件的文件名
/// （简单起见，文件系统不需要支持目录，所有的文件都放在根目录 / 下），
/// flags 描述打开文件的标志，具体含义下面给出。
/// 返回值：如果出现了错误则返回 -1，否则返回打开常规文件的文件描述符。
/// 可能的错误原因是：文件不存在。
/// syscall ID：56
fn sys_open(path: &str, flags: u32) -> isize
```
---

#### close() system call

```
/// 功能：当前进程关闭一个文件。
/// 参数：fd 表示要关闭的文件的文件描述符。
/// 返回值：如果成功关闭则返回 0 ，否则返回 -1 。
/// 可能的出错原因：传入的文件描述符并不对应一个打开的文件。

/// syscall ID：57
fn sys_close(fd: usize) -> isize
```

---

#### read() system call

```
/// 功能：当前进程读取文件。
/// 参数：fd 表示要读取文件的文件描述符。
/// 返回值：如果成功读入buf，则返回 读取的字节数，否则返回 -1 。
/// 可能的出错原因：传入的文件描述符并不对应一个打开的文件。

/// syscall ID：63
sys_read(fd: usize, buf: *const u8, len: usize) -> isize
```


---

#### write() system call

```
/// 功能：当前进程写入一个文件。
/// 参数：fd 表示要写入文件的文件描述符。
/// 返回值：如果成功把buf写入，则返回写入字节数 ，否则返回 -1 。
/// 可能的出错原因：传入的文件描述符并不对应一个打开的文件。

/// syscall ID：64
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
     let read_len = read(fd, &mut buffer) as usize;// read file content into the buffer
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
- The content of a directory is an array of ``DirEntry``
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
     cache: [u8; BLOCK_SZ], //512字节数组
     block_id: usize, //对应的块编号
     //底层块设备的引用，可通过它进行块读写
     block_device: Arc<dyn BlockDevice>,
     modified: bool, //它有没有被修改过
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
     //调用open_file函数获得一个OSInode结构的inode
     if let Some(inode) = open_file(path.as_str(),
                            OpenFlags::from_bits(flags).unwrap()) {
         let mut inner = task. inner_exclusive_access();
         let fd = inner.alloc_fd(); //得到一个空闲的fd_table项的idx，即fd
         inner.fd_table[fd] = Some(inode); //把inode填入fd_table[fd]中
         fd as isize // return fd
     ...
```
If failed, return `-1`

---

#### Open (create) a file

```rust
fn open_file(name: &str, flags: OpenFlags) -> Option<Arc<OSInode>> {
    …
  ROOT_INODE.create(name) //在根目录中创建一个DireEntry<name，inode>
                 .map(|inode| {//创建进程中fd_table[OSInode]
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
  ROOT_INODE.find(name) //在根目录中查找DireEntry<name，inode>
             .map(|inode| { //创建进程中fd_table[OSInode]
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
