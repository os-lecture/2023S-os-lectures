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

## Lecture 1 Operating System Overview

### Section 5 Practice: Try UNIX/Linux

<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

<br>
<br>

Spring 2023

---
## Where is UNIX/Linux?

- Linux
    - Ubuntu, Fedora, SuSE, openEuler
    - Kylin OS(麒麟), Unity OS(统信)
- Windows with WSL (Windows Subsystem of Linux)
- MacOS with UNIX shell
---
## Why Linux?
- Open source, sufficient documents, concise design, widely used
- Learning ucore/rcore will be helpful if you aim to know the design details of Linux.

---
## Try Linux

- shell
    - bash, fish, zsh, starship...

- program
    - ls, rm, gcc, gdb, vim...

---
## What services does the Linux kernel usually provide?

   * Process (a running program)
   * Memory allocation
   * Document content
   * File name, directory
   * Access Control (Security)
   * Many others: user, IPC, network, time, endpoint


---
## The application/kernel interface provided by the Linux kernel?

   * "System call"
   * Examples, in C, from UNIX (e.g. Linux, macOS, FreeBSD).

             fd = open("out", 1);
             write(fd, "hello\n", 6);
             pid = fork()

  * These look like function calls, but they are not
  * The number of core system calls is not large (about 20)

---
## The application/kernel interface provided by the Linux kernel?

<style scoped>
table {
  font-size: 25px;
}
</style>

| System call name | Meaning |
| ------------------------ | ---- |
| ``int fork()`` | Create a process and return the PID of the child process. |
| ``int exit(int status)`` | Terminate the current process; report the status to the parent process that executes the wait() system call. Nothing is returned. |
| ``int wait(int *status)`` | Wait for the child process to exit; the exit status is ``*status``; return the PID of the child process. |
| ``int kill (int pid)`` | Terminate the process whose process ID is PID. Returns 0 for success, or -1 for error. |
| ``int getpid()`` | Return the PID of the current process. |

---
## The application/kernel interface provided by the Linux kernel?

<style scoped>
table {
  font-size: 28px;
}
</style>

| System call name | Meaning |
| ------------------------ | ---- |
| ``int sleep(int n)`` | Pause for n clock cycles. |
| ``int exec(char *file, char *argv[])`` | Load file with arguments and execute; return only when facing errors. |
| ``char *sbrk(int n)`` | Increase the process memory by n bytes, return the start address of the new memory. |
| ``int open(char *file, int flags)`` | Open the file; the flag indicates the read/write attribute of the file operation; return an fd (file descriptor). |
| ``int write(int fd, char *buf, int n)`` | Write n bytes from buf to file descriptor fd; return n. |

---
## The application/kernel interface provided by the Linux kernel?

<style scoped>
table {
  font-size: 28px;
}
</style>

| System call name | Meaning |
| ------------------------ | ---- |
| ``int read(int fd, char *buf, int n)`` | Read n bytes into buf; return the number that has been read, or 0 if end-of-file. |
| ``int close(int fd)`` | Release the opened file descriptor with fd. |
| ``int dup(int fd)`` | Return a new file descriptor referencing the same file as the file descriptor. |
| ``int pipe(int p[])`` | Creates a pipe with read/write file descriptors in p[0] and p[1]. |
| ``int chdir(char *dir)`` | Change the current directory. |

---
## The application/kernel interface provided by the Linux kernel?

<style scoped>
table {
  font-size: 28px;
}
</style>

| System call name | Meaning |
| ------------------------ | ---- |
| ``int mkdir(char *dir) `` | Create a new directory. |
| ``int mknod(char *file, int, int)`` | Create a device file. |
| ``int fstat(int fd, struct stat *st)`` | put the meta information of file fd into ``*st`` |
| ``int stat(char *file, struct stat *st)`` | Put the meta information of the file ``*file`` into ``*st`` |
| ``int link(char *file1, char *file2)`` | Create another name (file2) for the file file1. |
| ``int unlink(char *file)`` | Delete a file. |


---
## Some UNIX/Linux applications

[Some simple programs](https://pdos.csail.mit.edu/6.828/2021/lec/l-overview/)

#### Process related

fork.c exec.c forkexec.c ...
#### File system related
list.c open.c echo.c copy.c ...
#### Inter-process communication related
  pipe1.c pipe2.c redirect.c ...

---
## Some UNIX/Linux applications
  Example: [copy.c](https://pdos.csail.mit.edu/6.828/2021/lec/l-overview/copy.c), copy input to output
It reads bytes from input, and writes them to output

         $ copy

   copy.c is written in C language
    
   read() and write() are system calls
   The first parameter of read()/write() is "file descriptor" (fd), which is passed to the kernel and indicates which "open file" to read/write.

---
## Some UNIX/Linux applications

<style scoped>
{
  font-size: 30px
}
</style>

An opened FD (descriptor) should be connected to a file/device/socket.
A process can have many opened files with many descriptors. 

In UNIX, FD: 0 is usually "standard input", and 1 is "standard output"


The second parameter of read() is a pointer to some memory to be read.

The third parameter of read() is the maximum number of bytes to read.
Note: read() can read less, but not more


---
## Some UNIX/Linux applications

Return value: the actual number of bytes read, or -1 for an error.
Note: copy.c doesn't care about the format of the data.

UNIX I/O is 8-bit bytes.
Interpretation is application specific, e.g. database records, C source code, etc.

Where do file descriptors come from?


---
## Some UNIX/Linux applications

Example: [open.c](https://pdos.csail.mit.edu/6.828/2021/lec/l-overview/open.c), create a file

     $ open
     $ cat output.txt

open() creates a file, and returns a file descriptor (or -1 for errors).
FD is a small integer, and indexed into a table maintained by the kernel for each process.

Different processes have different FD namespaces. For example, FD 1 has different meanings for different processes. Further details can be found in UNIX manuals.

<!--eg "man 2 open".
man 1 is a shell command such as ls; man 2 is a system call such as open; man 3 is a function description-->

---
## Some UNIX/Linux applications

What happens when a program calls a system call like open()?

- Looks like a function call, but it's actually a special instruction
- The hardware saves some user registers
- Elevate the privilege level
- Jumps to a known "entry point" in the kernel
- Now run C code in the kernel


---
## Some UNIX/Linux applications

What happens when a program calls a system call like open()?

- Kernel calls the implementation of the system call 
- open() looks up the name in the file system
- it may wait for the disk
- Update the data structures of the kernel (e.g., cache, FD table)
- restore user registers
- Lower the privilege level
- Jump back to the call point in the program and continue running

---
## Some UNIX/Linux applications

Input information to the UNIX command line interface (shell).
The shell prints a "$".
The shell lets you run UNIX command-line tools.
Useful for system administration, working with files, developing and writing scripts.

     $ ls
     $ ls > out
     $ grep x < out

---
## Some UNIX/Linux applications

However, supporting time-sharing and multi-tasking execution through the shell is the design focus of UNIX at the beginning.
Many system calls can be made through the shell.

The shell creates a new process for each command you type, for example, for

     $ echo hello



---


## Some UNIX/Linux applications
The system call fork() creates a new process

     $ fork

The kernel creates a copy of the calling process
- Instructions, data, registers, file descriptors, current directory
- "parent" and "child" processes

---


## Some UNIX/Linux applications

The only difference: fork() returns the pid of child process in the parent process and 0 in the child process.

pid (process ID) is an integer, the kernel assigns a different pid for each process.

Therefore, the "fork() returns" of [fork.c](https://pdos.csail.mit.edu/6.828/2021/lec/l-overview/fork.c) in **two** processes can be used for distinguishing the parent and child processes by using "if(pid == 0)".

---
## Some UNIX/Linux applications

How can we run a new program in this process?

Example: [exec.c](https://pdos.csail.mit.edu/6.828/2021/lec/l-overview/exec.c), replace the calling process with an executable file.
How the shell runs a program? Such as

     $ echo a b c

A program is stored in a file: the instructions and initial memory, is created by the compiler and linker.
So there is a file called echo, containing the command of the `exec` system call.

---
## Some UNIX/Linux applications

exec() replaces the current process with an executable file
- Discard the instruction and data memory
- Load instructions and memory from the file
- File descriptors are preserved

---
## Some UNIX/Linux applications

exec(filename, argument-array)
argument-array holds command line arguments; exec passes them to the function main()

     cat user/echo.c

echo.c shows how a program handles its command line arguments

---
## Some UNIX/Linux applications

Example: [forkexec.c](https://pdos.csail.mit.edu/6.828/2021/lec/l-overview/forkexec.c), fork() a new process, exec() a program.

       $ forkexec

forkexec.c contains a common UNIX idiom.
- fork(), fork a child process
- exec(), execute a command in a child process
- The parent process waits for the child process to complete


---
## Some UNIX/Linux applications

- The shell forks/execs/waits every command you enter.
- After wait(), the shell will print out the next prompt
- Run in the background -- `&` -- , the shell will skip wait()


---
## Some UNIX/Linux applications

exit(status) --> wait(&status)

The value of status: 0 = success, 1 = facing errors on execution
Note: fork() makes a copy, but exec() discards the copied memory.

This may seem wasteful. You can transparently eliminate copying with "copy-on-write" techniques.


---
## Some UNIX/Linux applications

Example: [redirect.c](https://pdos.csail.mit.edu/6.828/2021/lec/l-overview/redirect.c), redirect the output of a command
What does the shell do about this?

     $ echo hello > out
 
Answer: fork, change the FD1 of the child process, execute echo

     $ redirect
     $ cat output.txt

---
<style scoped>
{
  font-size: 30px
}
</style>

## Some UNIX/Linux applications

Note: open() always chooses the lowest unused FD; the choice of 1 is due to close(1).

fork, FD and exec interact well for I/O redirection.

Independent fork-then-exec provides the child process a chance to change the FD before exec. FDs provide the destiniations.

Commands only need to use FDs 0 and 1 instead of knowing their acutual positions. exec preserves the FDs set by sh.

Therefore: only sh needs to know about I/O redirection, not every program.



---
## Some UNIX/Linux applications

Some questions:
- Why these I/O and process abstractions? Why not something else?
- Why provide a filesystem? Why not let programs use the disk in their own way?
-Why FDs? Why not pass a filename to write()?
- Why are files a stream of bytes, not disk blocks or formatted records?
- Why not combine fork() and exec()?

The design of UNIX works well, but we'll see other designs.

---
## Some UNIX/Linux applications

Example: [pipe1.c](https://pdos.csail.mit.edu/6.828/2021/lec/l-overview/pipe1.c), communicate through a pipe

     $ ls | grep x
     $pipe1

An FD can refer to either a "pipe" or a file.
The system call pipe() creates two FDs
- read from the first FD
- write to the second FD
  

---
## Some UNIX/Linux applications

The kernel maintains a buffer for each pipe
- write() adds data to the buffer
- read() waits until data appears

---
## Some UNIX/Linux applications

Example: [pipe2.c](https://pdos.csail.mit.edu/6.828/2021/lec/l-overview/pipe2.c), communicates between processes.
Pipe combines with fork() to do ls|grep x.
The shell creates a pipe and forks (twice). Then, it connects the FD1 of ls to the write FD of the pipe.
and grep's FD 0 is connected to the pipe.

    $ pipe2 -- a simplified version

Pipe is a separate abstraction, but work well with fork().


---
## Some UNIX/Linux applications


* Example: [list.c](https://pdos.csail.mit.edu/6.828/2021/lec/l-overview/list.c), list files in a directory
How does ls get a list of files in a directory?

- You can open a directory and read it -> filename
- "... " is a pseudonym for the current directory of a process.
See ls.c for more details

---
## Summary


   * We have studied UNIX's abstractions of I/O, file system and process.
   * These interfaces are very simple, only with integers and I/O buffers.
   * These abstractions combine well, for example, I/O redirection.