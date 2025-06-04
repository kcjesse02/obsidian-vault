Inputting or Outputting logic levels to the pins of the MSP430

- MSP430 has 8 individually configurable (as input or output) IO ports
- 1-7 are 8 bits, 8 is 3 bits
- Each port is controlled by six byte-addressable registers
- All the I/O ports are memory mapped (of course)
- Each IO port has an interrupt, which can be used to halt info from coming to the CPU

# 6 Controlling Registers
- Each 8 bits long
1. Function Select Register
	1. PnSEL, `P3SEL = 0` 
	2. Bit Values 0 means digital I/O, 1 means some other function (serial comm, timer, ect.)
	3. There are 8 bits P(0-8)SEL
2. Direction Register
	1. Accessed with PnDIR
	```
	P3DIR=0x0F
	O = Input
	1 = Output
	```
3. Input Register
	1. 1st Configuration Register
	```
	PxIN
	char x = P3IN
	```
4. Output Register
	1. 2nd configuration Register
	```
	PxOUT
	char x = 5
	P3OUT = x
	```
1. Drive Strength
2. Pull-up/down resistor enable
	1. Makes the default value of the pin 1 or 0 respectively
	```
	PxREN
	PxREN = 1: Active
	PxREN = 0: Not active
	```
toggle pullup/pull down
```
PxOUT = 0: pull down (default to 0)
PxOUT = 1: pull up (default to 1)
```

**We write 8 bits at a time**
How do we change only one pin at a time?

### Masking Operations
AND:
0 & X = 0
1 & X = X
Can be used to set bits to 0 without touching other bits

OR: 
0 | X = X
1 | X = 1
Can be used to set bits to 1 without touching the other bits

NOT:
Negates a number

Ex: Using CCS configure Port 3 for digital IO with Pins 1 and 0 as inputs and pins 7 through 4 as outputs
![[digital IO example.png|400]]
overwriting example
```c
P3SEL = 0
P3DIR = OxF0 //(1111 0000) toggles the highest 4 as out
//What is the problem?
//Overwriting pin 3 and 4
```
```c
P3SEL = P3SEL & 0x0C //0000 1100

P3DIR = P3DIR | 0xF0 //1111 0000
P3DIR = P3DIR & 0xFC //1111 1100
/*
Do this thing when calculating masking bits
xxxx xxxx
1111 0000 |
1111 1100 &
---------
1111 xx00 <-- put result first, and fill in the blanks
*/
```

### Predefined Constants
```
BIT0 = 0000 0001
BIT1 = 0000 0010
BIT2 = 0000 0100
BIT3 = 0000 1000
....
BIT7 = 1000 0000
```

![[Port COnfig Example.png|600]]

Ex: Write a function that reads the low nibble from P6 into a byte. Use internal pullup resistors. Use contants
```c
P6SEL &= ~(BIT3|BIT2|BIT1|BIT0); //1111 0000
P6DIR &= (BIT7|BIT6|BIT5|BIT4); //1111 0000

//set pullup resistors
//enable them by setting the nibble to 1
P6REN |= (BIT3|BIT2|BIT1|BIT0); //0000 1111
P6OUT |= (BIT3|BIT2|BIT1|BIT0);

//P4
P4SEL &= (BIT3|BIT2|BIT1|BIT0);
P4OUT |= (BIT7|BIT6|BIT5|BIT4);

char in_P6(){
	char inbits;
	inbits = P6IN & (BIT3|BIT2|BIT1|BIT0);
	return inbits;
}

void out_comp_P4(char inByte){
	char outbits = ~inByte;
	
	
}

main() {  
	portConfig();  
	char inData = in_P6(); // inData is locally defined char  
	out_comp_P4(inData);  
}
```

How do we know to configure Digital IO ports when given that the pins are to be set as inputs are outputs, but how do we know?

- look at the scematics
![[LED Schematic.png]]
- We need a voltage difference between the two sides to turn on the LED
- right side of the LED with always be 0V (logic level 0) at resting state
- When a Px is on (logic level 1), the LED will turn on

Not always the case

![[set LEDs example.png]]

On requires a voltage difference
![[Button Schematic.png]]

- Pull Up! Set default value to one!

## Polling
- How we monitor and properly use configured Digital I/O
- create a forever loop

```c
while(1){
	ret_val = readButton; //queries input
	setLeds(~ret_val); //controls output
	sw_Delay(1); //slows down
}
```