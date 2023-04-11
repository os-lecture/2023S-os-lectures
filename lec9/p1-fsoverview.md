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

# Lecture 9 File System

## Section 1 File and File system


<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023

---

**Outline**

### 1. Files
- Concept of a file
- File operations
- File descriptors
2. File Systems and File Organization

---

#### What is a File System?

* [File system](https://blog.csdn.net/wsmzyzdw/article/details/82908736) is a **method** and **data structure** for organizing files on storage devices.

![bg right:60% 90%](figs/fs01.png)

---

#### What is a file system?

* [File system](https://blog.csdn.net/wsmzyzdw/article/details/82908736) is a **method** and **data structure** for organizing files on storage devices.
* [File system](https://chinese.freecodecamp.org/news/file-systems-architecture-explained/) is the **subsystem** responsible for files naming, storage, and retrieval in OS.

![bg right:50% 90%](figs/fs1.png)

---
#### What is a file?
- A file is a **collection of data items** with a symbolic name. A file consists of a sequence of bytes.
   - File is the basic data unit of a file system
   - The filename is the identifier of the file

- File Header: Store **File Information** in filesystem metadata
   - File Properties: name, type, location, creation time, ...
   - File storage location and order

---
#### Everything is a file
- A design philosophy of UNIX-like OS: **everything is a file**
   - Normal files, directory files
   - Character device files (like keyboard, mouse...)
   - Block device files (eg hard disk, CD-ROM...)
   - Network files (socket...) etc.
- Everything is abstracted into a file, providing a **unified interface** for applications.

---
#### File View

- **User's file view**
   - Persistent data structures
   - System call interface: collection of **byte sequences** (UNIX)
- **OS's file view**
   - A collection of **DataBlocks**
   - The data block is the storage unit at logical level, while the sector is the storage unit at physical level.

---

#### Internal structures of data in files

- Related to the application
   - Unstructured: text files
   - Simple structure: CSV, JSON and other formatted files
   - Complex structures: Word files, ELF executable files

---

**Outline**

1. Files
- Concept of a file
### File operations
- File descriptors
2. File Systems and File Organization

---

#### Basic operations on files

- **Read file** by the process
   - Get the data block where the file is located
   - Return the corresponding part in the data block
- **Write file** by the process
   - Get data block
   - Modify corresponding parts of the data block
   - Write the data block back

![bg right:45% 100%](figs/fs002.png)



---

#### Basic Operation Unit of Files

- The basic operation unit in a file system is **data block**
   - For example, getc() and putc() need to cache 4096 bytes of target data even if only accessing 1 byte of data each time

---
#### File access mode
- **Sequential access**: read data byte by byte
   - Map one-dimensional data to a file
- **Random Access**: read and write from any location
   - Map a complex structure (matrix) into a file
- **Index access**: Index based on data features
   - Database access is a type of indexed access.

---

#### File Access Control

- File sharing in a multi-user OS is essential
- Access control
   - User's access permissions to a file
   - Read, write, execute, delete
- File Access Control List (ACL-Access Control List)
   - <File entity, permissions>

---

#### File Access Control

- UNIX mode
   - <user|group|all, read|write|execute>
   - User ID
   - Group ID



---

#### Shared access to files

How can multiple processes access shared files simultaneously?
- A file is a shared resource
   - Need mutual exclusion access
   - Use similar synchronization and mutual exclusion techniques (later)
     - read-write lock

---

#### Shared access to files

UNIX File System (UFS) Semantics
- For the write content of an open file, it is immediately visible to other users who have opened the same file
- Shared file pointer allows multiple users to read and write the file simultaneously

---

**Outline**

1. Files
- Concept of a file
- File operations
### File descriptors
2. File Systems and File Organization

---

#### How do applications access files?
- Before the application can access the file data, it must first "open" the file to **obtain the file descriptor**
- Further **read and write** files through the file descriptor (fd)
```C
fd = open(name, flag);
read(fd, ...);
close(fd);
```

---
<style scoped>
{
  font-size: 30px
}
</style>
#### File descriptor

When an application program requests the kernel to open/create a new file, the kernel returns a file descriptor **corresponding to the opened/new file**.
- Formally, a file descriptor is a **non-negative integer**
- In fact, the file descriptor is an **index value** that points to the record table of the open files that the kernel maintains for each process.

![bg right:50% 90%](figs/file-descriptor.png)


---

#### Open file table

- The kernel **tracks** all files opened by the process
   - The OS maintains a table of open file descriptors for each process
   - A system-wide open file table
   - The i-node table points to the specific file content
![bg right:48% 90%](figs/file-descriptor.png)



---

#### Open file table

- The kernel **maintains** open file status and information in the open file table
   - **File Pointer**
      - The most recent read-write position
      - Each process maintains its own open file pointer

![bg right:48% 90%](figs/file-descriptor.png)

---
<style scoped>
{
  font-size: 32px
}
</style>
#### Open file table

- The kernel **maintains** the open file status and information in the open file table
   - **File Open Count**
      - The number of times the file is currently open.
      - When the last process closes the file, remove it from the open file table

![bg right:52% 90%](figs/file-descriptor.png)


---
<style scoped>
{
  font-size: 32px
}
</style>
#### Open file table

- The OS **maintains** the open file status and information in the open file table
   - **Disk location of the file**
      - Cache data access information
   - **Access Permission**
     - File access pattern information for each process
![bg right:50% 90%](figs/file-descriptor.png)

---

**Outline**

1. Files
### 2. File Systems and File Organization
- Functions of a file system
- Directory

---
#### Types of Filesystem
- Disk File Systems: FAT, NTFS, ext2/3, ISO9660, etc.
- Network/Distributed file systems: NFS, SMB, AFS, GFS
- Special File Systems: procfs, sysfs

---

#### Virtual File System (VFS)

![w:650](figs/vfs-interface.png)

---

#### Functions of File Systems
- File systems are subsystems in OS that **manage persistent data**, providing functions for **naming, storing, and retrieving** data files.
   - Organize, retrieve, read and write data
   - Most computer systems have file systems

---
#### Functions of File Systems
- Allocate disk space for files
   - Manage file blocks (location and order)
   - Manage free space (location)
   - Allocation algorithm (strategy)

---
#### Functions of File Systems
- Manage file collections
   - Organization: organize the control structure and data structure of files
   - Naming: Give files names
   - Positioning: locate files and their contents by name

---
#### Functions of File Systems
- Data reliability and security
   - Security: multi-level protection for data security
   - Reliability:
     - Persistently store files
     - Avoid system crashes, data loss, etc.
 
---
#### Forms of File System Organization
**Hierarchical file system**
- Files are organized in directories
- Directories are a special type of files
- The content of the directory are the file index table <filename, pointer to file>

![bg right 100%](figs/fs-tree.png)

 
---

**Outline**

1. Files
2. File Systems and File Organization
- Functions of a file system
### Directory

---

#### Directory Operations

Applications manipulate directories through system calls, such as:
- Search files
- Create files
- Delete file 
- list directory
- Rename file

![bg right 90%](figs/fs-tree.png)

 
---
#### Directory Implementation
- Linear list of file names, with pointers to the data blocks
   - Simple to program, but can be slow to execute
- Hash Table – a linear list of file names using a hash data structure
   - Reduces directory search time
   - Can have collisions - two file names with the same hash value

---
#### Traversing directory paths
Example: parse `./fs/inode.rs`
- Read the data in the current directory file `.`
- Find the `fs` item and read the data in the directory file `fs`
- Find the `inode.rs` item and read the data in the general file `inode.rs`

![bg right:48% 90%](figs/fs-tree.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### File Aliases
Multiple file names can refer to the same file
- **Hard link**
   - multiple file entries point to the same file
- **Soft link**(symbolic link)
   - Point to another file by storing its filename

Inode: Data structure that manages file data

![bg right:51% 100%](figs/hardlink-softlink.jpeg)


---
#### How to Avoid Creating Loops in a Directory?
   - Only links to files are allowed, links to subdirectories are not allowed
   - Use a loop detection algorithm when creating links
   - Limit the number of directory path traversals

  ![w:800](figs/softlink-limits.png)

  ---
#### Mounting a File System
   - A file system must be mounted before it can be accessed.
  ![w:800](figs/mountfs.jpg)
 
  ---

### Summary

1. Files
- Concept of a file
- File operations
- File descriptors
2. File Systems and File Organization
- Functions of a file system
- Directory