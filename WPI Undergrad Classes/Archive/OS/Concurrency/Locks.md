- Annotate source code
- Dictate critical sections
##### How do we keep Critical Sections Safe
- Mutual Exclusion: only one thread executing at the time
- Progress/Fairness: allows all threads to be executed in a timely manner
- Performance: the solution should have good performance
Don't stop: Thread prevents other thread from executing in a critical section on uniprocessor systems

Don't Start: the external thing that prevents other threads from entering **ITS A LOCK**

##### EXAMPLE WITH LOCK


##### What locks look like
```
lock_t mutex; // some globally-allocated lock ’mutex’    
...   
lock(&mutex);  //call lock() to lock
balance = balance + 1;    
unlock(&mutex; //call unlock() to lock

```
- Balance is a variable shared by multiple threads
- lock ensures that it executes to the end
##### Pthread Locks
```
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER; 2
Pthread_mutex_lock(&lock); // wrapper; exits on failure
balance = balance + 1;
Pthread_mutex_unlock(&lock);
```
- prevents threads from entering locked instructions while another thread is executing in them
### How to Build Locks
- provide mutual exclusion at low cost
	- Disable interrupts in critical sections, code will not stop before execution is complete
		- Downside: gives complete trust to the program, which can cause problems
![[Screenshot 2023-02-15 at 7.11.01 PM.png]]
- uses a flag to determine when interrupts should and shouldn't be allowed
- **Issues with this code**
	- What if both threads set flag to 1?
	- only works on one-processor systems
## Atomic Instructions
- Hardware instructions that are garunteed to be atomic
#### Test-and-Set
- special hardware support for locking
- write to a memory location and return its old value
![[Screenshot 2023-02-15 at 7.30.28 PM.png]]
- also called spin lock

##### Evaluating Spin Locks
- Correctness: does it provide mutual exclusion?
- Fairness: how fair is the lock to a waiting thread?
	- don't provide any fairness garuntees
- Performance:
- We want to spin when the wait for execution is short, when wait is long, CPU should do something else
- 
### Compare and Swap
- checks whether value at a pointer is equal to where expected

### Load-Linked and Store-Conditional
- load linked works the same as a load instruction 
- store conditional only stores if no previous store to the pointer has taken place
![[Screenshot 2023-02-16 at 10.13.04 AM.png]]

Ticket Lock:
- first does a fetch and add 
![[Screenshot 2023-02-16 at 10.18.54 AM.png]]

##### Yeilding
- allows thread to give up the CPU