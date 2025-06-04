**FAU and UCCS1 (and tufts now!)**
- both have the `DEBUG=1` flag set in their build scripts for car and fob. Did this correlate to more successful attacks on their system?
- Did this correlate with the uart bug being present? I think these could be used to show which teams maybe didn't do code review

Many teams optimize for size which makes sense since this is embedded
- VT, UMass, SMU
- CMU uses the z option which optimized for size even more


### Compiler Options
- Wformat option from CMU


**potential thing to explore (after to low hanging fruit):** look at potential side-channel and timing attack prevention from teams who used side-channel-vulnerable crypto algorithms. See if they used other methods to prevent side-channel attacks

### To Do
- Find Low hanging fruit
	- compiler/language
	- compiler options
	- wheather they use embedded assembly
- After that, fill in teams one at a time
	- do some analysis of that design 