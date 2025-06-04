1. Difference between the Stack and Heap in allocation
2. Convert a virtual address to hardware address
	1. Dynamic Reallocation and Segmentation
		1. `virtual address + base`
	2. Paging
		1. 3 bits for the page frame in the hardware.
		2. offset byte is the same
3. what is internal and external fragmentation
4. Memory allocation approaches
	1. What is Dynamic Reallocation
	2. What is direct mapping
		1. used in embedded systems often
	3. What is segmentation
	4. What is paging
		1. breaking address space into smaller, more manageable chunks
		2. How are virtual page addresses divided up?
			1. page number(2 bits) +offset(4 bits)
		3. How do you convert
		4. How does is solve problems from dynamic reallocation and segmentation
	5. What are linear page tables?
	6. What is a multi-level page table?
		1. What problem does it solve from linear page tables
5.  What are stack canaries?
	1. a byte of data used to detect stack smashing, if the value is changed, then the stack has been smashed and throw an error
6. What are some Common Mistakes with Memory and how do you avoid them
	1. 
7. Free Space Management
	1. What is Splitting
	2. What is Coalecing
8. Pointer Aritmetic
	1. 
	