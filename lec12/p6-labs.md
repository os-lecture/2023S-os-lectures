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

## Section 6 OS(SMOS) Supporting Synchronous Mutex


---
### Practice: SMOS
- **Evolution Goal**
- General idea
- History background
- Practical steps
- Programming

![bg right:65% 100%](figs/thread-coroutine-os-detail.png)


---
### Practice: SMOS -- past goals
<style scoped>
{
  font-size: 29px
}
</style>
Improve performance, simplify development, enhance security, support data persistence, support application flexibility, support inter-process interaction, support threads and coroutines
- TCOS: Support threads and coroutines; IPC OS: Inter-process interaction
- Filesystem OS: support for persistent data storage
- Process OS: Enhanced process management and resource management
- Address Space OS: Isolate the memory address space accessed by APP
- Multiprog & time-sharing OS: Let APP share CPU resources
- BatchOS: isolate APP from OS, strengthen system security, and improve execution efficiency
- LibOS: Isolate APP from HW, simplifying the difficulty and complexity of applications accessing hardware

---
### Practice: SMOS -- Evolutionary Goals
Supports synchronized mutex access to shared resources in multiple threads
- Mutex lock mechanism
- Semaphore mechanism
- The mechanism of locking and condition variables.



---
### Practice: SMOS
### Classmate's evolutionary goal
- Understand the various mechanisms of synchronous mutual exclusion
- Understand how to use the synchronization mutual exclusion mechanism to solve the synchronization mutual exclusion problem
- Can write an OS that supports synchronization and mutual exclusion between threads


<!-- The English name of the loving mother dragon (maiasaura) means "good mother lizard". In Montana, USA in 1979, scientists discovered some dinosaur nests, which contained the skeletons of small dinosaurs. So they named this dinosaur Cimosaurus. . -->

![bg right:30% 100%](figs/maiasaura.png)

---
### Practice: SMOS
- Evolution goals
- **General idea**
     - **Synchronous mutex**
- History background
- Practical steps
- Programming

![bg right:60% 100%](figs/syncmutex-os-key-structures.png)

---
### Practice: SMOS
- Evolution goals
- **General idea**
     - **Synchronous mutex**
- History background
- Practical steps
- Programming

![bg right:60% 100%](figs/syncmutex-os-detail.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

### Practice: SMOS
History background
- Around 1963, when the mathematician Edsger Dijkstra and his team were developing an operating system (THE multiprogramming system) for the Electrologica X8 computer, they proposed that the signal (Semaphore) is a variable or abstract data type, Used to control access to common resources by multiple threads.
- Brinch Hansen (1973) and Hoare (1974) combined the operating system and the Concurrent Pascal programming language to propose a high-level synchronization primitive called a monitor. A monitor is a collection of procedures (procedures, Pascal term, function), shared variables, etc. A thread can call a procedure in a monitor.

---
### Practical steps
```
git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
cd rCore-Tutorial-v3
git checkout ch8
```
Includes multiple multi-threaded applications that are related to synchronization and mutual exclusion.

```
user/src/bin/
├── mpsc_sem.rs # Producer-consumer problem based on semaphore
├── phil_din_mutex.rs # Philosopher's problem based on mutex
├── race_adder_*.rs # Global variable accumulation problems in various ways
├── sync_sem.rs # Synchronous operation based on semaphore
├── test_condvar.rs # Synchronous operation based on condition variable
```

---
### Practical steps
Major improvements to the kernel code
```
os/src/
├── sync
│ ├── condvar.rs //Condition variable
│ ├── mod.rs
│ ├── mutex.rs // mutex
│ ├── semaphore.rs //Semaphore
│ └── up.rs
├── syscall
│ ├── sync.rs // Added system calls related to mutexes, semaphores and condition variables
├── task
│ ├── process.rs //The process control block adds mutexes, semaphores and condition variables
├── timer.rs // Add timer-related condition variables of the TimerCondVar class
```

---
<style scoped>
{
  font-size: 30px
}
</style>

### Practical steps
For example, an application that implements the philosopher's problem shows the process of 5 philosophers thinking/eating/resting with 5 forks.
```
Rust user shell
>> phil_din_mutex
time cost = 7271
'-' -> THINKING; 'x' -> EATING; ' ' -> WAITING
#0: -------xxxxxxxx----------xxxx-----xxxxxx--xxx
#1: ---xxxxxx--xxxxxx-------- x---xxxxxx
#2: -----xx---------xx----xxxxxx------------xxxx
#3: -----xxxxxxxxxx---xxxxx--------xxxxxx--xxxxxxxxx
#4: ------ x------ xxxxxx-- xxxxx------ xx
#0: -------xxxxxxxx----------xxxx-----xxxxxx--xxx
>>
```

---
<style scoped>
{
  font-size: 30px
}
</style>

### Practical steps
`race_adder.rs` : a Multi-threaded application for global variable accumulation problem
```
A //global variable
A=A+1 //Multiple threads accumulate A
```
Multiple threads execute the above code, will there really be a Race Condition?

- **Race**:Concurrent and disordered threads use limited, exclusive, and non-preemptive resources to create conflicts 
- **Race Condition** : multiple threads competitively access a resource that cannot be accessed simultaneously, causing execution errors in an unordered manner.



---
### Practical steps
`race_adder.rs` : a Multi-threaded application for global variable accumulation problem
```
pub fn main() -> i32 {
     let start = get_time();
     let mut v = Vec::new();
     for _ in 0..THREAD_COUNT {
         v.push(thread_create(f as usize, 0) as usize); // f function is the thread body
     }
     let mut time_cost = Vec::new();
     for tid in v. iter() {
         time_cost.push(waittid(*tid));
     }
     println!("time cost is {}ms", get_time() - start);
     assert_eq!(unsafe { A }, PER_THREAD * THREAD_COUNT); //Compare the accumulated value A
     0
}
```


---
### Practical steps
`race_adder.rs` : a Multi-threaded application for global variable accumulation problem
```rust
unsafe fn f() -> ! {
     let mut t = 2usize;
     for _ in 0..PER_THREAD {
         let a = &mut A as *mut usize; // "slow execution" A=A+1
         let cur = a.read_volatile(); // "slow execution" A=A+1
         for _ in 0..500 { t = t * t % 10007; } // increase switching probability
         a.write_volatile(cur + 1); // "slow execution" A=A+1
     }
     exit(t as i32)
}
```
---
### Practical steps
`race_adder.rs` : a Multi-threaded application for global variable accumulation problem
```
>> race_adder
time cost is 31ms
Panicked at src/bin/race_adder.rs:40, assertion failed: `(left == right)`
   left: `15788`,
  right: `16000`
[kernel] Aborted, SIGABRT=6
```
Race Condition will appear every time it is executed!


---
### Practical steps
`race_adder_atomic.rs` :A Multi-thread application for global variable accumulation problem based on atomic operation
```rust
unsafe fn f() -> ! {
     for _ in 0..PER_THREAD {
         while OCCUPIED
             .compare_exchange(false, true, Ordering::Relaxed, Ordering::Relaxed)
             .is_err() { yield_(); } // Approximate spin lock operation based on CAS operation
         let a = &mut A as *mut usize; // "slow execution" A=A+1
         let cur = a.read_volatile(); // "slow execution" A=A+1
         for _ in 0..500 { t = t * t % 10007; } // increase switching probability
         a.write_volatile(cur + 1); // "slow execution" A=A+1
         OCCUPIED.store(false, Ordering::Relaxed); // unlock operation
     }
     ...
```
---
### Practical steps
`race_adder_atomic.rs` :A Multi-thread application for global variable accumulation problem based on atomic operation

```
>> race_adder_atomic
time cost is 29ms
>> race_adder_loop
```
As you can see, the execution is fast and correct.

---
### Practical steps
`race_adder_mutex_[spin|block]`:multi-thread application  based on Mutex
```rust
unsafe fn f() -> ! {
     let mut t = 2usize;
     for _ in 0..PER_THREAD {
         mutex_lock(0); //lock(id)
         let a = &mut A as *mut usize; // "slow execution" A=A+1
         let cur = a.read_volatile(); // "slow execution" A=A+1
         for _ in 0..500 { t = t * t % 10007; } // increase switching probability
         a.write_volatile(cur + 1); // "slow execution" A=A+1
         mutex_unlock(0); //unlock(id)
     }
     exit(t as i32)
}
```
---
### Practical steps
`race_adder_mutex_spin` : A Multi-thread application for  global variable accumulation problem based on mutex
```
>> race_adder_mutex_spin
time cost is 249ms
# Execute system calls, and perform fetch/insert/wait operations on the ready queue
```

`race_adder_mutex_block` : A Multi-thread application for  global variable accumulation problem based on mutex
```
>> race_adder_mutex_blocking
time cost is 919ms
# Execute system calls, and perform fetch/insert/wait operations on the ready queue + waiting queue
```

---
###  Programming
The core data structure of spin mutex and block mutex: `UPSafeCell`
```rust
pub struct UPSafeCell<T> { // Allow safe use of mutable global variables on a single core
     inner: RefCell<T>, //provides internal mutability and runtime borrow checking
}
unsafe impl<T> Sync for UPSafeCell<T> {} //Declaration supports global variables to be safely shared between threads
impl<T> UPSafeCell<T> {
     pub unsafe fn new(value: T) -> Self {
         Self { inner: RefCell::new(value) }
     }
     pub fn exclusive_access(&self) -> RefMut<'_, T> {
         self.inner.borrow_mut() //Get exclusive access to the data it wraps
     }
}
```

---
###  Programming
The core data structure of spin mutex and block mutex
```rust
pub struct MutexSpin {
     locked: UPSafeCell<bool>, //locked is a Boolean global variable wrapped by UPSafeCell
}
pub struct MutexBlocking {
     inner: UPSafeCell<MutexBlockingInner>,
}
pub struct MutexBlockingInner {
     locked: bool,
     wait_queue: VecDeque<Arc<TaskControlBlock>>, //The thread waiting to acquire the lock waits for the queue
}
```


  ---
###  Programming
Related functions of spin mutex
```rust
pub trait Mutex: Sync + Send { //Send means cross-thread move, Sync means cross-thread share data
     fn lock(&self);
     fn unlock(&self);
}

     fn unlock(&self) {
         let mut locked = self.locked.exclusive_access(); //exclusive access locked
         *locked = false; //
     }
```

---
###  Programming
Related functions of spin mutex
```rust
impl Mutex for MutexSpin {
     fn lock(&self) {
         loop {
             let mut locked = self.locked.exclusive_access(); //exclusive access locked
             if *locked {
                 drop(locked);
                 suspend_current_and_run_next(); //put the current thread at the end of the ready queue
                 continue;
             } else {
                 *locked = true; //Get the lock, you can continue to execute in the critical section
                 return;
         ...
  ```


---
###  Programming
Related functions of block mutex
```rust
impl Mutex for MutexBlocking {
     fn lock(&self) {
         let mut mutex_inner = self.inner.exclusive_access(); //Exclusive access to mutex_inner
         if mutex_inner. locked {
             //Put the current thread in the waiting queue related to this lock
             mutex_inner.wait_queue.push_back(current_task().unwrap());
             drop(mutex_inner);
             //Take the current thread out of the ready queue, set it to a blocked state, and switch to another ready thread for execution
             block_current_and_run_next();
         } else {
             mutex_inner.locked = true; //Get the lock, you can continue to execute in the critical section
         }
     }
```


---
###  Programming
Related functions of block mutex
```rust
     fn unlock(&self) {
         let mut mutex_inner = self. inner. exclusive_access();
         assert!(mutex_inner.locked);
         //Take the thread from the waiting queue and put it into the ready queue
         if let Some(waking_task) = mutex_inner.wait_queue.pop_front() {
             add_task(waking_task);
         } else {
             mutex_inner.locked = false; //release the lock
         }
     }
```
---
### Practical steps
`sync_sem`: multi-threaded application based on semaphore
```rust
pub fn main() -> i32 {
     // create semaphores
     assert_eq!(semaphore_create(0) as usize, SEM_SYNC);
     // create threads
     let threads = vec![
         thread_create(first as usize, 0),
         thread_create(second as usize, 0),
     ];
     // wait for all threads to complete
     for thread in threads. iter() {
         waittid(*thread as usize);
     }
...
```

---
### Practical steps
`sync_sem`: multi-threaded application based on semaphore
```rust
unsafe fn first() -> ! {
     sleep(10);
     println!("First work and wakeup Second");
     semaphore_up(SEM_SYNC);
     exit(0)
}
unsafe fn second() -> ! {
     println!("Second want to continue, but need to wait first");
     semaphore_down(SEM_SYNC);
     println!("Second can work now");
     exit(0)
}
```

---
### Practical steps
`sync_sem`: multi-threaded application based on semaphore
```
>> sync_sem
Second want to continue, but need to wait first
First work and wakeup Second
Second can work now
sync_sem passed!
```
- The initial value of the semaphore is set to 0
- semaphore_down() : The thread will suspend/block
- semaphore_up(): will wake up the suspended thread

---
###  Programming
The core data structure of semaphore
```rust
pub struct Semaphore {
     pub inner: UPSafeCell<SemaphoreInner>, //The internal variable structure wrapped by UPSafeCell
}

pub struct SemaphoreInner {
     pub count: isize, //count value of semaphore
     pub wait_queue: VecDeque<Arc<TaskControlBlock>>, //Waiting queue for semaphore
}
```

---
###  Programming
Related functions of semaphore
```rust
     pub fn down(&self) {
         let mut inner = self. inner. exclusive_access();
         inner.count -= 1; //The count value of the semaphore is reduced by one
         if inner.count < 0 {
             inner.wait_queue.push_back(current_task().unwrap()); //put into the waiting queue
             drop(inner);
             //Take the current thread out of the ready queue, set it to a blocked state, and switch to another ready thread for execution
             block_current_and_run_next();
         }
     }
```


---
<style scoped>
{
  font-size: 30px
}
</style>

### Practical steps
`test_condvar` :Multi-threaded application based on mutex and condition variable
```rust
pub fn main() -> i32 {
     // create condvar & mutex
     assert_eq!(condvar_create() as usize, CONDVAR_ID);
     assert_eq!(mutex_blocking_create() as usize, MUTEX_ID);
     // create threads
     let threads = vec![ thread_create(first as usize, 0),
                         thread_create(second as usize, 0),];
     // wait for all threads to complete
     for thread in threads. iter() {
         waittid(*thread as usize);
     }
     ...
```

---
### Practical steps
`test_condvar` :Multi-threaded application based on mutex and condition variable
```rust
unsafe fn second() -> ! {
     println!("Second want to continue, but need to wait A=1");
     mutex_lock(MUTEX_ID);
     while A == 0 {
         println!("Second: A is {}", A);
         condvar_wait(CONDVAR_ID, MUTEX_ID);
     }
     mutex_unlock(MUTEX_ID);
     println!("A is {}, Second can work now", A);
     exit(0)
}
```

---
### Practical steps
`test_condvar` :Multi-threaded application based on mutex and condition variable
```rust
unsafe fn first() -> ! {
     sleep(10);
     println!("First work, Change A --> 1 and wakeup Second");
     mutex_lock(MUTEX_ID);
     A = 1;
     condvar_signal(CONDVAR_ID);
     mutex_unlock(MUTEX_ID);
     exit(0)
}
```

---
### Practical steps
Multi-threaded application `test_condvar` based on mutex and condition variable
```
>> test_condvar
Second: A is 0
First work, Change A --> 1 and wakeup Second
A is 1, Second can work now
```
- `second` is executed first, but because `A==0`, it waits on the condition variable
- `first` is excuted later, but before `second`, and wake up `second` through the condition variable

---
###  Programming
The core data structure of condvar
```rust
pub struct Condvar {
     pub inner: UPSafeCell<CondvarInner>, //The internal variable structure wrapped by UPSafeCell
}

pub struct CondvarInner {
     pub wait_queue: VecDeque<Arc<TaskControlBlock>>, //wait for queue
}
```

---
###  Programming
Related functions of condvar
```rust
     pub fn wait(&self, mutex: Arc<dyn Mutex>) {
         mutex.unlock(); //release the lock
         let mut inner = self. inner. exclusive_access();
         inner.wait_queue.push_back(current_task().unwrap()); //put into the waiting queue
         drop(inner);
         //Take the current thread out of the ready queue, set it to a blocked state, and switch to another ready thread for execution
         block_current_and_run_next();
         mutex. lock();
     }
```

---
###  Programming
Related functions of condvar
```rust
     pub fn signal(&self) {
         let mut inner = self. inner. exclusive_access();
         //Take the thread from the waiting queue and put it into the ready queue
         if let Some(task) = inner.wait_queue.pop_front() {
             add_task(task);
         }
     }
```

---
###  Programming
The design and implementation of sleep
```rust
pub fn sys_sleep(ms: usize) -> isize {
     let expire_ms = get_time_ms() + ms;
     let task = current_task().unwrap();
     add_timer(expire_ms, task);
     block_current_and_run_next();
     0
}
```
---
### Summary
- Learn to master the synchronization and mutual exclusion mechanism for multi-threaded applications
    - Mutex
    - Signal
    - Condition variable
    - Atomic operations
- Able to write SMOS OS

![bg right:40% 100%](figs/maiasaura.png)