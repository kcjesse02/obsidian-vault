As shown in the comments in the code and in the schematic for the MSP430F5529 lab board, the RED  
is connected to digital I/O Port 6 Pin 2 (P6.2). The LED flashes on and off depending on whether a logic  
1 or a logic 0 is written to that pin. In blink.c P6.2 is toggled by XOR'ing (^) the Port 6 Out register  
P6OUT with BIT2. BIT2 is a constant defined in msp430f5529.h as 0x0004. Explain how and why this works in your report.



In blink.c there are 2 commented lines of code below the exclusive OR (XOR) statement. Test whether  
logic 1 or logic 0 lights the LED by commenting out the toggle statement and then successively un-  
commenting each of these lines of code. You can also determine which logic value lights the LED by  
stepping through the code and watching the value of P6OUT. Document which logic level (0 or 1) lights  
LED1 in your report.

| expression | led state | value |
| ---- | ---- | ---- |
| P1OUT = P1OUT & ~BIT0; (and not) | always off | 0 |
| P1OUT \|= BIT0; | always on | 1 |


## Demo
1. How do you control the text position on the screen
	1. The 4th and 5th arguments of `drawStringCentered()` are the x and y coordinate of the center of the string position on the screen
2. Write your name onto the lcd screen. Do you need to add a null terminator to the end?
	1. no, the `length` parameter specifies the amount of chars read in by the `drawStringCentered()`
3. When does the buzzer sound?
	1. starts when we press * amnd stops when we press \#
4. What is the relationship between the keypad and the leds
	1. Binary
	2. returns ascii values