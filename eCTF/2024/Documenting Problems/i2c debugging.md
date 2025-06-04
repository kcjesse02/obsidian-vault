# Basics
### i2c protocol
- Used for controller to communicate to one or more peripherals.
	- In this case, the controller is the AP and the peripherals are the components
	- 
- each peripheral has a byte-long address
	- ex: Component 1 address 



## looping in `MXC_I2C_RevA_MasterTransaction` problem

#### On the AP
it keeps looping through this portion of code 
```asm
0x100150ae <+378>:   ldr     r3, [r4, #8]
0x100150b0 <+380>:   lsls    r2, r3, #25
0x100150b2 <+382>:   bpl.n   0x100150ae <MXC_I2C_RevA_MasterTransaction+378>
```
1. Load contents of r4+8 into r3
2. logical shift left contents of r3 by 25 bits, store in r2
3. if N flag set (result of shift less than 0), jump up to top of loop
#### On the Component Side
- not receiving anything

### Our Issue
- High Voltage Level hung around 1.5v as opposed to 3.3v
- This was fixed by removing the broken, power off board from the pcb
