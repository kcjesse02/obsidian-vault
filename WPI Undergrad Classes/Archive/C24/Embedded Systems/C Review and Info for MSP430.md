- 16 bit ints
	- max `unsigned int` is $2^{16}$ = $65563$
	- `int` range is $-2^{15}$ to $2^{15}-1$
	- value will overflow if exceeds max size
- Addresses are represented in 20 bits (5 hex digits)
- use `long int` for 32 bit number
- float: 32 bits
- double for 64 buts
- chars are still 8 bytes (duh)

EX: overflow program
```c
c=253;
for (a=0; a<4; a++){
	h=c+(char)a;
}
```

| a   | h   |
| --- | --- |
| 0   | 253 |
| 1   | 254 |
| 2   | 255 |
| 3   | 0    |
|     |     |


We have access to basic operations: +, -, \*, /, %
And we have logic operations: > >= < <= == != && ||

and conditional statements if, if-else, case statement, while, for, do while.

Bitwise: & | ^ ~ >> <<
- We will use these a lot
- bit-shifting is often faster than multiplication 

Brackets denote multi line piece of code

