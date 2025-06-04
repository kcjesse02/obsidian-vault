## CPU
- executes memory and arithmetic
- controls timing
- controls peripherals
## Memory
two types
- volatile: requires power to maintain data
	- fast!! zooooom!!
	- ex: RAM
- non-volatile: does not require power to maintain data
	- slow
	- ex: flash, where we store the code

## Peripherials
- 
- usb
- i/o
- adc


These three things are connected through three things
![[Screenshot 2024-01-18 at 9.11.03 AM.png]]
- each peripherial will have its own memory address (means of exchange)
	- write to it to control behavipr
	- read address to see behavior
	- ex: timer, write to it to start counting, read from it to see count





# Layers of Embedded Systems
- Does not have an OS

#### Von Neuman Arch

![[Screenshot 2024-01-18 at 9.31.09 AM.png]]
- we dont need the same physical devices, but they use the same address space
- 

#### Harvard Architecture
- separate addresses for code and data

![[Screenshot 2024-01-18 at 9.27.02 AM.png]]
Can fetch code and data at the same time (load in a single clock cycle)
downsides:
- more space is being used
- complexity of instructions