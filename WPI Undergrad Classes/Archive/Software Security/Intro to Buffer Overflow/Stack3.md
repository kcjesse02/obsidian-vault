No function call after `gets(buffer)`
Overwrite return instruction with call to `relay_init()`

```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>  

void relay_init(){
	char buffer[64];
	FILE *fp;

	printf("Code flow successfully changed, relays restored.\n");
	printf("Scanning for messages...\n");
	fp = fopen("./flag.txt", "r");
	fgets(buffer, 64, (FILE*)fp);
	printf("flag: %s\n", buffer );

	exit(0);
}

int main(int argc, char **argv){

	char buffer[{{buffsize}}];

	gets(buffer);
}
```
### Process
- Overwrite the return address of `main()`
- Why not overwrite return addr of `gets()`?
	- creates a new stack frame for gets
	- stack frame of `gets()` is before the `main()` stack frame
	- buffer is local to main, and buffer overflows from lower to higher addresses, so it can't overflow anything in gets!

>[!Important]
>Ctrl+A+{ can be used to scroll up in the docker container
>Ctrl+A+} can be used to scroll down

## Aside: Returns
- Where does a function know where to begin execution in after a function is finished
	- The `call` instruction
	- Sets `rip` to the first address in the callee function
	- it saves the value of the next instruction of the caller, on the stack
	- We can overwrite that return address
- **Calling Convention**: Set of rules that define how to actually call functions
	- How do we pass args to a function, via registers
	- `$rsi` and `$rdi`, move arguments into registers before calling
	- execute `call` instruction, jumps into `hello()`
- What does main return to?
	- `__libc_start_call_main()` a library function that calls main

>[!Important]
>MOVAPS Issue
>stack needs to allign to 16 bytes when `movaps` instructon is called in the print function
>need to control the location of `rsp` to fix
>
>How??
>skip the push `rbp` instruction in the prologue of relay_init
>set the address to after the `push rbp`


### $rbp corruption issue
>[!reminder]
>`$rbp` is the stack fram pointer, it points to the bottom of the current stack frame

*How do we corrupt the frame pointer?*
frame pointer did not get setup correctly in win function.
Caused by skipping the set-up for `$rbp`: `mov.  rbp,rsp`
`$rsp` will have the same value as the previous function, which is overwritten with the cyclic

*Why would this happen?*
gdb puts the breakpoint on the first instruction after the prologue! Skipping all stack frame setup.

*Typical Prologue*
```Assembly
push   rbp
mov    rbp, rsp
sub    rsp, 0x50
```
1) pushes the frame pointer onto the stack
2) sets the frame pointer to the correct value 
3) alocates space on the stack by subtracting 0x50 from the stack
*How did we diagnose this bug?*
1. determine what the instruction does
2. determine if the arguments are legal
3. draw memory out by hand to understand how its changing
