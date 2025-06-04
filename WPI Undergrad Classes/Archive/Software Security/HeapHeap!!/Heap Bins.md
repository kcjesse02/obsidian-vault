Hold free data after it has been freed. 
Each has a different way of holding data so different exploits will be required for each 
- **tcache:** Thread cache
	- every thread has 64 tcache bins
	- in groups of data 8
	- small, it implements caching
	- singly linked list
	- kind of like a stack: most recently freed bins are on top and first to be grabbed for allocation
- **unsorted:** place where chunks go when they are in the tcache too long
	- this is where consolidation occurs
	- have libc pointers in them!!
- **fast:** only seven chunks at a time, all the same size
- **small:** small allocations
	- 0x10 - 0x3f0
- **large:**

use `heap bins` to view bins in gef

Why do we care about free chunks:
- improves proformance, requires less computation for allocating free space!
- allows for the reuse of space!

Malloc hook and Free hook
- slots in libc where you can put a function pointer to run upon malloc and free
- I wonder how this can be useful to use ඞ