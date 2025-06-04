
- Mixing multiple sources of entropy is useful on embedded because there are so little entropy in many of the sources 
- NIST acceptable mixing for entropy sources:
	- XORing different samples together
	- concatenating bits together
	- both of these are acceptable
	- adding, multiplying, and other arithmetic operations are not acceptable because you can predict a range that this number will be in
- Conditioning
	- "The optional conditioning component is a deterministic function responsible for reducing bias and/or increasing the entropy rate of the resulting output bits"
	- CMU used a Von Neumann extractor

Some teams relied on combining multiple randomly sourced values to generate stronger entropy. 

For example, CMU used samples from two ADCs to gather input from the thermal sensors on the board and combined them with a build-time generated seed by XORing one sample from each ADCs with the seed. It is also worth noting that the samples were hashed with the Blake2 hashing algorithm before being mixed, this is likely to further unpredictability to the resulting seed and to ensure a constant length of the bits.
There are two primary examples of simple mixing functions that NIST mentions in their guidelines for secure entropy sources, concatinating the bits of the different values or XORing them. These could create predictable patterns in the generation of the random seed 
UCSC used 5 sources of entropy all together:  including uninitialized memory.
- XORing something with the "uninitialized memory" (which is actually all 0xFF). XORing an all ones bit values is problematic because it will just give the inverse of the other value, not adding any entropy.
- Concatenating with 0xFF memory: there will be a section that is predictable even if it is hashed, making brute-force attacks slightly easier.
- However, combining multiple sources can notably prevent complete lack of entropy, if one of the sources happens to be 
- UCSC likely made this issue, but no other teams were able to exploit this

## Things to change
- hashing spreads around the randomness
- new paragraph for describing uninitialized memory
- read ERHARD RNG
- define terms like ADC, RTC, CPU Clock in footnote
- don't use flowery language. Use clear, precise, and concise language. 