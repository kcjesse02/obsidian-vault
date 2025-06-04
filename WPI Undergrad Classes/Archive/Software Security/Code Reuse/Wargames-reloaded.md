#### Code
```c
#include<stdlib.h>
#include<pthread.h>
#include<stdio.h>
#include<unistd.h>
#include<string.h>

#define LOG_SIZE 9
#define CODE_SIZE 12

  

int zero;
char board[3][3];
char *launch_code = "CPE 1704 TKS";
void* ret;

int type_string(const char* str){
	for(int i = 0; str[i] != 0; i++){
		printf("%c", str[i]);
		usleep(20000);

	}
	return 0;

}

  

char check_win() {

	if (board[0][0] == board[0][1] && board[0][0] == board[0][2])
		return board[0][0];
		
	if (board[1][0] == board[1][1] && board[1][0] == board[1][2])
		return board[1][0];

	if (board[2][0] == board[2][1] && board[2][0] == board[2][2])
		return board[2][0];

	if (board[0][0] == board[1][0] && board[0][0] == board[2][0])
		return board[0][0];

	if (board[0][1] == board[1][1] && board[0][1] == board[2][1])
		return board[0][1];

	if (board[0][2] == board[1][2] && board[0][2] == board[2][2])
		return board[0][2];

	if (board[0][0] == board[1][1] && board[0][0] == board[2][2])
		return board[0][0];

	if (board[0][2] == board[1][1] && board[0][2] == board[2][0])
		return board[0][0];

  
	if (board[0][0] == '?' ||
		board[0][1] == '?' ||
		board[0][2] == '?' ||
		board[1][0] == '?' ||
		board[1][1] == '?' ||
		board[1][2] == '?' ||
		board[2][0] == '?' ||
		board[2][1] == '?' ||
		board[2][2] == '?')

			return '?';

  

	return '!';
}

  

void print_board() {

	if(getenv("DEBUGJOSHUA")){

		return;
	}

	char buffer[16];
	sprintf(buffer, " %c | %c | %c\n", board[0][0], board[0][1], board[0][2]);

	type_string(buffer);

	type_string("--- --- ---\n");

	sprintf(buffer, " %c | %c | %c\n", board[1][0], board[1][1], board[1][2]);

	type_string(buffer);

	type_string("--- --- ---\n");

	sprintf(buffer, " %c | %c | %c\n", board[2][0], board[2][1], board[2][2]);

	type_string(buffer);
} 

void calculate_move(int player_r, int player_c){
	int r, c;

  

	if(check_win() != '?')
		return;

	srand(player_r+player_c);

	do {
		r = rand()%3;
		c = rand()%3;

	} while(board[r][c] != '?');

	board[r][c] = 'O';

	printf("Joshua's move: %d %d\n", r, c);

}

void* play() {
	int move_log[LOG_SIZE][2];
	int i = 0;

	type_string("How to play:\n");
	type_string("select a move with 'i j'\n");
	type_string("get three in a row!'\n");

	if(getenv("DEBUGJOSHUA")){
		printf("move log: %p\n", move_log);
	}

	while(i <= LOG_SIZE && check_win() == '?') {

		if (zero == 0 && i % 2){
			calculate_move(move_log[i-1][0], move_log[i-1][0]);
			print_board();
			i++;
			continue;
		}

		type_string("Your move? \n");
		if (scanf("%d %d", &move_log[i][0], &move_log[i][1]) != 2)
			break;

		if (i % 2)
			board[move_log[i][0]%3][move_log[i][1]%3] = 'O';
		else
			board[move_log[i][0]%3][move_log[i][1]%3] = 'X';
		i++;
		print_board();

	}

	printf("Winner is %c\n", check_win());
	exit(0);
	return NULL;

}

  
  

void* launch(){

	int i, j;
	char code[16];
	memset(&code, '?', CODE_SIZE);
	code[CODE_SIZE]= 0;

  

	if(getenv("DEBUGJOSHUA")){
		printf("launch code: %p\n", code);
	}

  

	sleep(3);

  

	for(i = 0; i < CODE_SIZE; i++) {
		for(j = 0x20; j < 0x7F; j++) {
			if(getenv("DEBUGJOSHUA")){
				printf("\r%s", code);
				fflush(stdout);
				}

			sleep(1);
			code[i] = j;
			if (code[i] == launch_code[i])
				break;
			}
		}
		printf("launch code: %s\n", code);
		type_string("Code found. Launching!\n");
		return NULL;
}

  

int main() {

char num_players[16];

char ans;

pthread_t id_play, id_launch;

int result = 0;

ret = __builtin_return_address(0);

if(getenv("DEBUGJOSHUA")){

printf("TODO: Remove DEBUGJOSHUA=1");

printf("TODO: Add hint: cat flag.txt");

}

  

memset(&board, '?', sizeof(board));

  

setbuf(stdout, NULL);

  

type_string("Shall we play a game? [Y/n] ");

result = scanf("%c", &ans);

fflush(stdin);

  

if (ans != 'Y' && ans != 'y' && ans != '\x0a') {

type_string("Goodbye.\n");

return 0;

}

  

type_string("How about Global Thermonuclear War? [Y/n] ");

result = scanf("%c", &ans);

fflush(stdin);

  

if (ans == 'Y' || ans == 'y' || ans == '\x0a') {

type_string("A strange game\n");

sleep(1);

type_string("The only winning move is not to play.\n");

}

type_string("...How about a nice game of tic-tac-toe? [Y/n]");

result = scanf("%c", &ans);

fflush(stdin);

  

if (ans != 'Y' && ans != 'y' && ans != '\x0a') {

type_string("Goodbye.");

return 0;

}

  

type_string("How many players?\n");

gets(num_players);

  

if( strncmp(num_players, "zero", 4) == 0)

zero = 1;

else if (strncmp(num_players, "two", 3) != 0){

type_string("Invalid number of players.\n");

exit(1);

}

  

if(getenv("DEBUGJOSHUA")){ printf("Libc: %p\n", ret);

fflush(stdout);

}

  

pthread_create(&id_play, NULL, play, NULL);

pthread_create(&id_launch, NULL, launch, NULL);

pthread_join(id_play, NULL);

  

exit(0);

}
```

#### Checksec
```bash
[*****] '/root/host-share/CS4401/reloaded/wargames'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
```
- **No PIE:** Text section will not be randomized with ASLR. gadget addresses will stay the same for different executions of the program
- **NX enabled:** No shellcode, if we want to run code, we will have to use ROP or find a rwx section elsewhere.
- **No Canary:** No need for a memory leak

In the hints professor walls outlined three stages to the exploit:
1. Information Leak
2. Control-Flow Hijacking
3. Chain Building
I will use these to describe the different stages of the exploit because they are helpful!

## Information Leak Stage
Looking at the code, one of the first thing that you are likely to notice are the checks of an environement variable `DEBUGJOSHUA`. This is a debug mode! If `DEBUGJOSHUA` is set to 1, a lot of useful information will be provided. 

For example, if we can get the the main return address! We can use this to get the libc base address and bypass ASLR!!
```c
ret = __builtin_return_address(0);

...

if(getenv("DEBUGJOSHUA")){ printf("Libc: %p\n", ret);
	fflush(stdout);
}

```
How do we inject the `DEBUGJOSHUA` variable into the environment?
In order to do that we need to think about how evironment variables work!

We know environment variables are stored at the bottom of the stack from previous challenges. But how exactly are they passed in and stored? Environment variables are really just strings formatted in a specific way: `Key=Value`. They are passed in to the process as a char*[] through a call to `execvpe()`.They remain as a char*[] when they sit on the stack. 

So, we can essentially overwrite one of the string pointers to one that points to the string `DEBUGJOSHUA=1`. The format for our exploit string will be 

## Control Flow Hijacking

## Exploit Chain building

Threads
- each thread has its own stack
- no protections on that stack!!