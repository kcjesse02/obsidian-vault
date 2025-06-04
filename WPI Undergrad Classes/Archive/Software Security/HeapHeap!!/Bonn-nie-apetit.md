- previous challenges: data we want to corrupt lives on the heap, doesn't require hijacking the function of the allocator
- corrupt heap metadata in order to do something we want
### Analysis
- set up to use specific libc files provided to us in glibc folder
	- means we may be exploiting an older version of malloc
- Full RELRO: no manipulation of got
- PIE: text section is randomized
- NX: no shellcode

### Gidrah
- no win function
	- maybe a ret2libc: We need to have some sort of leak or fork to get canary and ret address
	- leak known libc address and Get base of system
	- What did we leak the first four bytes of flag?
		- input was being read in through a read call, no null terminator
		- put random bytes up until start of data
		- another info leak: str without null terminator

### Bugs
- orders are an array of char pointers
	- local variable on the stack!


#### New order
- order for how many?: allocates that number of bytes
- What would order: thing you would like to store

## Exploit
- creat 11 orders of size 0x100
- free 9 of them
	- first 7 free chunks go to the tcache bin
	- remaining 2 go to the unsorted bin, they will be consolidated
- make order slightly larger than previous orders
	- will be garunteed to be created from last two freed orders (in the unsorted bin)
	- get that libc address
	- called b chunk
- order 2 chunks the size of the tcache chunks
	- will allocate the chunks before the unsorted bin allocated chunk
	- first one: c chunk
	- second: d chunk
- free c and b chunk
- allocate c with exploit string
	- edit metadata so it points to the malloc-hook
- allocate the b chunk with random data
- allocate the malloc-hook with a one gadget
- `strlen()`
	- store metadata in 8 previous bytes (size)
	- write to c chunk len of c chunk + 8 and increase the size to partially overlap with b chunk
When heap is free, replaces the contents of the chunk with address

[[Heap Bins]]