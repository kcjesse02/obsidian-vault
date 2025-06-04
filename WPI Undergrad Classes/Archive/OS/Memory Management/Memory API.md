#### Types of Memory
- Stack Memory: also called automatic memory. allocated implicitly when functions and undeclared vars are created
	- implicitly managed memory
	- Every change to the stack pointer is an allocation or deallocation.
	- Ex: when the `call` instruction is run, the return address is saved ont the stack.
	- The stack grows to lower addresses
	- Stack Canary: a type of software defense that attempts to mitigate buffer overflow attacks.
	- stack smashing: attack where an attacker attempts to write beyond the bounds of a local variable.
		- place canary between buffer and other code. check the value of the canary to see if stack smashing has occurred.
- Heap Memory: handled manually using functions like `malloc()` and `calloc()`.
	- Malloc: takes size_t to denote the number of **bytes** of the memory needed
		- returns a void * - OS doesn't know what you are going to use those bytes for. cast to the type of pointer you need. 
	- `double *d = (double *) malloc(sizeof(double));`
		- This creates a pointer to a piece of memory large enough to store 1 double
- `sizeof()` function:
```
int *x = malloc(10 * sizeof(int));
printf("%d\n", sizeof(x));
```
- this will print out the size of the pointer (8bytes), not of size of intx10.
- `free()` deallocates a piece of heap memory
	- makes it available for future use
	- free memory when you're done with it! Can cause memory leaks!
#### Common Memory Errors
- Forgetting to allocate memory
- Not allocating enough memory (buffer overflow)
	- May not even effect anything if corrupted memory is no longer used
- Forgetting to initialize allocated memory
- forgetting to free memory
	- called memory leak
	- eventually leads to running out of memory
- freeing memory before you're done with it (dangling pointer)
- freeing memory repeatedly(double free)
#### A tool to examine memory errors
- Valgrind!!!
- Will give more info than gef
- Tell us about memory leaks and overflow errors that gef won't show us.
# Free Space Management
- The OS is using segmentation to manage memory on the heap (external fragmentation is a problem)
- available space is tracked by the **Free List**
	- linked list with pointer to start and length of chunk
- Splitting and Coalescing
	- Splitting
		- When we request for something smaller than 10 bytes
		- 1 chunk of memory will return to the caller, the other will remain on the list
		- request for one byte of memory: split up one of the free chunks and take one byte for the memory
![[Screenshot 2023-02-06 at 11.41.11 AM.png]]

- Coalescing
	- combines portions of free space to create larger portions for allocation
	- must be adjacent!!!
#### Tracking the size of allocated regions
- store metadata in the header
- This includes size of the allocated portion of memory!!
```
typedef struct {
    int size;

    int magic;
} header_t;
```
![[Screenshot 2023-02-06 at 11.48.55 AM.png]]

#### Growing the Heap
- Best fit: if the heap runs out of space, return a block that is closest to the size the user asks for
- Worst Fit: return the largest chunk it can find. prevents lots of small fragments
- First fit: finds the first block that is big enough
- Next fit: similar to first fit but saves the last position where it was looking between allocations
# Project 4 Tips and Tricks
- Memory allocator which will replace malloc
	- walloc()
	- wfree()
	- init() and destroy()
- CODE CANNOT CALL MALLOC
- chunk headers must be stored in the arena
- read from "/dev/zero", initializes the arena to all 0s.
### Pointer Arithmetic
- depends on the type of pointers
- When we add 1 to an int pointer, it actually add 4 bytes (adds the size of the pointer)
- What if we add to the pointer directly? Still adds the size of the type.
- basing other types of pointers from void pointers
	- offset will be sizeof(void * ) * NumSpace, calculated, then casted.
	- 

#### Valgrind Info
- will give the size of the value. Use that the determine the type.
- 

