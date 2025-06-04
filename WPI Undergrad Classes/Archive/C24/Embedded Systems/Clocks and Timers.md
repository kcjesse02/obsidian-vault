## Hardware Multiplier
- accelerates multiplication
- allows for the data to be moved to the hardware multiplier registers 

# Clocks
- synchronizes the digital circuit in the CPU
- used as reference to execute instructions

![[Clock Cycle.png]]

clock period = $\rho$
$\frac{{\rho}}{{t_{clk}}}=f_{clk}$

- Clock is used as the systems time reference
- increased freq = increased performance
- decreased freq = decreased power consumption
- you will want to change the frequency based on the system application

### MSP430 Clock
- 3 clock outputs: we can create up to 3 clock signals
	- run the CPU and serial communication on separate frequencies

#### Registers
**External Clocks**
`XT1CLK` - LF Crystal Clock
- 32768 Hz fixed freq
`XT2CLK` - HF Crystal Clock
- 4MHz fixed freq
**Internal Clocks**
`DCOCLK` - Internally digitally generated
- ffrom 10KHz to 25MHz
`REFOCLK` - Internal Fixed LF
- 32768Hz Fixed Freq
`VLOCLK` - Internal Fixed HF
- 10 KHz fixed freq

Why do we have Internal and External clocks?
- Internal Clocks are more prone to errors
- External Clocks are used as much as possible to have more precise calculations

##### 3 Clock Signals
`ACLK` - Auxillary Clock
- used by peripherials
- 32768 Hz
`MCLK` - Main CPU Clock
- 1.048576 MHz
`SMCLK` - Sub-main clock
- used by peripherals
- 1.048576 MHz

All of these are software selectable

**A Note**
- use the clock defualts when possible

# Timers
- A digital circuit thagt counts the number of clock ticks
- increment with each clock edge
- (Timers have a set amount of memory, can only count up to a certain number)
#### 4 Bit Timer
![[4-bit timer example.png]]
#### Modes
**Unidirectional Mode**
- count from 0
- set max count
- `t_int`: time between interrupts
![[unidirectional.png]]
$$t\_{int}=(max\_count+1)+t_{clk}$$
**Continuous Mode**
- COunts from 0 to full count of timer
![[continuous mode.png]]

**Up/Down Mode
- Counts from 0 up to max counts, then back to 0

![[up-down.png]]
$$t_{int}=(2*Max\_Count)*t_{clk}$$

Interrupts
- Signal to CPU from a peripheral or other external source
	- Either a request for service from CPU or an indication that the peripheral has something for the CPU
	- CPU chooses to accept or ignore the interrupts
	- ex: fault interrupt kicks in, CPU switches to internal clock
	- Interrupt from a timer means a fixed amount of time has passed

example
```c
x= readButtons()
setLeds(x);

...

interrupt function(){
	blinkLeds();
}

```
stops CPU code and executes something else for a while
![[interrupt-code-exp.png|600]]

#### Timers
Timer B
- used by graphics library
- used to creat PWM signal
- 7 capture and compare registers
Timer A0
- Also hase 7 CCR.

TimerA1
- Used for LCD
Timer A2
- 3 CCr
- We are going to use this one
Real Time Clock



###### Timer Diagram
![[CLKAx DS.png|550]]

###### Timer A Registers
![[timerAx reg.png]]

`TAxCTL` - Control Register
`TAxCCTL0` - Capture/Compare Control 0
`TAxIV` - Interrupt Vectore

##### Using timers in programs
- use Timer A2, not used by anything
- Operating Mode
	- use up mode normally
	- use up/down mode if a longer $t_{clk}$ is needed

**Config Control Registers**
- `TA2CCR0` holds the maximum count

**Write ISR and enable interrupts**


Ex: How will we measure the passage of 0.1 second?
![[timer examp 1.png]]

How do we config TimerA2 ctrl reg
- calculate max counts
![[max_count_demo.png|450]]
max count must be an int
```
327 = max_count + 1
or
328 = max_count + 1
so

max_count = 327 or 327
```
This will cause an error, because we are not counting a 0.1 second exactly!
We have to choose one of them though!
- lets just choose 327
```c
TA2CTL. = TASSEL_1 + ID_0 + MC_1; //ACLK, divider=1, up-mode
TA2CCR0 = 327; //max count
TA2CCTL0=CCIE; //TA2CCR0 interrupts enabled
```

# Real World Timers
default freq
ACLK - 32768Hz
MCLK - 1.048576Hz
SMCLK - 1.048576Hz

$$t\_{int}=(327+1)*(1/32768)=0.0100097661$$
We often can only *approximate* real times
- error will accumulate in real time

How long until clock is off by 0.01?

$$0.01 = \vert{(x\_interrupts)0.01 - (x\_interrupts)0.0100097661}\vert$$
$$ \approx 1024Hz \approx 10.23sec$$

### How do we account for these errors?
Use leapcounts in software!

if we are slower
```c
if(leap_cnt < 1024){
	timer++;
	leap_cnt++;
}
else{
	timer+=2; //+1 to fix the error
	leap_cnt = 0;
}
```

if we are faster
```c
if(leap_cnt < 1024){
	timer++;
	leap_cnt++;
}
else{
	//skip 1 count to fix the error
	leap_cnt = 0;
}
```

#### But its not perfect
leapcounts only really decrease the rate of increase for the error 

How long will it take to have another 0.01 sec error?

$$Real\_{time}= 1024*0.0100097661 = 10.250000386$$
$$error = 0.000000386$$

![[leapcountexamp.png]]

##### Example

timer -> 0.01 sec time interval for each increment
$$min = timer/60000;$$$$sec = \frac {(timer-(min*6000))} {100}$$
$$hundreth = timer\pmod {100}$$

What if we wanted a 1ms timer? What should we change about TimerA2 seconds

$$max_{cnt}= f_{clk}*0.001$$
$$= 32768 *0.001 = 32 ticks$$
Since the interval is so small, interrupts will be run more frequently and take up more of the CPU during execution

Error will also accumulate much faster.

What about $\frac 1 2$ second?
(0.5)(32768) = 16384 ticks

What about 0.0001 second?

(0.0001)() = 3.2768 ticks

3 ticks
- we should use a higher frequency clock, like SMCLK