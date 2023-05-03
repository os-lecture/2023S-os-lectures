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

# Lecture 11 Threads and coroutines

## Section 2 Coroutine


<br>
<br>

Xiang Yong, Chen Yu, Li Guoliang, Ren Ju

Spring 2023


---

**Outline**

### 1. The concept of coroutines
2. The implementation of coroutines
3. The example pf Coroutine
4. Coroutines and Operating System Kernel

![bg right:60% 70%](figs/coroutine-2.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Insufficient threads

<!-- What is a coroutine? https://zhuanlan.zhihu.com/p/172471249 -->
- What's wrong with threads?
   - Large-scale concurrent I/O operation scenarios
      - A large number of threads ** occupy a large amount of memory **
      - High manageent overhead of threads
         - creation/termination/switching
      - Accessing shared data is prone to errors.
![bg right:51% 100%](figs/thread-issue1.png)


---
<style scoped>
{
  font-size: 30px
}
</style>

#### Proposal of coroutine

<!-- Detailed explanation of coroutines in concurrent programming -- starting with python coroutines (3) https://blog.csdn.net/u013597671/article/details/89762233 -->
Coroutines were proposed and implemented by Melvin Conway in 1963
- The authors describe coroutines as "subroutines that behave like the main program"
- The coroutine uses synchronous programming to support large-scale concurrent I/O asynchronous operations

Donald Knuth: Subroutines are a special case of coroutines
![bg right:42% 100%](figs/coroutine-3.png)

<!-- The concept of a coroutine was first proposed and implemented by Melvin Conway in 1963 to simplify the cooperation between the lexical and syntactic analyzers of the COBOL compiler. At that time, his description of the coroutine was "similar to the main program subroutine". -->

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Definition of coroutine

<!-- Detailed explanation of coroutines in concurrent programming -- starting with python coroutines (3) https://blog.csdn.net/u013597671/article/details/89762233 -->
- Wiki definition: Coroutines are a type of program component that are generalized from the concept of subroutines (procedures, functions, routines, methods, subprograms). Subroutines have only one entry point and return only once, while coroutines allow multiple entry points and can be suspended and resumed at specified positions during execution.


core idea: active surrender and recovery of control flow

<!-- Learning thinking about Generator/async/await in Coroutine-ES https://blog.csdn.net/shenlei19911210/article/details/61194617 -->

![bg right:35% 100%](figs/coroutine-3.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Coroutine (asynchronous function) and function (synchronous function)

<!-- C++20 coroutine principle and application https://zhuanlan.zhihu.com/p/498253158 -->
- Compared with ordinary functions, the function body of coroutines can be suspended and resumed at any time
   - **Stackless coroutine is a generalization of ordinary functions**
   - The coroutines in **this course** are limited to stackless coroutines 

![bg right:50% 100%](figs/function-coroutine.png)

---
<style scoped>
{
  font-size: 28px
}
</style>

#### Comparison between coroutines (stackless coroutines) and user threads

- Coroutines have a smaller memory footprint than threads
   - The larger the number of threads, the more obvious the performance advantage of the coroutine
- There is no need for a multi-threaded locking mechanism, and there is no conflict of writing variables at the same time. In the coroutine, shared resources are controlled without locking, and only the status needs to be judged, so the execution efficiency is much higher than that of multi-threading.
![w:750](figs/coroutinevsthread.png)

---

#### The example pf Coroutine

```
def func()://normal function
    print("a")
    print("b")
    print("c")
```
```
def func()://coroutine function
   print("a")
   yield
   print("b")
   yield
   print("c")
```

![bg right:55% 80%](figs/coroutine2.png)

---

**Outline**

1. The concept of coroutine
### 2. The implementation of coroutines
3. The example pf Coroutine
4. Coroutines and Operating System Kernel

![bg right:50% 70%](figs/coroutine-2.png)

---

#### The implementation of coroutines

<!-- Detailed explanation of coroutines in concurrent programming -- starting with python coroutines (3) https://blog.csdn.net/u013597671/article/details/89762233 -->
In 2004, Ana Lucia de Moura and Roberto Ierusalimschy, the authors of Lua, published the paper "[Revisiting Coroutines](https://www.researchgate.net/publication/2934331_Revisiting_Coroutines)", proposing to classify coroutines according to three factors:
- Control-transfer mechanism
- Stackful structure
- First-class objects in programming languages

---
<style scoped>
{
  font-size: 26px
}
</style>

#### Coroutine based on control transfer

Control Transfer Mechanism: Symmetric v.s. Asymmetric Coroutines
- Symmetric coroutines:
    - Provides only one transfer operation for directly transfer control between coroutines
    - The symmetric coroutines are all equivalent, and the control is directly transferred between the symmetric coroutines
    - When the symmetric coroutine is suspended, it actively specifies another symmetric coroutine to receive control
- Asymmetric coroutines (Semi-symmetric coroutines):
   - Provide call and suspend operations, return control to the caller when the asymmetric coroutine suspends
   - The caller or upper-level manager calls other asymmetric coroutines to work according to a certain scheduling policy

<!-- The coroutines provided to support concurrency are usually symmetric coroutines, which are used to represent independent execution units, such as coroutines in golang. Coroutines that produce sequences of values are asymmetric coroutines, such as iterators and generators.
These two control transfer mechanisms can express each other, so only one of them needs to be implemented to provide a common coroutine. However, the same expressive power does not mean that the two are also the same in ease of use. Symmetric coroutines make the control flow of the program relatively complex and difficult to understand and manage, while asymmetric coroutines behave like functions in a sense because control is always returned to the caller. Programs written using asymmetric coroutines are more structured. -->

---

#### Coroutine based on control transfer

![w:900](figs/coroutine-sym.png)

---

#### Coroutine based on control transfer

![w:850](figs/coroutine-asym.png)

---

#### Stackful coroutines and stackless coroutines

<!-- Stacked coroutines and stackless coroutines https://cloud.tencent.com/developer/article/1888257 -->
Stackful structure: stackful coroutines vs. Stackful structure
- Stackless coroutine: refers to a function that can be suspended/resumed
    - No independent context space (stack), data is stored on the heap
    - Overhead: the overhead of the function call
- Stackful oroutines: threads that are managed and run in user mode
   - Has an independent context space (stack)
   - Overhead: the overhead of switching threads in user mode
- Can it be suspended in arbitrary nested functions?
   - Stackful structure: yes; Stackful structure: no
<!-- ![bg right:40% 100%](figs/function-coroutine.png) -->

<!--
https://zhuanlan.zhihu.com/p/25513336
Coroutine from entry to persuasion

In addition, the wiki also classifies coroutines:
Asymmetric coroutine, asymmetric coroutine.
Symmetric coroutine, symmetric coroutine.
Semi-coroutine, semi-coroutine.

-->

---
<style scoped>
{
  font-size: 30px
}
</style>

#### Coroutines based on first-class language objects
<!-- Stacked coroutines and stackless coroutines https://cloud.tencent.com/developer/article/1888257 -->
First-class language objects: First-class objects v.s. restricted coroutines (**can't be passed as a parameter**)
- First-class object: the coroutine is used as a first-class object in the language
    - Can be passed as a parameter, created and returned by a function, and stored in a data structure for subsequent operations
    - Provides good programming expression, which is convenient for developers to operate on coroutines
- Constrained coroutines
    - A coroutine implemented for a specific purpose, the coroutine object is limited to a specified code structure
 
---
#### First-class language objects
- Can be assigned to a variable
- Can be embedded in data structures
- Can be passed as an argument to a function
- Can be returned by a function as a value

---

#### Coroutine Future in Rust 

<!--
Ref: https://os.phil-opp.com/async-await/#example
-->

A future is a representation of some operation which will **complete in the future**.

![bg right:50% 95%](figs/async-example.png)

---

#### Implementation of Rust coroutine based on finite state machine

```rust
async fn example(min_len: usize) -> String {
     let content = async_read_file("foo.txt").await;
     if content.len() < min_len {
         content + &async_read_file("bar.txt").await
     } else {
         content
     }
}
```

![bg right width:600px](figs/async-state-machine-basic.png)

---
<!--
#### Concept of Future

* Three phases in asynchronous task:

   1. **Executor**: A Future is **polled** which result in the task progressing
      -Until a point where it can no longer make progress
   2. **Reactor**: Register an **event source** that a Future is waiting for
      - Makes sure that it will wake the Future when event is ready
   3. **Waker**: The event happens and the Future is **woken up**
      - Wake up to the executor which polled the Future
      - Schedule the future to be polled again and make further progress

---
-->

#### Asynchronous execution process  of future based on polling 

<!--
Asynchronous execution process based on polling Future

- The executor polls the `Future` until eventually the `Future` needs to perform some kind of I/O
- The `Future` will be handed off to the reactor that handles the I/O, i.e. the `Future` will wait for that specific I/O
- When an I/O event occurs, the reactor will use the passed `Waker` parameter to wake up the `Future` and pass it back to the executor
- Loop the above three steps until the final `future` task is completed (resolved)
- When the task is completed and the result is obtained, the executor releases the handle and the entire `Future`, and the entire calling process is completed
   -->

![width:750px](figs/future-loop.jpg)

---

#### Advantages of coroutines
- The cost of coroutine creation is small, reducing memory consumption
- The coroutine's own scheduler reduces the overhead of CPU context switching and improves the CPU cache hit rate
- Reduce synchronization locks and improve overall performance
- Can write asynchronous code according to synchronous thinking
   - Write callbacks scheduled by coroutines with synchronous logic

---
#### Coroutines vs Threads vs Processes
- switching
   - Process: page table, heap, stack, registers
   - Thread: stack, registers
   - Coroutine: register, no stack change

![w:560](figs/threadvsroutine1.png)![w:580](figs/threadvsroutine2.png)

---

#### Coroutines vs Threads vs Processes

Coroutines are suitable for IO-intensive scenarios

![w:1200](figs/coroutine-asym3.jpeg)

---

**Outline**

1. The concept of coroutine
2. The implementation of coroutines
### 3. The example pf Coroutine
4. Coroutines and Operating System Kernel

![bg right:60% 70%](figs/coroutine-2.png)

---

#### Programming languages that support coroutines
- Stackless coroutines: Rust, C++20, C Python, Java, Javascript, etc.
- Stackful coroutines (threads): Go, Java2022, Python, Lua
   
![w:1100](figs/coroutine-langs.png)
<!--
https://wiki.brewlin.com/wiki/compiler/rust%E5%8D%8F%E7%A8%8B_%E8%B0%83%E5%BA%A6%E5%99%A8%E5%AE% 9E%E7%8E%B0/

The core of understanding coroutines is to suspend and resume. Rust's coroutines do this through a state machine, and golang does this through an independent stack. It is important to understand this -->

<!-- Java coroutines are coming https://cloud.tencent.com/developer/article/1949981 -->
<!-- In-depth Lua: The implementation of coroutines https://zhuanlan.zhihu.com/p/99608423 -->

<!-- Coroutines in Rust: Future and async/await https://zijiaw.github.io/posts/a7-rsfuture/ -->

---
### Using coroutines -- go
<!-- Go by Example Chinese Version: Coroutines
https://gobyexample-cn.github.io/goroutines -->

```go
... //https://gobyexample-cn.github.io/goroutines
func f(from string) {
     for i := 0; i < 3; i++ {
         fmt. Println(from, ":", i)
     }
}
func main() {
     f("direct")
     go f("goroutine")
     go func(msg string) {
         fmt.Println(msg)
     }("going")
     time. Sleep(time. Second)
     fmt.Println("done")
}
```

![bg right:30% 100%](figs/go-ex1.png)

---
<style scoped>
{
  font-size: 30px
}
</style>

<!--
Making multiple HTTP requests using Python (synchronous, multiprocessing, multithreading, asyncio)
https://www.youtube.com/watch?v=R4Oz8JUuM4s
https://github.com/nikhilkumarsingh/async-http-requests-tut -->
### Using coroutines -- python
<!-- asyncio is a standard library introduced by Python 3.4, which directly has built-in support for asynchronous IO. Just add the async keyword in front of a function to turn a function into a coroutine. -->

```python
URL = 'https://httpbin.org/uuid'
async def fetch(session, url):
     async with session. get(url) as response:
         json_response = await response.json()
         print(json_response['uuid'])
async def main():
     async with aiohttp.ClientSession() as session:
         tasks = [fetch(session, URL) for _ in range(100)]
         await asyncio. gather(*tasks)
def func():
     asyncio. run(main())
```
```
// https://github.com/nikhilkumarsingh/async-http-requests-tut/blob/master/test_asyncio.py
b6e20fef-5ad7-49d9-b8ae-84b08e0f2d35
69d42300-386e-4c49-ad77-747cae9b2316
1.5898115579998375
```

<!-- Process, Thread and Coroutine (Process, Thread and Coroutine) theory, practice, code python
https://leovan.me/cn/2021/04/process-thread-and-coroutine-theory/
https://leovan.me/cn/2021/04/process-thread-and-coroutine-python-implementation/
https://github.com/leovan/leovan.me/tree/master/scripts/cn/2021-04-03-process-thread-and-coroutine-python-implementation -->



---
### Using coroutines -- rust

<!-- https://rust-lang.github.io/async-book/01_getting_started/01_chapter.html -->

```rust
use futures::executor::block_on;

async fn hello_world() {
     println!("hello, world!");
}

fn main() {
     let future = hello_world(); // Nothing is printed
     block_on(future); // `future` is run and "hello, world!" is printed
}
```
```
https://rust-lang.github.io/async-book/01_getting_started/01_chapter.html
```

<!--
Write an os with python
http://www.dabeaz.com/coroutines/
http://www.dabeaz.com/coroutines/Coroutines.pdf -->
---
### Process/thread/coroutine performance comparison
<!--
https://www.youtube.com/watch?v=R4Oz8JUuM4s
https://github.com/nikhilkumarsingh/async-http-requests-tut
git@github.com: nikhilkumarsingh/async-http-requests-tut.git -->
Single process: 28 seconds
```python
import requests
from timer import timer
URL = 'https://httpbin.org/uuid'
def fetch(session, url):
     with session.get(url) as response:
         print(response. json()['uuid'])
@timer(1, 1)
def main():
     with requests.Session() as session:
         for _ in range(100):
             fetch(session, URL)

```
---
### Process/thread/coroutine performance comparison
<!--
https://www.youtube.com/watch?v=R4Oz8JUuM4s
https://github.com/nikhilkumarsingh/async-http-requests-tut
git@github.com: nikhilkumarsingh/async-http-requests-tut.git -->
Multi-process: 7 seconds
```python
from multiprocessing.pool import Pool
import requests
from timer import timer
URL = 'https://httpbin.org/uuid'
def fetch(session, url):
     with session.get(url) as response:
         print(response. json()['uuid'])
@timer(1, 1)
def main():
     with Pool() as pool:
         with requests.Session() as session:
             pool.starmap(fetch, [(session, URL) for _ in range(100)])
```

---
### Process/thread/coroutine performance comparison
<!--
https://www.youtube.com/watch?v=R4Oz8JUuM4s
https://github.com/nikhilkumarsingh/async-http-requests-tut
git@github.com: nikhilkumarsingh/async-http-requests-tut.git -->
Thread: 4 seconds
```python
from concurrent.futures import ThreadPoolExecutor
import requests
from timer import timer
URL = 'https://httpbin.org/uuid'
def fetch(session, url):
     with session.get(url) as response:
         print(response. json()['uuid'])
@timer(1, 1)
def main():
     with ThreadPoolExecutor(max_workers=100) as executor:
         with requests.Session() as session:
             executor. map(fetch, [session] * 100, [URL] * 100)
             executor. shutdown(wait=True)
```

---
### Process/thread/coroutine performance comparison
<!--
https://www.youtube.com/watch?v=R4Oz8JUuM4s
https://github.com/nikhilkumarsingh/async-http-requests-tut
git@github.com: nikhilkumarsingh/async-http-requests-tut.git -->
Coroutine: 2 seconds
```python
...
URL = 'https://httpbin.org/uuid'
async def fetch(session, url):
     async with session. get(url) as response:
         json_response = await response.json()
         print(json_response['uuid'])
async def main():
     async with aiohttp.ClientSession() as session:
         tasks = [fetch(session, URL) for _ in range(100)]
         await asyncio. gather(*tasks)
@timer(1, 1)
def func():
     asyncio. run(main())
```
<!-- import asyncio
import aiohttp
from timer import timer

requirements.txt
requests
aiohttp

-->

---
#### [Example](https://deepu.tech/concurrency-in-modern-languages/) of Rust threads and coroutines

Multi-threaded concurrent webserver

```rust
fn main() {
     let listener = TcpListener::bind("127.0.0.1:8080").unwrap(); // bind listener
     let pool = ThreadPool::new(100); // same number as max concurrent requests

     let mut count = 0; // count used to introduce delays

     // listen to all incoming request streams
     for stream in listener.incoming() {
         let stream = stream. unwrap();
         count = count + 1;
         pool. execute(move || {
             handle_connection(stream, count); // spawning each connection in a new thread
         });
     }
}
```

---

#### [Example](https://deepu.tech/concurrency-in-modern-languages/) of Rust threads and coroutines

Asynchronous concurrent webserver

```rust
#[async_std::main]
async fn main() {
     let listener = TcpListener::bind("127.0.0.1:8080").await.unwrap(); // bind listener
     let mut count = 0; // count used to introduce delays

     loop {
         count = count + 1;
         // Listen for an incoming connection.
         let (stream, _) = listener. accept(). await. unwrap();
         // spawn a new task to handle the connection
         task::spawn(handle_connection(stream, count));
     }
}
```
---

#### [Example](https://deepu.tech/concurrency-in-modern-languages/) of Rust threads and coroutines



```rust
fn main() { //Asynchronous multi-threaded concurrent webserver
     let listener = TcpListener::bind("127.0.0.1:8080").unwrap(); // bind listener

     let mut pool_builder = ThreadPoolBuilder::new();
     pool_builder. pool_size(100);
     let pool = pool_builder.create().expect("couldn't create threadpool");
     let mut count = 0; // count used to introduce delays

     // Listen for an incoming connection.
     for stream in listener.incoming() {
         let stream = stream. unwrap();
         count = count + 1;
         let count_n = Box::new(count);

         // spawning each connection in a new thread asynchronously
         pool.spawn_ok(async {
             handle_connection(stream, count_n).await;
         });
     }
}
```

---

#### Thread/coroutine performance comparison

![width:950px](figs/webserver-Thread-Coroutine-Performance.png)

---

**Outline**

1. The concept of coroutine
2. The implementation of coroutines
3. The example pf Coroutine
### 4. Coroutines and Operating System Kernel

![bg right:52% 95%](figs/coroutine-2.png)

---

#### [Unified scheduling of threads and coroutines] 
1. Flexible binding between coroutines and threads;
2. Realize the concurrent execution of the coroutine (future) on a single CPU; it can be executed in parallel on multiple CPUs;
3. Threads and coroutines can adopt different scheduling strategies;
4. Following the processing process of thread interruption, **coroutine can be forcely interrupted**;

![width:600px](figs/thread-corouting-scheduler.png)

---

#### User Mode Coroutine Control Block (CCB, Coroutine Control Block)

![bg right:60% 90%](figs/coroutine-CCB.png)

Ref: [A Design and Implementation of Rust Coroutine with priority in Operating System](https://github.com/AmoyCherry/papper_Async_rCore)

---

#### Process scheduling in kernel mode

![width:850px](figs/coroutine-scheduler-bitmap.png)

---

#### Performance comparison between threads and coroutines

![width:950px](figs/pipe-thread-coroutine-performance.png)

---
#### Reference Information
- https://www.youtube.com/watch?v=R4Oz8JUuM4s
- https://github.com/nikhilkumarsingh/async-http-requests-tut
- http://www.dabeaz.com/coroutines/
- https://rust-lang.github.io/async-book/01_getting_started/01_chapter.html
- https://github.com/nikhilkumarsingh/async-http-requests-tut/blob/master/test_asyncio.py
- https://gobyexample-cn.github.io/goroutines
- https://zijiaw.github.io/posts/a7-rsfuture/

---

### Summary

1. The concept of coroutine
2. The implementation of coroutines
3. The example pf Coroutine
4. Coroutines and Operating System Kernel

![bg right:40% 100%](figs/coroutine-2.png)