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
## Section 2 Disk Subsystem

<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

<br>
<br>

Spring 2023

---
### Disk -- Overview
Disk working mechanism and performance parameters
![w:700](figs/disk.png)


---
### Disk -- Overview
Disk I/O transfer time
![w:1000](figs/disk-time1.png)



---
### Disk -- Overview
Disk I/O transfer time
![w:1000](figs/disk-time2.png)


---
### Disk -- Overview
Disk I/O transfer time
![w:1000](figs/disk-time3.png)


---
### Disk -- Overview
Disk I/O transfer time
![w:1000](figs/disk-time4.png)


---
### Disk --Disk Scheduling Algorithm
Improve disk access performance by optimizing the disk access request order.
- Seek time is the most time-consuming part of disk access.
- Multiple I/O requests are present on the same disk simultaneously.
- Randomly processing disk access requests performs poorly.


---
### Disk --Disk Scheduling Algorithms - FIFO
![w:900](figs/disk-fifo.png)

---
### disk --disk scheduling algorithm -- FIFO
- First in first out (FIFO) algorithm
- Process requests in order.
- Fair treatment to all processes
- Performs close to random scheduling when many processes are present.



---
### Disk -- Disk Scheduling Algorithm -- Shortest Service Time First (SSTF)
- Selects the I/O request that requires the least movement from the current position of the disk arm.
- Always chooses the shortest seek time.
  ![w:550](figs/disk-sstf.png)


 
---
### Disk --Disk Scheduling Algorithms - SCAN
  ![w:750](figs/disk-scan.png)

---
### Disk --Disk Scheduling Algorithms - SCAN
- Disk arm moves in one direction, accessing all unfinished requests.
- Continues until the arm reaches the last track in that direction, then changes direction.
- Also known as the elevator algorithm.

---
### Disk --Disk Scheduling Algorithm -- Circular SCAN (C-SCAN)
- Limits scanning to only one direction.
- After accessing the last track, the arm returns to the other end of the disk to start the C-LOOK algorithm.
- The arm reaches the last request in that direction, then immediately reverses instead of servicing all requests on the path to the last point.



----
### Disk --Disk Scheduling Algorithm -- Circular SCAN (N-step-SCAN)
- Arm Stickiness phenomenon
    - In algorithms like SSTF, SCAN, and C-SCAN, the disk arm may remain stationary at a certain position.
- N-step scanning algorithm
   - Divides the disk request queue into sub-queues of length N.
   - Processes each sub-queue using the FIFO algorithm.
   - The scanning algorithm handles each queue.


----
### Disk --Disk Scheduling Algorithm -- Double Queue Scanning Algorithm (FSCAN)

FSCAN algorithm
- Divide disk I/O requests into two queues
- Alternately use the scanning algorithm to process a queue
- Newly generated disk I/O requests are placed in the other queue
- All new requests are deferred until the next scan

FSCAN is a simplification of the N-step scan algorithm

FSCAN only splits the disk request queue into two subqueues