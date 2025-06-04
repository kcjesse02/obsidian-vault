### Basic Info (given by the binary)
Improved Fully Unguessable Convoluted Kinetogenic Userspace Pseudoransomization (IFUCKUP)is a new method where the binary is constantly moving around in memory.

It is also capable of moving the stack around randomly and will be able to move the heap around in the future.

- Sections move around while the code is running!! Essentially Runtime ASLR.
- Tactics for bruteforcing ASLR
	- if there is fork or repeated section of code, take advantage of that to brute force one bit at a time?
- maybe brute force attack

### CheckSec Info
```bash
[*****] '/root/host-share/CTFs/DEFCON_QUALS/IFUCKUP/ifuckup'
    Arch:     i386-32-little
    RELRO:    No RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x8048000)
```
- ASLR is applied, but no PIE??
	- Start of the code execution remains the same... You can at least break at the same location in gdb
	- sections are scrambled by a function that manually moves around pointers
- NX
	- ROP, not shellcode
- 32 bit
	- this makes ret2libc possible
### Questions
- What is the range for the possible starts of the sections with this new ASLR??
- What is the code that remains constant

### Analysis
#### Functions
- Syscall(1:call_number, 2-7:parameters)- this is just a function that makes a syscall.
- set_and_move(pointer, new_value, add_to_addr) - moves and sets the value at a given pointer. parameter 2 specifies the new value that the pointer should point to. parameter 3 specifies the amount (x8 bytes) the address should be moved (increased, negative number for decrease).
- 