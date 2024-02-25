---
noteId: "da9f8fa0d86511edb7d82f06d610f713"
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

## Lecture 1 Operating System Overview
### Section 1 Course Overview & Teaching Arrangement

<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

<br>
<br>

Spring 2024

---

## Course Information

### Lecturers:
  - Yong Xiang, Yu Chen, Guoliang Li, Ju Ren


### Teaching Assistant
  - Congyuan Rao, Kaifu Tian, Haoyang Bi, Zixu Hao, Tuowei Wang, Mingxian Su, Wang Huang

---

## Class Information

### Class time and place
- Monday 9:50am-12:15pm (1-16 weeks)
- Place
     - 六教 6A209

----

## About Me

- E-mail: renju@tsinghua.edu.cn
- Homepage: https://juren1987.github.io
- Research Interests: Internet-of-Things, Edge Computing & Edge Intelligence, Operating System

- First author of 《openEuler操作系统》（第1版&第2版）
----
## Preliminary knowledge

### Programming Languages (Assembly, C, and Rust)
  - :( not develop applications
  - :) develop system programs

### data structure
  - :) Just understand the basic data structure

---

## Preliminary knowledge
### Principles of computer composition
  - :( Prof. Liu/Prof. Chen's RISC-V principle
  - :) Patterson's RISC-V Principles

### Compilation principle
  - :) It doesn't matter if you haven't learned it
  - :( But need to be familiar with high-level language <–>RISC-V assembly language


---
<style scoped>
{
  font-size: 30px
}
</style>
#### Course Reference
- [Course Slides](https://www.yuque.com/docs/share/4c39608f-3051-4445-96ca-f3c018cb96c7)
- Reference books
   * [Operating Systems: Three Easy Pieces](https://pages.cs.wisc.edu/~remzi/OSTEP/)
   - [In-depth understanding of computer systems](https://hansimov.gitbook.io/csapp/)
   - [RISC-V Reader (Chinese version)](http://riscvbook.com/chinese/RISC-V-Reader-Chinese-v2p1.pdf)
#### Course Practice: rCore Tutorial Book v3
- [Course Practice Reference Book](https://learningos.github.io/rCore-Tutorial-Book-v3/)
- [Course Practice Code Warehouse](https://github.com/rcore-os/rCore-Tutorial-v3)
- [API document for course practice code](https://github.com/rcore-os/rCore-Tutorial-v3#os-api-docs)

---

### Experiment Guide


#### uCore-RV-64

* [uCore Code](https://github.com/LearningOS/uCore-Tutorial-Code-2023S/)
* [Document - uCore Tutorial](https://learningos.github.io/uCore-Tutorial-Guide-2023S/)

* [uCore Test Cases](https://github.com/LearningOS/uCore-Tutorial-Test-2023S)


---

### Experiment Guide

#### rCore
- [Document - rCore Tutorial](https://learningos.github.io/rCore-Tutorial-Guide-2023S/)
- [rCore API](https://github.com/LearningOS/rCore-Tutorial-Guide-2023S/#os-api-docs-of-rcore-tutorial-code-2022s)
- [rCore Code](https://github.com/LearningOS/rCore-Tutorial-Code-2023S)
- [Test Cases](https://github.com/LearningOS/rCore-Tutorial-Test-2023S)

#### [uCore and rCore experiment introduction videos](https://www.yuque.com/xyong-9fuoz/qczol5/axrf11?)
-Keep updating

---

<style scoped>
{
  font-size: 32px
}
</style>
![bg right 100%](figs/ucorearch.png)


### OS principles and design ideas

* Operating system structure
* Interrupts and system calls
* Memory management
* Process management
* Processor scheduling
* Synchronization & Mutex
* File system
* I/O subsystem


---
<style scoped>
{
  font-size: 32px
}
</style>
## Experiments and Project
### Assignments
- Homework: Some Questions and Programs
### Basic Experiments
- 5 Required Experiments (Required): Design and implement operating system functions in Rust/C for RISC-V CPU
- 1 Additional Experiment (Optional): More difficult
 
### Course Project (Optional)
   - A challenging project

---
<style scoped>
{
  font-size: 30px
}
</style>

## Basic experiments
### Experiment 1: Basic support of the operating system
### Experiment 2: Address space
### Experiment 3: Process management and scheduling
### Experiment 4: File system and inter-process communication
### Experiment 5: Synchronization & Mutex


---
<style scoped>
{
  font-size: 30px
}
</style>
## Course Project

### Develop various new OS functions and extensions, e.g.,

- OS porting on various CPU platforms
   * RISC-V, x86-64, MIPS, ARM
- Developing drivers for various development boards
   * RaspBerry PI, U740, D1, etc.
   * GUI, driver, file system, network
- Develop new or improve OS kernel modules
   * Loadable kernel modules, microkernels
   * Support asynchronous programming in the kernel

---
<style scoped>
{
  font-size: 30px
}
</style>
## Grades

### Choice 1:
   - Complete the basic experiments (5 basic experiments + additional experiments) on time: 40%
   - Midterm Exam 20% + Final Exam 40%
   - Bouns: Homework (每做一次平时作业加1分，最高加5分，总分不超过100分)
### Choice 2:
   - Complete the basic experiments in 4 weeks: 40%
   - Course Design: 60%
     - Note: Students need to take the exam if they quit the course design.

---
<style scoped>
{
  font-size: 30px
}
</style>
## Survey

[Questions for 2024 Spring OS Course](https://www.wjx.cn/vm/es1fD1R.aspx)

- Why do you want to take this course?
- How are you going to take this course?
- What do you expect to learn from this course?
- Are you willing to truthfully report whether you completed the experimental tasks independently?
- What knowledge and ability do you hope to acquire in this course?
- Previous studies?
