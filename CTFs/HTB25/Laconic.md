there is only the `_start` function:
```
Dump of assembler code for function _start:
=> 0x0000000000043000 <+0>:	mov    rdi,0x0
   0x0000000000043007 <+7>:	mov    rsi,rsp
   0x000000000004300a <+10>:	sub    rsi,0x8
   0x000000000004300e <+14>:	mov    rdx,0x106
   0x0000000000043015 <+21>:	syscall
   0x0000000000043017 <+23>:	ret <-- returns to 0x43017 and seg faults...
   0x0000000000043018 <+24>:	pop    rax <-- value of next ret is in rax
   0x0000000000043019 <+25>:	ret
```

- 
- the syscall reads in from stdin to the stack
	- sets `rcx` to `0x43017`
	- sets `rax` to the length of the string inputted
- In order to run code injection, we need to get the function to return to the stack
	- the return address for the first `ret`is fixed at `0x43017`
		- this creates a loop for returning
	- It is set to this value during the system call
- 
