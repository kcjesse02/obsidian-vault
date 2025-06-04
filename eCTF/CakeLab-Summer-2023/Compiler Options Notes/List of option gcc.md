-fstack-protector-all
-fPIE
-fsanitize
	shadow-call-stack
	kernel-address
	hwaddress
	kernel-hwaddress
	thread
	leak
	pointer-compare
	pointer subtract
-­D_FORTIFY_SOURCE=2 (checks for buffer overflow)
-Wl,-z,relro,-z,now
	
| option                | suboption                 | Purpose of option                                                                                                | result(arm-none-eabi)(nostdlib)  | Result(with stdlib) |
| --------------------- | ------------------------- | ---------------------------------------------------------------------------------------------------------------- | -------------------------------- | ------------------- |
| -fsanitize            | address                   | used to detect a variety of memory issues. Tags every 8 bytes of memory with 1 byte tag. requires memory tagging | linker error                     | same linker error   |
| -fsanitize            | hwaddress                 | Hardware assisted address sanitizer, a version of address sanitizer that consumes less memory                    | not supported                    |                     |
| -fsanitize            | cfi                       |                                                                                                                  | not recognized by gcc            |                     |
| -fsanitize            | shadow-call-stack         | adds a shadow stack                                                                                              | not supported                    |                     |
| -fsanitize            | thread                    | detects race conditions, also included in address                                                                | linker error                     |                     |
| -fsanitize            | leak                      | detects memory leaks, only for multiple linked executables, also included in address                             | compiles                         |                     |
| -fsanitize            | pointer-compare           |                                                                                                                  | linker error from address option |                     |
| -fsanitize            | pointer-subtract          |                                                                                                                  | linker error from address option |                     |
| -fsanitize            | kernel-address            | same function as address, but for the linux kernel                                                               | compiles                         |                     |
| -fsanitize            | kernel-hwaddress          |                                                                                                                  | not supported                    |                     |
| -fsanitize            | undefined                 |                                                                                                                  | linker error                     |                     |
| -fstack-protector     | --param ssp-buffer-size=1 | adds a stack canary to the function                                                                              | linker error                     |                     |
| -fstack-protector-all |                           | adds stack canary to the function. Equivalent of ssp-buffer-size=4                                               | linker error                     |                     |
| -D_FORTIFY_SOURCE=2   |                           |                                                                                                                  | compiles                         |                     |
| -Wl                   | ,-z,relro,-z,now          | makes the global offset table read only                                                                          | compiles                         |                     |
| -z noexecstack        |                           |                                                                                                                  | compiles                         |                     |
| -fPIE, -pie           |                           |                                                                                                                  | compiles                         |                     |
|                       |                           |                                                                                                                  |                                  |                     |

**Possible note about linker errors:** The same error occurs when you try to use a stdlib function like `gets()` with the -nostdlib flag


### Options that compiled

| option                    | what effect does it have on the code?                                                                      |
| ------------------------- | ---------------------------------------------------------------------------------------------------------- |
| -fsanitize=leak           | option only matters for linking executables                                                                |
| -fsanitize=kernel-address | nothing, there is no linux kernel so it does not have an effect                                            |
| -D_FORTIFY_SOURCE=2       |                                                                                                            |
| -z noexecstack            | no noticable effect on the binary, only way to designate a section as nonexecutable is though the MPU                              |
| -Wl,-z,relro,-z,now       | has no effect on the produced assembly code since there is no relocation table                             |
| -fstack-protector         | works because it doesn't even attempt to add canary, need to specify ssp-buffer-size to have it set canary |
| -fPIE -pie                | none, no ASLR on cortex-m                                                                                  |
| -Werror                   | is still valid even with nostdlib                                                                          |
- all of the binaries produced appear the same. Only options that don't have an affect seemed to work.

### -fstack-protector-all
error
```
arm-none-eabi-gcc -g -nostdlib -fstack-protector-all test.c -o test
/root/host-share/eCTF/paper/arm-gnu-toolchain-12.2.mpacbti-rel1-x86_64-arm-none-eabi/bin/../lib/gcc/arm-none-eabi/12.2.1/../../../../arm-none-eabi/bin/ld: warning: cannot find entry symbol _start; defaulting to 00008000

/root/host-share/eCTF/paper/arm-gnu-toolchain-12.2.mpacbti-rel1-x86_64-arm-none-eabi/bin/../lib/gcc/arm-none-eabi/12.2.1/../../../../arm-none-eabi/bin/ld: /tmp/ccxHs0V5.o: in function `funct':
/root/host-share/eCTF/paper/LookingAtCompilerFlags/test.c:4: undefined reference to `__stack_chk_fail'

/root/host-share/eCTF/paper/arm-gnu-toolchain-12.2.mpacbti-rel1-x86_64-arm-none-eabi/bin/../lib/gcc/arm-none-eabi/12.2.1/../../../../arm-none-eabi/bin/ld: /root/host-share/eCTF/paper/LookingAtCompilerFlags/test.c:4: undefined reference to `__stack_chk_guard'

/root/host-share/eCTF/paper/arm-gnu-toolchain-12.2.mpacbti-rel1-x86_64-arm-none-eabi/bin/../lib/gcc/arm-none-eabi/12.2.1/../../../../arm-none-eabi/bin/ld: /tmp/ccxHs0V5.o: in function `err':
/root/host-share/eCTF/paper/LookingAtCompilerFlags/test.c:8: undefined reference to `__stack_chk_fail'

/root/host-share/eCTF/paper/arm-gnu-toolchain-12.2.mpacbti-rel1-x86_64-arm-none-eabi/bin/../lib/gcc/arm-none-eabi/12.2.1/../../../../arm-none-eabi/bin/ld: /root/host-share/eCTF/paper/LookingAtCompilerFlags/test.c:8: undefined reference to `__stack_chk_guard'

/root/host-share/eCTF/paper/arm-gnu-toolchain-12.2.mpacbti-rel1-x86_64-arm-none-eabi/bin/../lib/gcc/arm-none-eabi/12.2.1/../../../../arm-none-eabi/bin/ld: /tmp/ccxHs0V5.o: in function `main':
/root/host-share/eCTF/paper/LookingAtCompilerFlags/test.c:15: undefined reference to `__stack_chk_fail'

/root/host-share/eCTF/paper/arm-gnu-toolchain-12.2.mpacbti-rel1-x86_64-arm-none-eabi/bin/../lib/gcc/arm-none-eabi/12.2.1/../../../../arm-none-eabi/bin/ld: /root/host-share/eCTF/paper/LookingAtCompilerFlags/test.c:15: undefined reference to `__stack_chk_guard'

collect2: error: ld returned 1 exit status
make: *** [Makefile:4: test] Error 1
```

### -D_FORTIFY_SOURCE

`-D` is an option that sets a macro
`_FORTIFY_SOURCE` is a macro

It probably works because it is checking the code not as a part of the OS, but on the level of the code
However, many of its features involves checking standard library functions (like preventing compilation with `gets()` and making sure format strings are set for `printf()`) and thus are not useful for embedded programming

| level | effect                                                                                                                |
| ----- | --------------------------------------------------------------------------------------------------------------------- |
| 1     | buffer bounds checking with `_builtin_object_size`, which is a function that determines the size of a piece of memory |
| 2     | buffer bounds checking and trapping code that has some other unsafe attributes (ex: format string vulnerability)      |
| 3     | adds buffer bounds checking with `_builtin_dynamic_object_size`, which allows for a non-constant size to be returned                                                       |

https://www.gnu.org/software/libc/manual/html_node/Source-Fortification.html

https://gcc.gnu.org/onlinedocs/gcc/Object-Size-Checking.html

1. Would using `-D_FORTIFY_SOURCE` help teams with buffer overflows? 