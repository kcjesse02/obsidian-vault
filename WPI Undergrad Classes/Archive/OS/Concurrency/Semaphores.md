- locks with a value
- `sem_wait()` decrements value associated with semaphore. if to 0 or below, puts to sleep
- `sem_post()` increments value of the sem by 1 and if there are one or more threads waiting, wake one
![[Screenshot 2023-02-20 at 2.11.43 PM.png]]
![[Screenshot 2023-02-20 at 2.12.09 PM.png]]
Semaphores can be used as locks!
- initialize semaphore to 1
- >1: allow more than 1 thread into the critical section

Semaphores can be used as condition variables!
- allow threads to wait until a condition becomes true, and signal when the condition becomes true
- initialize value to 0

Others
- cannot check the value of the semaphore, only increment and decrement
- you don't know the # of waiting threads, the number of unblocked threads maybe 0 or 1


### A lock with Fairness
- rely on the OS: `yeild()` system call.
- Allows the thread to deschedule itself
- The scheduler might go through N-1 yeilds before returning to thread that holds the lock
- Atomic instruction: `FetchAndAdd()` increments value while returning the old value
- Ticket lock: each lock has a ticket
	- Turn keep track of the current turn
	- ticket executes on turn
	- Spins while waiting: looking for num to show up

### Sloppy Counters
- we might have multiple threads that need to implement some sort of counter

#### Semaphores for Ordering

![[Screenshot 2023-02-20 at 2.59.58 PM.png]]
![[Screenshot 2023-02-20 at 2.59.39 PM.png]]
#### Barrier
- all threads wait at the barrier until all threads are done

#### What should be a Critical Section?
- make it as small as possible
- Only on the shared resources that are written to need to be critical section
- Array doesn't need to be a shared variable

## Read Write Locks
- goal: allow as many readers at one time. Writers only one at a time. No readers during writing.
- Write: a simple lock around writing section
- Reading: acquires the write lock so no writing can interrupt reading