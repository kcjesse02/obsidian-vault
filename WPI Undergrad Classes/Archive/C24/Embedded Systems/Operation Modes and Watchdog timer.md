## Power Modes
- We normally use it in Active Mode - where it is fully functional
	- 
	- but this spends power unnecessarily
	- imagine we are running a system off of a battery...

#### 6 op modes
- Active: CPU and all clocks active
- LP 0: CPU and MCLK disabled, SMCLK and ACLK running
- LP 1: CPU, and MCLK, and DCO osc. disabled. DC gen is disabled of the DCO is not used for MCLK and SMCLK in active mode, ACLK active
- LP2: CPU. MCLK, SMCLK, DCO osc disabled. DC generator active, ACLK active
- LP3: CPU, MCLK, SMCLK, DCO osc are disabled, DC gen disabled, ACLK active
- LP4: CPU and all clocks disabled. RAM memory is maintained
- LP4.5: CPU and all clocks disabled. Power management off, Digital IO pin config retiention. RAM memory is not maintained
	- only external interrupts can wake up system


### How to put in Low Power mode
controlled by the status register! (Which also keeps track of control flow flags!)
![[Screenshot 2024-02-23 at 9.22.52 AM.png]]

set with `_BIS_SR()` function

```c
_BIS_SR(LPM3_bits);//sets bits to enter LPM3

_BIS_SR(LPM1_bits | GIE); //sets bits to enter LPM1 and enables interrupts

//make sure to enable interrupts so the system can wake up!!
```

make sure to enable interrupts so the system can wake up!!

### Interrupts Review
- when GIE is enabled or non-maskable interrupt
	- CPU finished executing previous instruction
	- PC and SR to the stack
	- clear the SR (Active Mode...)
	- retrieve addr of ISR from vector table
	- execute ISR
So, when an interrupt is called, wake up and go to active mode!