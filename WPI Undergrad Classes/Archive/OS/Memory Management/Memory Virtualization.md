## Abstraction: Address Space
- early OS didn't have any sort of abstraction for memory. They were basically libraries
- multiprogramming: in which multiple processes are ready to run at a given time
	- Requires time-sharing between programs
	- manually placing things in memory is slow
- Address Space: abstraction of physical memory
	- each process has an address space
	- Think of it like an array!!
		- starts at 0 and goes up!
	- contains all the memory space of the running program![[Screenshot 2023-01-29 at 2.31.59 PM.png]]
- Text: Where the code is
- Heap: Dynamically Allocated Memory
- Stack: 
	- Local Variables 
	- in the stack frame: all info needed to call a particular function
		- Arguments
		- Return Address
- loaded at an arbitrary physical address
- Virtual memory
	- **transparency:** program should act as if it's in its own private, physical memory. Keeping track of all the tasks. **invisible to the process.**
	- **efficiency:** run program in time and space efficient way
	- **protection:** processes shouldn't be able to perform instructions on memory that belong to other processes.
#### Memory access in Assembly
- 5 memory accesses
	- load ebx into eax
	- add 3 to eax register
	- store eax back to ebx
1. load
2. instruction 1
3. instruction 2: add 3
4. instruction 3
5. store
#### Memory Map
```
int main(){
int x = 777;
printf("location of code: %p\n", (void *) main); //compiler decides where main should be
printf("location of the heap: %p\n", (void *) malloc(1));
printf("location of the stack: %p\n", (void *) &x);
printf("location of printf: %p\n", (void *) printf);
printf("location of malloc: %p\n", (void *) malloc);
}
```
- malloc and printf are contained in **procedure linkage table (PLT)**: which lets the code figure out where linked code is loaded into memory
	- Basically makes a bunch of jumps to figure out where links are
	- 
## Address Translation
- Limited Direct Execution: let programs run directly on the hardware, but with some interruption in case something goes wrong
	- Virtual memory is similar to LDE but for managing memory storage instead of execution
	- hardware-based address translation: transforming a memory access from virtual to physical
- assumptions
	- address space is less than the size of the physical memory
	- address spaces place contiguously in memory
```
128: movl 0x0(%ebx), %eax ;load 0+ebx into eax
132: addl $0x03, %eax     ;add 3 to eax register
135: movl %eax, 0x0(%ebx) ;store eax back to mem
```
Makes 5 jumps
 ![[Screenshot 2023-01-29 at 2.55.29 PM.png]]
 ![[Screenshot 2023-01-29 at 2.55.50 PM.png]]

### Dynamic Realocation
- also called base-and-bound
- Base register: Where the code starts in physical memory
```
physical address = virtual address + base
```
- Bound or limit register: the bound of the memory, the size. WIll always check the calculated physical address to make sure it is in bound of the physical memory
- MMU: part of CPU that helps with memory management![[Screenshot 2023-01-29 at 3.01.08 PM.png]]
![[Screenshot 2023-01-29 at 3.02.19 PM.png]]
![[Screenshot 2023-01-29 at 3.03.36 PM.png]]
![[Screenshot 2023-01-29 at 3.05.10 PM.png]]![[Screenshot 2023-01-29 at 3.05.40 PM.png]]
- What does the OS do if the process tries to access memory outside of its bounds
- OS keeps track of what processes are free
	- Memory allocation between different processes
	- Provides exception handlers
	- cleans up after misbehaving process
- Internal Fragmentation: Lots of wasted space!
	- Only a small amount of allocated space is being used
## Segmentation
- Dynamic Reallocation Causes too much internal fragmentation
- How do we support a large address space with lots of free space between the stack and the heap?
- **Segmentation:** have a base and bound pair for every logical segment of the address space: code, stack, and heap
![[Screenshot 2023-01-29 at 3.19.38 PM.png]]
- Place stack, heap, and text independently in memory
- We introduce External Fragmentation
	- Chunks spread out in physical memory
```
// get top 2 bits of 14-bit VA
Segment = (VirtualAddress & SEG_MASK) >> SEG_SHIFT
// now get offset
Offset  = VirtualAddress & OFFSET_MASK
if (Offset >= Bounds[Segment])

    RaiseException(PROTECTION_FAULT)
else

    PhysAddr = Base[Segment] + Offset
    Register = AccessMemory(PhysAddr)
```
- implicit approach: hardware sees where the address comes from and determines segment that way
	- ex: seeing it comes from program counter; must be code
- how does the stack work? since it grows backwards
![[Screenshot 2023-01-29 at 3.28.11 PM.png]]
- sharing certian parts of the address
	- protection bits
	- like chmod
![[Screenshot 2023-01-29 at 3.31.03 PM.png]]
- coarse-grained segmentation: chops up address spaces into large, coarse chunks
- fine-grained: many, smaller chunks
	- requires a **segmentation table**
 ![[Screenshot 2023-01-29 at 3.32.38 PM.png]]
 - compacting physical memory makes it more space efficient. The OS spends less time finding space for new address space. solves external fragmentation problem
 - 
