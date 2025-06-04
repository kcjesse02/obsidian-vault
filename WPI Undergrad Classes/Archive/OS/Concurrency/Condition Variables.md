- there are many cases where threads want to check if a condition is true before executing
- Condition Variable: explicit queue threads can put themselves in when waiting for a desired condition to be satisfied.
	- allows to allow ordering to locks
	- allows signals to context switch between threads
	- type declared a `pthread_cond_t`
![[Screenshot 2023-02-16 at 10.32.26 AM.png]]
![[Screenshot 2023-02-20 at 8.11.10 PM.png]]
Circular Wait Prevention: 
- total ordering: always acquire locks in the same order
- partial ordering:

Lock Free Increment
- CompareAndSwap instruction
# Project 5 Info
- using concurrency to write the solution to prefix sums problem.
- Must use **Hillis-Steele Algorithm**
	- hint: ref uses phase barriers
- 