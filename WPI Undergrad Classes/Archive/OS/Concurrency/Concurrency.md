- The illusion of concurrency is both powerful and useful
	- Helps us think about structure of applications
	- Hides latencies
- Also creates problems
	- Correctness: creates unique problems

### Pandoras Concurrency Box
- Causes us to force us to relax any assumptions that we have about how any thread executes
- Processes may run in any order, stopped and restarted at any time, remain stopped for arbitrary lengths of time

- OS is one of the most difficult concurrent programs
	- share state between processes
	- may use threads to hid hardware delays
	- synchronization problem

## Threading
- **thread**: a point of execution in a process, can have multiple
- processes contain threads, multiple threads can be executed concurrently within a single process
- can also be thought of as an abstraction of the CPU state
- Both processes and threads are used for concurrency
Example: a prgram to create threads
![[Screenshot 2023-02-15 at 11.23.12 AM.png]]
Pthread_join waits until threads are done executing
- Memory on Threads
	- Share the same virtual address space
	- each thread needs its own stack
![[Screenshot 2023-02-14 at 2.06.50 PM.png]]
- Why use threads
	- Threads can all access same memory
	- Lower cost of context switch
	- gives programmer additional control over concurrent execution
- A Multithreaded program: Google Chrome
	- needs to load up many different types of content
	- Needs to request and load many different objects for one page
	- Requests all objects on a page at the same time in one process
	- each tab is one process
- How will address space change with threading
	- Adding new stack for each thread

### Shared Data
- Shared variable counter should be incremented on both threads, creating double the expected output 
![[Screenshot 2023-02-15 at 11.32.00 AM.png]]
- produces different results each time, all below the expected 20000000
- Why?
	- Sometimes, a context switch will occur before final mov instruction on an increment step of a thread occurs.
	- Will cause only one to be incremented instead of 2
	![[Screenshot 2023-02-15 at 11.42.01 AM.png]]
- Race condition: when execution is determined by timing and other non-deterministic factors! (a concurrency bug)

Goal: eliminate race conditions while preserving as much concurrency as possible

- Concurrency: often an illusion that multiple things are happening at once
	- requires stopping and starting
- Atomicity: illusion that set of separate actions occur all at once
	- line that updated a counter in broken example
- Critical Section: sequence of instructions that only one thread should be executing at a time.
	- if multiple threads run in a critical section, causes a race condition

