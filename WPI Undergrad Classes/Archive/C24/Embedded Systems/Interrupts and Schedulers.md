Requests occur outside of CPU asyncronously
- Timers (periodic)
- I/O pin value
- ADC interrupts
- Interrupt sources are hardwired
	- only certain devices can generate interrupts
	- have a fixed priority ranking
	- executes higher priority interrupts first

#### Types 
- nonmaskable
	- cannot be disabled
	- have the highest priority
- maskable
	- can be disabled
	- timer and ADC!
	- `_BIS_SR(GIE)`: enabling general interrupts

## Interrupt Sources
![[Screenshot 2024-02-20 at 9.14.22 AM.png]]

- interrupts are an example of double indirection (pointer to a pointer (like the global offset table))
- table is fixed

#### When an interrupt is called
- finish current asm instruction
- place status register and program counter on the stack
- 