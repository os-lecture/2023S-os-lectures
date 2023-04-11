---
noteId: "db866d30d86511edb7d82f06d610f713"
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

## Section 3 File System Supporting Crash Consistency


<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

Spring 2023

---

**Outline**

### 1. Crash-consistency problem
- Crash consistency
- Crash scenario
2. File system checker fsck
3. Journaling file system

---

#### Challenge of persistent data update in file system

How to update persistent data structures in the event of a power loss or system crash

Crashes can cause **inconsistencies** in the filesystem data structures in the disk file system image. For example, there may be space leaks or garbage data returned to users

---

#### Crash-consistency problem

The **crash-consistency** problem, also known as the **consistent-update** problem
- Specific operation needs to update **two structures** A and B on the disk
- As the disk can only serve one request at a time, one of the requests will be served first (A or B)
- If a system crash or power loss occurs after one write is completed, the structures on the disk will be in an inconsistent state
---

#### Requirements for crash consistency


* Goal
   * To transition the file system from one consistent state (before the file is appended) to another consistent state (after the inode, bitmap, and new data block are written to the disk) atomically
* Challenge
   * The disk can only commit one write at a time, and a crash or power loss may occur between updates
---

#### Example of file update process

An application updates the disk structure in some way: by appending a single data block to an existing file
- By opening the file, calling `lseek()` to move the file offset to the end of the file, and issuing a single 4KB write to the file before closing it to complete the append

---

#### File system data structure

* Inode bitmap (8 bits, one for each inode)
* Data bitmap (also 8 bits, one for each data block)
* Inodes (total of 8, numbered 0 to 7, distributed over 4 blocks)
* Data block (total of 8, numbered 0 to 7)
<!--
Schematic diagram of the file system
-->
![w:1100](figs/crash-ex.jpg)



---
<style scoped>
{
  font-size: 30px
}
</style>
#### Disk operations in file updates

An application updates the disk structure in some way: by appending a single data block to the existing file
- **Three separate writes** must be performed to the disk
   - inode (I[v2]), bitmap (B[v2]) and data block (Db)
- When a write() system call is issued, these write operations usually do not happen immediately
   - The dirty inode, bitmap, and new data first exist in **memory** (page cache or buffer cache) for a period of time
- When the file system eventually decides to write them to the disk (such as 5s or 30s), the file system issues the necessary **write requests** to the disk

<!--![w:900](figs/crash-ex.png)-->



---

**Outline**

1. Crash-consistency problem
- crash consistency
### - Crash scenario
2. File system checker fsck
3. Journaling file system

---

#### Crashes in file operations
A crash may occur during file operations, disrupting these updates to the disk
* If a crash occurs after one or two of the write operations are completed, instead of all three, then the file system may be in an **interesting** (inconsistent) state
<!-- ![w:900](figs/crash-ex.png) -->
![w:1100](figs/crash-ex-normal.jpg)

---

#### Crash scenario 1

Only the data block (Db) is written to the disk
* **The data is on disk**, but there is no inode pointing to it, nor is there a bitmap indicating that the block has been allocated
* **It appears as if the write never happened**

<!-- ![w:900](figs/crash-ex.png) -->
![w:1100](figs/crash-ex-normal.jpg)

---

#### Crash scenario 2

Only the **updated inode** (I[v2]) is written to the disk
* The inode points to disk block 5, where Db is about to be written, but Db has not yet been written
* **Garbage data is read from the disk** (old content of disk block 5)

<!-- ![w:900](figs/crash-ex.png) -->
![w:1100](figs/crash-ex-normal.jpg)



---

#### Crash scenario 3

Only the **updated bitmap** (B[v2]) is written to the disk
* The bitmap indicates that block 5 has been allocated, but there is no inode pointing to it
* This type of write will result in a **space leak**, as the file system will never use block 5

<!-- ![w:900](figs/crash-ex.png) -->
![w:1100](figs/crash-ex-normal.jpg)


---

#### Crash scenario 4

The **inode(I[v2]) and bitmap(B[v2]) are written to disk**, but the data(Db) is not
* The inode has a pointer to block 5, and the bitmap indicates that block 5 is in use, so everything **appears normal** from the file system's metadata perspective.
* However, **the disk block 5 contains garbage**

<!-- ![w:900](figs/crash-ex.png) -->
![w:1100](figs/crash-ex-normal.jpg)

---

#### Crash scenario 5

The **inode (I[v2]) and data block (Db)** are written, but the bitmap (B[v2]) is not
* The inode points to the correct data on disk
* There is a **discrepancy** between the inode and the old version of the bitmap (B1)

<!-- ![w:900](figs/crash-ex.png) -->
![w:1100](figs/crash-ex-normal.jpg)

---

#### Crash scenario 6

The **bitmap (B[v2]) and data block (Db)** are written, but the inode (I[v2]) is not
* There is a **discrepancy** between the inode and the data bitmap
* It is not known which file the block belongs to, as there is no inode pointing to it

![w:1100](figs/crash-ex.png)

---

**Outline**

1. Crash-consistency problem
### 2. File system checker fsck
3. Journaling file system

---

#### Crash solution

* The file system checker fsck
* File system based on write ahead log

---

#### File system checker fsck

Early file systems used a simple approach to handle crash consistency
* Let inconsistent things happen and then fix them (upon restart)
* Goal: ensure **internal consistency of the file system metadata**


---
<style scoped>
{
  font-size: 27px
}
</style>
#### Superblock check

Check if the superblock is reasonable, mainly conducting an integrity check
* Ensure that the file system size is greater than the number of allocated blocks
* If the contents of the superblock are found to be **inconsistent (conflicting)**, the system (or administrator) can decide to use the **alternate copy** of the superblock

Note: For file systems with high reliability, there will be multiple disk sectors where superblock backups are placed

![bg right:45% 100%](figs/backup-superblock.png)

---

#### Consistency check between bitmap and inode

Scans inodes, indirect blocks, doubly indirect blocks, etc. to understand the blocks currently allocated in the file system and generate a correct version of the allocation bitmap
* If there is any inconsistency between the bitmap and the inode, the issue is resolved by trusting the information inside the inode
* The same type of check is performed for all inodes to ensure that all inodes that appear to be in use are marked in the inode bitmap


---

#### Inode status check

Check if each inode is damaged or has other issues
* Each allocated inode has a valid type field (i.e. regular file, directory, symbolic link, etc.)
* If there is an issue with the inode field that is not easy to fix, the inode is considered suspicious and is cleared by fsck, and the inode bitmap is updated accordingly

---

#### Link count check

The inode link count indicates the number of different directories containing references (i.e. links) to this particular file
* Starting from the root directory, scan the entire directory tree and build its own link count for each file and directory in the file system
* If the newly calculated count does not match the count found in the inode, the count in the inode is usually repaired
* If an allocated inode is found without a directory referencing it, it is moved to the lost+found directory

---

#### Duplicate pointer check

The case where two different inodes reference the same block
* If one inode is obviously wrong, the block it points to may be cleared or duplicated, providing each inode with its own file data
* There are many possibilities for inode errors, such as inconsistent metadata within the inode
   * The inode has a length record for the file, but the actual size of the data block it points to is smaller than the file length

---

#### Bad block check

Check for bad block pointers while scanning all pointer lists. If a pointer points obviously beyond its valid range, the pointer is considered "bad"
* Address pointing to a block larger than the partition size
* Delete (clear) the pointer from the inode or indirect block

---

#### Directory check

Fsck does not know the content of user files, but directories contain information of a specific format created by the file system itself. Additional integrity checks are performed on the contents of each directory
   - Ensure that "." and ".." are the first entries and that every inode referenced in directory entries is allocated
   - Ensure that no directory is referenced more than once in the entire hierarchy

---

#### Shortcomings of the file system checker fsck

- For very large disk volumes, scanning the entire disk to find all allocated blocks and reading the entire directory tree may take several minutes or hours.
- Data loss may occur!

---

**Outline**

1. Crash-consistency problem
2. File system checker fsck
### 3. Journaling file system
- Logging
- Data journaling
- Performance optimization of journaling file system

---

#### Logging (or write-ahead logging)

Write-ahead logging
- Ideas borrowed from the world of database management systems
- In file systems, for historical reasons, write-ahead logging is usually referred to as journaling
- The first filesystem to implement it was [Cedar](https://www.microsoft.com/en-us/research/publication/the-cedar-file-system/)
- Many modern file systems use this idea, including Linux ext3 and ext4, reiserfs, IBM's JFS, SGI's XFS, and Windows NTFS

---

#### Idea of write-ahead logging

* When updating the disk, before overwriting the structure, write a small note (in another location on the disk, in a well-known location) describing what you are about to do
* Writing this note is the "write-ahead" part, putting it into a structure and organizing it into a "log"

---

#### Crash recovery with write-ahead logging

- By writing the note to the disk, you can ensure that if a crash occurs while the structure being updated is being overwritten, you can return and look at the note you made and try again
- Know exactly what needs to be fixed (and how to fix it) after a crash without having to scan the entire disk
- The log function greatly reduces the amount of work required during recovery by adding some extra work during updates

---

**Outline**

1. Crash-consistency problem
2. File system checker fsck
3. Journaling file system
- Logging
### Data journaling
- Performance optimization of journaling file system

---

#### Data journaling

![w:900](figs/ext3-journal-struct.png)
- TxB: transaction begins
- TxE: transaction ends
- Logical logging: intermediate 3 blocks of data
---


#### Data journaling

![w:900](figs/ext3-journal-struct.png)
- Data journal written to disk
- Update the disk, cover the relevant structure (checkpoint)
   - I[V2] B[v2] Db

---

#### A crash occurs while writing to the log

Internally on the disk, TxB, I[v2], B[v2], and TxE are written before Db
* If the disk crashes between (1) and (2), the disk will be like:

![w:1000](figs/ext3-journal-struct-err.png)

---

#### Two-step transaction write of data log

To avoid this problem, the file system issues transaction writes in two steps
- Write all blocks except TxE block to the log and issue these writes
- When these writes are complete, the log will look like this (assuming it is a file append workload):
![w:1000](figs/ext3-journal-write.png)

---

#### Two-step transaction write of data log

When these writes are complete, the file system issues a write of the TxE block, putting the log in its final, secure state:
![w:900](figs/ext3-journal-commit.png)

---
<style scoped>
{
  font-size: 30px
}
</style>
#### Data log update process

The current protocol for updating the file system is as follows, with each of the three stages labeled
1. **Log write**
    - Write the contents of the transaction (including TxB, metadata, and data) to the log and wait for these writes to complete
2. **Log commit**
    - Write the transaction commit block (including TxE) to the log and wait for the write to complete, and the transaction is considered committed
3. **Add Checkpoint**
    - Write the updated contents (metadata and data) to their final disk location

---

#### Crash recovery with data log

A crash can occur at any time during this update sequence

- If the crash occurs before the transaction is safely written to the log
- If the crash occurs after the transaction has been committed to the log but before the checkpoint is complete

Too many writes, slow!

---

**Outline**

1. Crash-consistency problem
2. File system checker fsck
3. Journaling file system
- Logging
- Data journaling
### Performance optimization for journaling file systems

---

#### Journal superblock

- Batched journal updates
- Limiting the log: circular log

![w:900](figs/ext3-journal-batch.png)
![w:900](figs/ext3-journal-superblock.png)

---

#### Update process of journal superblock
- Journal write
- Journal commit
- Checkpoint
- Free: After a period of time, the superblock marks the transaction record as free by updating the journal


---

#### Metadata journaling

When should data block Db be written to disk?
   - The order of data writes is important for metadata journaling only
   - Is there a problem with writing the Db to disk after the transaction (including I [v2] and B [v2]) is completed?

---

#### Update process of metadata journaling 
- Data write
- Journal metadata write
- Journal commit
- Checkpoint metadata
- Free

By forcing data to be written first, the file system can ensure that pointers never point to garbage data

---

#### Data journaling timeline v.s. metadata journaling timeline
![bg 90%](figs/ext3-journal-data-timeline.png)


![bg 90%](figs/ext3-journal-metadata-timeline.png)

---
<style scoped>
{
  font-size: 32px
}
</style>
### Summary

1. Crash-consistency problem
- Crash consistency
- Crash scenario
2. File system checker fsck
3. Journaling file system
- Logging
- Data journaling
- Performance optimization of journaling file system