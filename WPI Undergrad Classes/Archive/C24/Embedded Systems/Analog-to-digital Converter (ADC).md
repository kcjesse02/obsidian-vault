- used to represent a voltage in a range of voltages
- Send a digital binary code to represent different a value of voltage.
	- so it inputs a voltage and produces a digital value
- Low codes = low voltage; high codes = high volt
![[ADCcodeexp.png]]
- precision is determined by the number of bits for the ADC

Can be used with any analog sensor

## Basics
![[ADCgraph.png]]
Readings
1. 100
2. 101
3. 110
4. 111
5. 011
6. 111
7. 011
8. 010

bin_code = 100101110111011111011010

**Some Notes:** 
- Binary codes are always fixed
- ADC will always floor values (if inbetween codes)
- Analog voltage is sampled within fixed timeframe

$$code = floor([{\frac {v - {v^{ref-}}} {{v^{ref+}}-{v^{ref-}}}}]*{2^k-1})$$

$v^{ref+}$ = upper limit of measurable voltage, 5V in this example
$v^{ref-}$= lower limit of measurable voltage, 0v in this case
$k$ = bits of ADC
$2^k-1$ = possible bit combos


### Sample and Hold
sample: take a new value
hold: maintaining the last sampled value

real voltage curve vs ADC sampled
![[sampleandhold.png]]

- freq sampling can actually be a nonideal
	- actual circuits have fluctuations in voltage and more frequent samples can capture these more often and create an unpredictable system


### Conversion between code and read value

![[conversioneq.png]]



#### Appropriate Sampling Rate

![[precision1.png]]

###### Audio needs more precision
![[precision2.png]]


#### Important numbers

Full Scale Range (FSR): diff between upper and lower voltages

$$FSR = {{v^{ref+}}-{v^{ref-}}}$$

Resolution: Smallest possible change in value
$$Res = {\frac {FSR} {2^k}}$$

Dynamic Range: Ratio of largest to smallest value that can be measured signal to noise ratio)

$$DR = 20\log(\frac {2^{k}}1)$$
$1 = smallest \_val$ <-- might be different for other applications


#### Example
![[Screenshot 2024-02-11 at 9.16.40 PM.png|500]]
- measure across resistor with op-amp connected to ADC
Resolution = $\frac {2.5V} {4096} = 0.61 \frac {mV} {bit}$

FSR

# MSP430 ADC
- 16 channel, 12 bit sample and hold ADC
- 200k samples per second maximum
- 4 internal analog inputs A8-A11 
	- temp
	- voltage
	- ect
- Configure by setting values in registers
### Registers
ADC12CTL0
- Sample and Hold Time: ADC12SHT1x and ADC12SHT0x
	- number of clock cycles
- Multiple sample conversion method: ADC12MSC

![[Screenshot 2024-02-08 at 9.04.40 AM.png]]


![[Screenshot 2024-02-08 at 9.05.01 AM.png]]

![[Screenshot 2024-02-08 at 9.09.57 AM.png]]



# Examples

![[Screenshot 2024-02-08 at 9.09.22 AM.png]]
#### Tag-Length value tables
- contains address location to specific calibration values
- in MSP430 temp sensor: 85c - 30c
- voltages: 1.5v, 2.0c, 2.5v

We can use the calibration value to calculate the slope

$$TempC = \frac {(in\_temp-CAL\_VALUE\_30)*(85-30)} {CAL\_VALUE\_85-CAL\_VALUE\_30}$$

code.c
```c
#include <msp430.h>  
// Temperature Sensor Calibration Reading at 30 deg C is stored  
// at addr 1A1Ah. See end of datasheet for TLV table mapping  

#define CALADC12_15V_30C *((unsigned int *)0x1A1A)  

// Temperature Sensor Calibration Reading at 85 deg C is stored  
// at addr 1A1Ch See device datasheet for TLV table mapping  

#define CALADC12_15V_85C *((unsigned int *)0x1A1C)  
unsigned int in_temp;  
int main(void)  
	{  
	volatile float temperatureDegC, temperatureDegF, degC_per_bit;  
	WDTCTL = WDTPW + WDTHOLD; // Stop WDT  
	degC_per_bit = ((float)(85.0 – 30.0))/  
	((float)(CALADC12_15V_85C-CALADC12_15V_30C));   // calculate the slope
	// Reset REFMSTR to hand over control of internal reference  
	// voltages to ADC12_A control registers  
	
	REFCTL0 &= ~REFMSTR;  
	// Internal ref is on and set to 1.5V  
	
	ADC12CTL0 = ADC12SHT0_9 | ADC12REFON | ADC12ON;  
	ADC12CTL1 = ADC12SHP; // Enable sample timer  
	
	ADC12MCTL0 = ADC12SREF_1 + ADC12INCH_10; //reading analog temp input
	//


	__delay_cycles(100); // delay to allow Ref to settle  
	ADC12CTL0 |= ADC12ENC; // Enable conversion  
	while(1)  
	{  
		ADC12CTL0 &= ~ADC12SC; // clear the start bit  
		ADC12CTL0 |= ADC12SC; // Sampling and conversion start  
	// Single conversion (single channel)  
	
	// Poll busy bit waiting for conversion to complete  
	while (ADC12CTL1 & ADC12BUSY)  
		__no_operation();  
	in_temp = ADC12MEM0; // Read results from conversion 
	 
	temperatureDegC=(float)(((long)in_temp-CALADC12_15V_30C) 
	 
	*degC_per_bit + 30.0;  
	// Temperature in Fahrenheit = (9/5)*Tc + 32  
	temperatureDegF = temperatureDegC * 9.0/5.0 + 32.0;  
	__no_operation(); // SET BREAKPOINT HERE  
	}  
}
```


Ex: Now, what if you wanted to monitor both the current through the sensing resistor and  
the internal temperature of the chip at the same time? How should we set up the ADC  
registers to do that?  

>This is a multiple channel, single conversion problem  

> What setting are changed from the single channel problem?

Problem 1: current sensor uses 2.5, temp sensor uses 1.5v
- supply ext. voltage level for other sensor
- change calibration data for temp sensor to read 2.5 v *<--- The Best One!!*
- change internal voltage ref for each individual reading to the correct one for the corresponding sensor

set both FSR to 2.5v

1ext + 1 int analog input

`P6SEL |= BIT0`

```c
#define MA_PER_BIT 0.244 // =1.0A/4096  

// Temperature Sensor Calibration readings for 2.5V from TLV  

#define CALADC12_25V_30C *((unsigned int *)0x1A22)  
#define CALADC12_25V_85C *((unsigned int *)0x1A24)  
unsigned int in_current,in_temp;  
float milliamps, tempC;  
// Reset REFMSTR to hand over control of internal reference  
// voltages to ADC12_A control registers  
REFCTL0 &= ~REFMSTR;  
// Initialize control register ADC12CTL0 = 0000 1001 0111 0000  
// SHT0x=9h (384 clk cycles), MCS=1=burst thru selected chans.,  
// REF2_5V = 1 (2.5V), REFON = 1 = use internal reference volts  
// and ADC12ON = 1 = turn ADC on  
ADC12CTL0=ADC12SHT0_9|ADC12REFON|ADC12REF2_5V|ADC12ON|  
ADC12MSC;  
// Initialize control register ADC12CTL1 = 0000 0010 0000 0010  
// ADC12CSTART ADDx = 0000 = start conversion with ADC12MEM0,  
// ADC12SHSx = 00 = use SW conversion trigger, ADC12SC bits  
// ADC12SHP = 1 = SAMPCON signal sourced from sampling timer,  
// ADC12ISSH = 0 = sample input signal not inverted,  
// ADC12DIVx = 000= divide ADC12CLK by 1,  
// ADC12SSEL=00= ADC clock ADC12OSC (~5 MHz),  
// ADC12CONSEQx = 01 = sequence of channels converted once  
// ADC12BUSY = 0 = no ADC operation active  
ADC12CTL1 = ADC12SHP+ADC12CONSEQ_1;  
// Set conversion memory control registers for the 2 channels  
// ADC12MCTL0: EOS = 0, SREF =001 = voltage refs = GND to Vref+  
// INCHx = 0000  
ADC12MCTL0 = ADC12SREF_1 + ADC12INCH_0;  
// ADC12MCTL1: EOS = 1, SREF =001 = voltage refs = GND to Vref+  
// INCHx = 1010  
ADC12MCTL1 = ADC12SREF_1 + ADC12INCH_10 + ADC12EOS;  
// Set Port 6 Pins 0 to FUNCTION mode (=1) for ADC12  
P6SEL = P6SEL | BIT0;  
// Forever loop to take measurements  
while (1)  
{ //Enable and start single burst conversion  
	ADC12CTL0 |= ADC12SC + ADC12ENC;  
	while (ADC12CTL1 & ADC12BUSY) // poll busy bit  
		__no_operation();  
	in_current = ADC12MEM0 & 0x0FFF; // keep only low 12 bits  
	in_temp = ADC12MEM1 & 0x0FFF; // keep only low 12 bits  
	milliamps = (float)in_current * MA_PER_BIT;  
	tempC = (float)(((long)in_temp-CALADC12_25V_30C)*(85 - 30))/  
	(CALADC12_25V_85C - CALADC12_25V_30C) + 30.0;  
}
```
