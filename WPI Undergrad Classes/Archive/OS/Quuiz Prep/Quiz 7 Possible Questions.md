We typically assume that a process has access to perfect memory. In other words, when a process stores something in memory, that stored value will remain unchanged until the process decides to overwrite it with some other value or the process terminates. Now consider an alternative: the flawed-memory model. In this model, the process has a tendency to forget values over time (especially those values which haven't been accessed in a while). List the challenges and describe what an OS might do to ensure that processes under the flawed-memory model execute correctly and efficiently.

- it might introduce some permanence bit system: 
	- if bit is set to 1, reaccess every so often, so value is not forgotten
	- or keep in a specifc area of memory that is accessed every so often to prevent forgetting (once every x cycles?)
	- rewite them to different memory locations to prevent forgetting
	- values without permanence bit do not have this 

## Possible Questions/Topics
- Semaphores
	- Sem_post() what does it do?
		- increments by 1 and frees one waiting thread
	- Sem_lock() what does it do?
		- decrements and causes thread to wait if val is <0
	- How do you initialize a sem as a lock
		- initial value to 1
	- a condition var?
		- initial value to 0