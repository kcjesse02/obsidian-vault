### Parts of the system
1. embedded OS
	- stores control data in separate memory regions
2. Compiler
	- transforms code
	- protects memory regions with Forward CFI
3. API
	- used to securely update protected data

- Extension to FreeRTOS
- Prevents the access of gadgets (~2000 -> 27)
- This is a proposed solution to:
	1. lack of space
	2. lack of common OS features (virtual memory ect.)

### Arm
- There is a privileged and unprivileged mode in ARM!
- also supports instructions that will prevent unprivileged code from accessing privileged memory.
- Provides an MPU
	- define start addr and length of protected regions


> [!Questions]
> 1. What is control data? Sounds like return addresses and other pointers
> 2. What is the difference between control flow integrity and return address integrity?
> 3. 