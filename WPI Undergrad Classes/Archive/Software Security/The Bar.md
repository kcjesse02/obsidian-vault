#### Part 1: Stack Canary
- leak the stack canary from param 9
- 
#### Leaking Memory
- address of system prints at the end: we can use that to leak the start of libc! to get other 
#### Libc Version stuff
- 
- liam stole the libc version from the server and used pwntools ELF symbols to get the offsets!
	- This is smart and you should do it next time
- 