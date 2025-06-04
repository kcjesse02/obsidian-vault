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

  

if(f->valid_script= env_scan()) {

f->fp(d);

}

}
```

### Analysis
- **No Relro, No PIE**
- two structs `i1, i2`
- adjacent in memory when we run `heap chunks`
- weird placement of empty `system()` call when we 
- priotity is just a number
- **mode**
	- pointer to a place in memory
	- points to the area directly after the struct
	- malloced after the creation of the struct
	- if we can change the char * when the strcpy is called
		- write-what-where primitive
**Where does system get its arguement string?**
- overwrite the global offset of puts with the got entry of system!!
- run system with the "Collection in progress..." string
- how do we modify the path variable
	- environment variable
- corrupt value of mode pointer in i2 to write to system_plt address

#### What we learned
- We can get a write-what-where primitive from 
- code reuse attacks do not need to be limited in the return to system
	- reuse plt stub code
	- reuse collection string to point to new bash script
- if you change your path to have your directory first