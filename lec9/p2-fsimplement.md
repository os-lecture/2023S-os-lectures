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

## Section 2 Design and Implementation of File System


<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

Spring 2023

---

**Outline**

### 1. Overview
2. Basic data structures of file system
3. File cache
4. File allocation
5. Example of file access process

---
#### The position of file system in kernel
![w:850](figs/ucorearch.png)

---

#### The layered structure of file system

![w:950](figs/fsarch.png)

---
#### The layered structure of file system in computer system

![w:700](figs/fsarchdetail.png)

---

#### The user view and kernel view of file system

![w:450](figs/fslayer.png)

![bg right:55% 95%](figs/fslayout.png)


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Virtual file system (VFS)


- Defines a set of standard data structures and interfaces that are supported by all file systems
- Disk-based file systems: store data directly on the disk, such as Ext2/3/4, XFS
- Memory-based file systems: memory-aided data structures, such as directory entries
![bg right 50% 80%](figs/fsarchi.png)


---
#### The function of virtual file system 
- Purpose: abstracts all different file systems
- Function
   - Provides the same file and file system interface
   - Manages all data structures associated with files and file systems
   - Efficient query routines, traverse file systems
   - Interacts with specific file system modules


---
#### The Virtual File System unifies the access interface of different file systems
![w:700](figs/vfs-app.png)


---

**Outline**

1. Overview
### 2. Basic data structures of file system
3. File cache
4. File allocation
5. Example of file access process

---

#### The storage view of file system
- File volume control block (`superblock`)
- File control block ( `inode`/`vnode`)
- Directory entry (`dir_entry`)
- Data block (`data block`)
![bg right:45% 100%](figs/fsdisk.png)

---

#### The organizational view of file system

![bg 90%](figs/fsorg.png)
![bg 90%](figs/fsall.png)


---

#### Basic data structure of file system
![w:700](figs/fsoverall.png)


---
<style scoped>
{
  font-size: 30px
}
</style>
#### File volume control block (`superblock`)

One file volume control block for each file system
- Detailed information about the file system
- Block size, number of free blocks, etc
- Total number of blocks and inodes, number of unused and used ones
- File system mount time, last write time, and last disk check (fsck) time
![bg right:45% 100%](figs/efs-superblock.png)


---
<style scoped>
{
  font-size: 30px
}
</style>
#### File control block inode
Each file has an inode (`inode`/`vnode`)
   - Size, data block position (points to one or more data blocks)
   - Access mode (read/write/excute)
   - Owner and group (owner/group)
   - Time information: time of creation or status change, time of last read/modified
   - **File name in the directory's data block**
<!--![bg right 10% 50%](figs/efs-inode.png)-->

![bg right:46% 90%](figs/efs-inode.png)



---
#### Bitmap block
Bitmap block ( `bitmap inode/dnode`)
- Flags indicating whether inode is used or unused
- Flags indicating whether dnode is used or unused

---
#### Data block dnode( `data node`)
- Data blocks for directories and files
     - Contains directory and file contents
     - Fixed size of data block determined during formatting
     - Each block is numbered for inode reference
     - Inode is generally 128B
     - Data block is generally 4KB

---
#### Data block of directory
![w:850](figs/fsdir.png)



---
<style scoped>
{
  font-size: 30px
}
</style>
#### Directory entry (`dir_entry`)
- Directory entries are generally cached in memory
   - Each directory entry represents a directory or file
   - Directory entry data structure and tree layout are encoded into a tree data structure
   - Points to the file control block, parent directory, child directory, etc.
![bg right 100%](figs/efs-direntry.png)

![bg right 100%](figs/fslayout.png)

---

**Outline**

1. Overview
2. Basic data structure of file system
### 3. File cache
4. File allocation
5. Example of file access process

---

#### Multiple disk cache locations
![w:1200](figs/diskcache.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Data block cache
- Data blocks **are read into memory as needed** 
   - Provides read() operation
   - Prefetch: Pre-read the next data block
- Data blocks are **cached** after use
   - Assumes data will be used again
   - Write operations may be cached and delayed writes
 
  Page cache: cache data blocks and memory pages uniformly

![bg right:45% 90%](figs/datacache.png)

---
#### Virtual page storage -- page cache

Virtual pages in the virtual address space can be mapped to local external storage files
  
![w:700](figs/pagecache.png)

<!--
---
#### Design and implementation of the file system -- cache
Virtual page storage -- page cache
- Virtual pages in the virtual address space can be mapped to local external memory files
- Page cache for file data blocks
   - File data blocks are mapped into pages in virtual memory
   - File read/write operations are converted to memory accesses
   - May result in page faults and/or dirty pages
   - Problem: The page replacement algorithm needs to coordinate the number of pages between the virtual storage and the page cache
-->


---
#### Virtual page storage -- page cache

Virtual pages in the virtual address space can be mapped to local external storage files
- Page cache for file data blocks
   - File data blocks are mapped into pages in virtual memory
   - File read/write operations are converted into accesses to memory
   - May result in page faults and/or dirty pages
- Issue: Page replacement algorithms need to coordinate the number of pages between virtual memory and page cache


---
#### File descriptor
- Each opened file has a file descriptor
- As an index, it points to corresponding file status information

![w:750](figs/fd-openfiletable.png)



---
#### Open file table
- Each process has an open file table
- A system-wide open file table

![w:750](figs/fd-openfiletable.png)


---
#### File lock
Some file systems provide file locks to coordinate file access by multiple processes
- Mandatory – Access is denied based on lock hold status and access requirements
- Advisory - Processes can look up the lock status to decide what to do

---

**Outline**

1. Overview
2. Basic data structure of file system
3. File cache
### 4. File allocation
5. Example of file access process

---

#### File size
- Most files are small
   - Need to support small files
   - Data block space cannot be too large
- Some files are very large
   - Can support large files
   - Can read and write efficiently
![bg right:50% 90%](figs/fd-openfiletable.png)


---
<style scoped>
{
  font-size: 30px
}
</style>
#### File allocation

Allocate file data blocks
- Allocation methods
    - Continuous allocation
    - Linked allocation
    - Indexed assignment
- Evaluation criteria
   - Storage efficiency: external fragmentation, etc
   - Read/write performance: access speed

![bg right:50% 90%](figs/fd-openfiletable.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Continuous allocation
The file header specifies the starting block and length

![w:900](figs/continuealloc.png)

- Allocation strategy: first fit, best fit, etc
- Pros: 
   - Efficient sequential and random read access
- Cons
   - Frequent allocation leads to fragmentation; large file content overhead



---
#### Linked allocation
Data blocks are stored in a linked list

![w:800](figs/linkalloc.png)

- Pros: easy to create, enlarge, and shrink; almost no fragmentation
- Cons:
    - Low efficiency for random access; poor reliability
    - If a link is broken, the subsequent data blocks are lost

---
#### Linked allocation

   - Explicit connection
   - Implicit connection
![bg right:35% 80%](figs/fs-explicit.png)

![bg right:70% 100%](figs/fs-implicit.png)


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Indexed allocation

- The file header contains a pointer to the index data block
- The index in the index data block is the pointer to the file data block
![w:800](figs/indexalloc.png)
- Pros
   - Easy to create, enlarge, and shrink; almost no fragmentation; supports direct access
- Cons
   - When the file is small, the storage overhead of indexing is relatively large

How to handle with large files?


---
#### Indexed allocation

- Linked index block (IB+IB+…)
![w:800](figs/linkindex.png)
- Multi-level index block (IB*IB *…)
![w:800](figs/multiindex.png)


---
#### Indexed allocation

![w:1000](figs/fsindex.png)


---
#### Multi-level index allocation

![w:800](figs/ufsinode.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Multi-level index allocation

- The file header contains 13 pointers
   - 10 pointers point to data blocks
   - The 11th pointer points to an index block
   - The 12th pointer points to a second-level index block
   - The 13th pointer points to a third-level index block

Accessing data blocks in large files requires a large number of queries


![bg right:43% 100%](figs/ufsinode.png)


---
#### Comparison of file allocation methods
![w:1150](figs/filespace.jpg)



---
<style scoped>
{
  font-size: 32px
}
</style>
#### Free space management
Track and record unallocated data blocks in file volumes: data structure?
- Bitmap: Representing the list of free data blocks using a bitmap
   - 11111111001110101011101111...
   - $D_i = 0$ indicates that data block $i$ is free, otherwise, it is allocated
   - 160GB disk --> 40M data block --> 5MB bitmap
   - Assuming free space is evenly distributed on the disk,
       - n/r data blocks need to be scanned before finding a "0"
         - n = the total number of data blocks on the disk; r = the number of free blocks

---
#### Free space management
- Linked list
![w:800](figs/link.png)
- Index
![w:900](figs/linkindex4free.png)
 


---

**Outline**

1. Overview
2. Basic data structure of file system
3. File cache
4. File allocation
### 5. Example of file access process

---

#### Example of file system organization
![w:850](figs/fsall.png)

---
#### File reading process
![w:650](figs/fsread.jpg)

---
#### File writing process
![w:800](figs/fswrite.jpg)



---
#### File system partition
- Most disks are divided into one or more partitions, each with an independent file system
![w:600](figs/fs-oall.png)

![bg right:40% 80%](figs/fs-block.jpg)

---

### Summary

1. Overview
2. Basic data structure of file system
3. File cache
4. File allocation
5. Example of file access process