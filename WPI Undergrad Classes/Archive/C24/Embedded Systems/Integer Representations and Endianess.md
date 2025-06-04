
![[Screenshot 2024-01-11 at 7.53.15 PM.png]]

How many unique things can we represent with 1 byte?

1 byte = 8 bits
$2^{8} = 256$

This is ascii!!

![[Pasted image 20240111195621.png]]

but a byte can be used to represent many things
- unsigned integers up to 256
- signed integers: -127 - 128
- 2 digit hex numbers
- 8 status flags

Positional Number systems: the base multiplier is determined by the position of a digit in the number
	ex: $$256={2*10^3}+{5*10^2}+{6*10}$$
	Formula: $$\sum_{i=1}^{n}{D_i}{r^i}$$
	10 is the base, for other bases, just change the exponent base to that value

Unsigned Ints: All bits are used to convey magnitude (no sign bit)

Decimal to Bin Conversion: Successive divison by 2
- take the remained and that is the next highest digit

Sign Magnitude Integers: n-1 bits used to convey magnitude with most significant bit used for sign (0=+, 1=-)

We dont use this for most computers. We use 2s Complement
![[Screenshot 2024-01-11 at 8.08.31 PM.png]]

Two's Complement Int: For n bits, values run from $-2^{(n-1)}$ to $2^{(n-1)}-1$. Most common integer system

for positive numbers: you can just use the magnitude to convert to decimal

for negative nums: Encode magnitude, Complement each bit and add 1 to the result

![[Screenshot 2024-01-11 at 8.12.07 PM.png]]

What is the benefit of this system?
1-1 = 1+(-1)

0001 + 1111 = 0000 (overflow)


## Fractional Numbers 
#### Fixed Point
- binary radix point assigned at a fixed location
- just take negative powers
![[Screenshot 2024-01-16 at 9.07.51 AM.png]]

issue: in order to represent smaller numbers, we need to use more and more of the 16 bits given to us by the processor. Floating point solves this issue.
#### Floating Point (IEEE standard)
- used to better approximate real valued decimal numbers to a prescribed number.
	- Sign Bit: its the sign
	- Exponent: used to compute the power of two to shift the magnitude
	- Mantissa: magnitude part!
	![[Screenshot 2024-01-16 at 9.12.13 AM.png]]
Formula
$$(-1)^{s}*2^{(E-127)}*1.F$$
(one point F, the mantissa is the decimal)
![[Screenshot 2024-01-16 at 9.15.01 AM.png]]
1. Identify the sign, exp, and mentissa bits
2. calculate the exponent in decimal
3. determine the 1.F number
4. shift number to the left by 1-E times
5. add sign!

![[Screenshot 2024-01-16 at 9.24.39 AM.png]]

## Endianness
- doesnt have too much advantage compare to the other

#### Little Endian
- Put low Byte value to low address, high byte value to hig address
- but they bytes *themselves* are flipped
![[Screenshot 2024-01-16 at 9.32.55 AM.png]]
- Bytes appear out of order

- x86 processors are little endian
- RISC processors are big

