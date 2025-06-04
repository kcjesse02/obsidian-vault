https://clang.llvm.org/docs/CrossCompilation.html
- clang lets you specify info about the target system without downloading a toolchain for the specified system
	- pros - no downloads
	- cons - requires more knowledge about the target system and some tweaking

`Makefile - arm7m`
```c
CFLAGS = --target=armv7m-none-eabi -mcpu=cortex-m4 -flto -fvisibility=hidden -fsanitize=cfi -g

test: test.c
	clang $(CFLAGS) test.c -o test-m
```
- It won't compile on cortex-m systems! Produces the following error:
```
clang: error: 'armv7m-none-unknown-eabi': unable to pass LLVM bit-code files to linker
make: *** [Makefile:4: test] Error 1
```
- If I remove the necessary flags, it does compile

> CMU attempted to use -fsanitize=cfi but commented it out


### Successful compilation
`test.c`
```c
int funct(int i, int j){
	return i + j;
} 

int err(int i, int j){
	return i - j;
}  

int main(){
	volatile char* input;

	gets(input);
	int result = funct(1, 3);
	return result;
}
```

`Makefile`
```c
CFLAGS = --target=armv9a-pc-linux-gnu -mcpu=cortex-a15 -flto -fvisibility=hidden -fsanitize=cfi -fuse-ld=lld -nostdlib -g -v

test: test.c
	clang $(CFLAGS) test.c -o test-a
```
- uses a version that supports memory tagging
	- it also compiles on armv7a, which doesn't support memory tagging
- for a cortex-a processor

| fsanitize setting | cortex-m                   | cortex-a    |
| ----------------- | -------------------------- | ----------- |
| cfi               | linker error               | yeah        |
| address           | linker error + unsupported | yeah        |
| thread            | linker error + unsupported | unsupported |
| memory            | linker error + unsupported | unsupported |
| safe-stack        | linker error + unsupported | yeah        |
| undefined         | linker error               | yeah        |
| dataflow          | linker error + unsupported | unsupported |
| kcfi              | linker error + unsupported | unsupported |



- Include brief description of each settings
- look at other options (especially gcc)
- What is the interaction between clang and gcc for options



Some teams choose to use clang as a comiler, which has an expanded amount of compiler options.

Clang introduces an option called \verb*|-fsanitize| to enable different memory protections, like CFI and a Shadow-stack, included in clang.

However, this is designed for general purpose systems. The code does not compile on a cortex-m when the \verb*|-fsanitize| is used, regardless of the choosen option due to an error with the linker.

Some of the options are not supported on arm systems regardless, such as \verb*|-fsanitize=memory|, which prevents memory access after it has been freed.