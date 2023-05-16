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

# Lecture 12 Synchronization and mutual exclusion
## Section 1 Overview

<br>
<br>

Yong Xiang, Yu Chen, Guoliang Li, Ju Ren

<br>
<br>

Spring 2023

---
<style scoped>
{
  font-size: 28px
}
</style>

### Background
- Independent Process/Thread
   - Does not share resources or state with other processes/threads
   - Deterministic => the result is determined by the input state
   - Reproducible => able to reproduce initial conditions
   - Scheduling order is not important

- If processes/threads have resource sharing
   - Uncertainty exists
   - Irreproducibility exists
   - Difficult-to-reproduce errors may occur

![bg right:40% 90%](figs/newpidplus.png)

---
### Background
- Possible errors when forking processes/threads with resource sharing

![w:800](figs/newpidpluserr.png)


---
<style scoped>
{
  font-size: 26px
}
</style>
### Background -- Atomic Operation

- An atomic operation refers to an operation that occurs without any interruption or failure at once
   - It either successfully completes the operation
   - Or the operation is not executed at all
   - There is no partial execution state


The OS needs to utilize synchronization mechanisms to ensure that certain operations are atomic while executing concurrently
![bg right:50% 100%](figs/newpidpluscorrect.png)


---
### Synchronization and mutual exclusion in real life
Example: Coordinating family shopping (using real-life problems to understand operating system synchronization issues)

   - Note the differences between computers and humans
![w:800](figs/syncinlife.png)

---
<style scoped>
{
  font-size: 26px
}
</style>

### Synchronization and mutual exclusion in real life
- How to ensure the success and efficiency of coordinating family shopping
   - When there is a need for groceries, one person goes to buy bread
   - Only one person is allowed to go buy bread at most
- Possible solutions
   - Set a lock and key on the refrigerator
   - Lock the refrigerator and take the key before going to buy bread
- New problem caused by locking
   - When there are other food items in the refrigerator, others cannot access them
![bg right:30% 100%](figs/icebox.png)


---
<style scoped>
{
  font-size: 30px
}
</style>
### Synchronization and mutual exclusion in real life -- Solution 1
- Use notes to avoid buying too much bread
   - Leave a note before making the purchase
   - Remove the note after buying
   - When others see the note, they refrain from buying bread
```
  if (nobread) {
     if (noNote) {
         leave Note;
         buy bread;
         remove Note;
     }
}
```


---
<style scoped>
{
  font-size: 30px
}
</style>

### Synchronization and mutual exclusion in real life -- Solution 1 -- Analysis
- Occasionally, buying too much bread may still occur - duplication
   - Before posting the note, there may be others checking the bread and the note

- The solution intermittently fails
   - Debugging the problem is difficult
   - The actions of the scheduler must be taken into consideration

![bg right:40% 100%](figs/method-1.png)



---
<style scoped>
{
  font-size: 28px
}
</style>
### Synchronization and mutual exclusion in real life -- Solution 2
- Leaving the note first, then checking the bread and the note
```
leave Note;
if (nobread) {
   if (noNote) {
        buy bread;
     }
}
remove note;
```
- what happens?
    - No one will buy bread

![bg right:50% 100%](figs/method-2.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### Synchronization and mutual exclusion in real life -- Solution 3
- Adding a marker to the note to distinguish between different people's notes
    - Now it is possible to leave the note before checking
```
leave note_2;
if (no note_1) {
    if (no bread) {
      buy bread;
    }
}
remove note_2;
```

![bg right:40% 100%](figs/method-3.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### Synchronization and mutual exclusion in real life -- Solution 3
- Adding a marker to the note to distinguish between different people's notes
    - Now it is possible to leave the note before checking
```
leave note_1;
if (no note_2) {
    if (no bread) {
      buy bread;
    }
}
remove note_1;
```
![bg right:40% 100%](figs/method-3.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### Synchronization and mutual exclusion in real life -- Solution 3
- Adding a marker to the note to distinguish between different people's notes
    - Now it is possible to leave the note before checking

  - What will happen?
    - It may result in no one buying bread
    - Each person thinks the other will buy bread
 
![bg right:40% 100%](figs/method-3.png)



---

### Synchronization and mutual exclusion in real life -- Solution 4
Two people adopt different processing flows

![w:1000](figs/method-4.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### Synchronization and mutual exclusion in real life -- Solution 4
Two people adopt different processing flows
- Is it effective now?
   - It is effective, but too complex
- Code for person A and person B is different
   - What if there are more threads?
- When A is waiting, it cannot do anything else
   - Busy-waiting

![bg right:40% 100%](figs/method-4.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### Synchronization and mutual exclusion in real life -- Solution 5
- Implementing a lock using two atomic operations
   - Lock. Acquire()
      - Wait until the lock is released, then acquire the lock
      - If both threads are waiting for the same lock and they both find the lock released simultaneously, only one can acquire the lock
   - Lock. Release()
      - Unlock and wake up any waiting threads
![bg right:30% 100%](figs/method-5.png)

<!--
---
### Interaction between progress/thread: degree of mutual awareness
![w:900](figs/relations-1.png)



---
### Interaction between progress/thread: degree of mutual awareness
![w:900](figs/relations-2.png)


---
### Interaction between progress/thread: degree of mutual awareness
![w:900](figs/relations-3.png)
  -->


---
<style scoped>
{
  font-size: 30px
}
</style>

### Critical Section
```
entry section
    critical section
exit section
    remainder section
```
- Entry section
   - A section of code that checks if it is possible to enter the critical section
   - If it is possible to enter, set the corresponding "accessing critical section" flag
- Critical section
   - A section of code in a thread that accesses critical resources and needs to be executed mutually exclusively


---
### Critical Section
```
entry section
    critical section
exit section
    remainder section
```
- Exit section
    - Clear the "accessing critical section" flag
- Remainder section
    - The remaining part of the code

---
<style scoped>
{
  font-size: 30px
}
</style>

### Critical Section -- Access Rules
```
entry section
    critical section
exit section
    remainder section
```
- 1. Enter if idle: Any thread can enter the critical section if no other thread is currently in it
- 2. Wait if busy: Other threads cannot enter the critical section if there is a thread already inside
- 3. Limited waiting: Threads waiting to enter the critical section **cannot** wait indefinitely
- 4. Yield waiting (optional): Threads unable to enter the critical section should release the CPU (e.g., transition to a blocked state)


---
### Methods for synchronization and mutual exclusion
- Method 1: Disabling hardware interrupts
- Method 2: Software-based solutions
- Method 3: Higher-level abstraction methods


---
<style scoped>
{
  font-size: 30px
}
</style>

### Method 1: Disable hardware interrupts
- By disabling interrupts, there are no context switches, and thus no concurrency
    - Hardware defers interrupt handling until interrupts are re-enabled
    - Modern computer architectures provide instructions to disable interrupts

           local_irq_save(unsigned long flags);
               critical section
           local_irq_restore(unsigned long flags);
- Entering the critical section: Disable all interrupts and save flags
- Leaving the critical section: Enable all interrupts and restore flags
 

---
<style scoped>
{
  font-size: 30px
}
</style>

### Method 1: Disabling hardware interrupts
- Disadvantages
   - Once interrupts are disabled, threads cannot be stopped
      - The entire system comes to a halt
      - May result in other threads starving
   - The critical section may be lengthy
      - It is impossible to determine the time required to respond to an interrupt (hardware influence may exist)
   - Not suitable for multi-core systems
- **Caution must be exercised when using this method**



---
### Method 2: Software-based solution

![w:900](figs/soft-0.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### Method 2: Software-based solution -- Attempt 1
![bg right:40% 100%](figs/soft-1.png)
- Meets the "wait if busy" condition but sometimes fails to meet the "enter if idle" condition
    - If Ti is not in the critical section and Tj wants to continue running, it must wait for Ti to enter the critical section first
    - turn = 0;
      - T0 does not need access
      - T1 needs access and waits indefinitely

---
<style scoped>
{
  font-size: 30px
}
</style>

### Method 2: Software-based solution -- Attempt 2
![bg right:40% 100%](figs/soft-2.png)
- Mutual dependency (thread blind waiting)
- Does not satisfy the "wait if busy" condition
   - flag[i]=flag[j]=0
```c
// thread Tj
do {
    while (flag[i] == 1) ;
    flag[j] = 1;
    critical section
    flag[j] = 0;
    remainder section
} while(1)
```


---
<style scoped>
{
  font-size: 30px
}
</style>

### Method 2: Software-based solution -- Attempt 3
![bg right:40% 100%](figs/soft-3.png)
- Meets the "wait if busy" condition but does not meet the "enter if idle" condition
   - flag[i]=flag[j]=1
```c
// thread Tj
do {
    flag[j] = 1;
    while (flag[i] == 1) ;
    critical section
    flag[j] = 0;
    remainder section
} while(1)
```

---
### Method 2: Software-based solution -- Peterson algorithm

![bg right:50% 100%](figs/soft-peterson.png)
- Meets the classic software-based solution for mutual exclusion between threads Ti and Tj (1981)
- "Kong Rong Yielding the Pear"


---
<style scoped>
{
  font-size: 30px
}
</style>

### Method 2: Software-based solution -- Peterson algorithm

![bg right:40% 100%](figs/soft-peterson-2.png)
```
flag[i] = True;
turn = j;
while(flag[j] && turn == j);
critical section;
flag[i] = False;
remainder section;
```
```
flag[j] = True;
turn = i;
while(flag[i] && turn == i);
critical section;
flag[j] = False;
remainder section;
```


---
### Method 2: Software-based solution -- Dekkers algorithm

![bg right:35% 100%](figs/soft-dekkers.png)
```
do {
   flag[0] = true;// First, P0 raises his hand to indicate that I want to visit
   while(flag[1]) {//Check if P1 has raised his hand
      if(turn==1){// If P1 also raises his hand, then see whose turn it is
          flag[0]=false;// If it is indeed P1's turn, then P0 puts his hand down first (let P1 go first)
          while(turn==1);// As long as it is still time for P1, P0 will not raise its hand and keep waiting
          flag[0]=true;// Wait until P1 is used up (it's P0's turn), and P0 raises its hand again
      }
      flag[1] = false; // As long as you can jump out of the loop, it means that P1 is used up, and you should jump out of the outermost circle of while
   }
   critical section;// access critical section
   turn = 1;// After P0 visits, give the turn to P1, so that P1 can visit
   flag[0]=false;// P0 put down
   remainder section;
} while(true);
```



---
### Method 2: Software-based solution -- Dekkers algorithm

![w:320](figs/dekker.png) vs ![w:320](figs/dekker1.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### Method 2: Software-based solution -- N threads
Eisenberg and McGuire
- A shared "turn" variable and a number of threads arranged in a circular manner.
- Each thread has a flag indicating its desire to enter the critical section
- If multiple threads want to enter the critical section, they move forward in order and the turn variable is updated to the next thread's value after each thread completes execution
![bg right:40% 100%](figs/soft-n.png)




---
### Method 2: Software-based solution -- N threads
```c
INITIALIZATION:

enum states flags[n -1]; //{IDLE, WAITING, ACTIVE}
int turn;
for (index=0; index<n; index++) {
    flags[index] = IDLE;
}
```

---
### Method 2: Software-based solution -- N threads
```c
ENTRY PROTOCOL (for Process i):
repeat {//Whether there is a request process from turn to i: if it exists, it will continue to loop until there is no such process, and mark the current process as ACTIVE
    flags[i] = WAITING;//Indicate that you need resources
    index = turn;//whose turn is it
    while (index != i) {// from turn to i take turns to find idle threads
       if (flag[index] != IDLE) index = turn;//turn to i has non-idle blocking
       else index = (index+1) mod n; //Otherwise it is i's turn and jump out
    }
    flags[i] = ACTIVE;//Pi active; other threads may be active
    // Make further judgments on all ACTIVE processes, and judge whether there are other ACTIVE processes besides the current process
    index = 0;//Check if there are other active ones
    while ((index < n) && ((index == i) || (flags[index] != ACTIVE))) {
       index = index+1;
    }//If there is no active later, and it's Pi's turn or turn idle, it's i's turn; otherwise continue to loop
} until ((index >= n) && ((turn == i) || (flags[turn] == IDLE)));
turn = i;//get turn and process
```

---
### Method 2: Software-based solution -- N threads
```c
EXIT PROTOCOL (for Process i ):

index = turn+1 mod n;//Find one that is not idle
while (flags[index] == IDLE) {
    index = index+1 mod n;
}
turn = index;//Find the one that is not idle and set it to turn; or set it to yourself
flag[i] = IDLE;//end, become idle
```

---
### Method 3: Higher-level abstraction method
- Software based solution
    - Complex and involves busy waiting

- Higher level abstract method
    - Hardware provides some synchronization primitives
        - Disabling interrupts and atomic instructions, etc.
    - **The OS provides higher-level programming abstractions to simplify thread synchronization**
        - eg: locks, semaphores
        - Using hardware primitives

---
<style scoped>
{
  font-size: 30px
}
</style>

### Method 3: Higher-level abstraction method -- Lock  
- A lock is an abstract data structure
    - A binary variable (locked/unlocked)
    - Locks are used to control access to critical sections
    - Lock::Acquire()
       - Waits until the lock is released, then acquires it
    - Lock::Release()
       - Releases the lock and wakes up any waiting threads
![bg right:30% 100%](figs/lock.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### Method 3: Higher-level abstraction method -- Lock  
Modern CPUs provide special atomic instructions
- Atomic instructions
   - Test-and-Set
      - Read a value from a memory location
      - Test if the value is 1 (returns true or false)
      - Set the memory location to 1
        - Enter 0, change to 1, and return 0;
        - Enter 1, keep 1, and return 1;

![bg right:35% 100%](figs/test-and-set.png)
 

---
### Method 3: Higher-level abstraction method -- Lock  
Modern CPUs provide special atomic instructions
```
do {
   while(TestAndSet(&lock));
   critical section;
   lock = false;
   remainder section;
} while (true)
```

![bg right:35% 100%](figs/test-and-set.png)
 
---
<style scoped>
{
  font-size: 30px
}
</style>

### Method 3: Higher-level abstraction method -- Lock  
Modern CPUs provide special atomic instructions
```
do {
   while(TestAndSet(&lock));
   critical section;
   lock = false;
   remainder section;
} while (true)
```
```
lock(): while(TestAndSet(&lock));
critical section;
unlock(): lock=false;
```
![bg right:35% 100%](figs/test-and-set.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

### Method 3: Higher-level abstraction method -- Lock  
- Atomic operation: Compare and Swap (CaS) 
```
bool compare_and_swap(int *value, int old, int new) {
    if(*value==old) {
       *value = new;
       return true; }
    return false;
}
```
```
int lock = 0; // Initially the lock is free
while(!compare_and_swap(&lock,0,1)); // lock lock
critical section;
lock=0; // unlock unlock
remainder section;
```

<!-- ---
### Method 3: More advanced abstract method -- lock (lock)
- Atomic operation: exchange instruction CaS (Compare and Swap)
```
bool compare_and_swap(int *value, int old, int new) {
    if(*value==old) {
       *value = new;
       return true;
    }
    return false;
}
```
```
lock(): while(!compare_and_swap(&lock,0,1));
critical section;
unlock(): lock=0;
``` -->

---
<style scoped>
{
  font-size: 30px
}
</style>

### Method 3: Higher-level abstraction method -- Lock  
<!-- What is CAS? How should the ABA problem be understood? https://zhuanlan.zhihu.com/p/139635112
https://www.zhihu.com/question/23281499/answer/24112589
Regarding the ABA problem, I thought of an example: when you are very thirsty, you find a cup full of water, and you drink it up. Then refill the glass with water. Then you leave, and when the real owner of the glass comes back and sees that the glass is still full of water, of course he doesn't know if someone has drunk it and refilled it. One strategy for a solution to this problem is to assume that an automated recorder records each pour, so the owner can tell when she returns if a refill has occurred since she left. This is also the strategy currently used to solve the ABA problem.
-->
- Atomic operation: Compare and Swap (CaS)
- ABA problem:
   - value = 100;
   - Thread1: value - 50; //Successful value=50
   - Thread2: value - 50; //Blocked
   - Thread3: value + 50; //Successful value=50
   - Thread2: Retry successful
- Solution idea: Add a version number (timestamp)
   - (100,1); (50,2); (100,3)
<!---
### Method 3: More advanced abstract method -- lock (lock)
Modern CPU architectures provide some special atomic operation instructions
- Atomic operation instructions
   - exchange command (exchange)
      - swap two values in memory

![bg right:50% 100%](figs/exchange.png)
-->

---
### Method 3: Higher-level abstraction method -- Lock  
Implementing spinlocks using the Test-and-Set (TaS) instruction
- Threads consume CPU time while waiting
![w:700](figs/spinlock-ts.png)


---
### Method 3: Higher-level abstraction method -- Lock  
**Busy-wait locks vs. waiting locks**
![w:900](figs/spin-wait-lock.png)

---
<style scoped>
{
  font-size: 28px
}
</style>

### Method 3: Higher-level abstraction method -- Lock 
- Advantages
   - Applicable to any number of thread synchronization in single processors or shared-memory multiprocessors
   - Simple and easy to prove
   - Supports multiple critical sections
- Disadvantages
   - Busy waiting consumes processor time
   - May result in starvation
     - Multiple waiting threads when a thread exits the critical section
   - Possible deadlock: Threads waiting for each other, unable to proceed

---
### Summary
- Three commonly used synchronization implementation methods
   - Disabling interrupts (for single processors only)
   - Software-based methods (complex)
   - Locks as a higher-level synchronization abstraction
      - Hardware atomic instructions (applicable to single or multiprocessor systems)
      - Locks can be used to achieve mutual exclusion