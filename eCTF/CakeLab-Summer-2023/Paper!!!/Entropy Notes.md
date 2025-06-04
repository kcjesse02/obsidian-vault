- the Linux Psuedo Random Number Generator (LPRNG) algorithm uses pools of entropy, where multiple random events like the timing of key strokes, occurrences of interrupts, and other factors that are generated from user input. (1) Embedded Systems lack the variety of entropy sources found on a desktop system. 
- Some teams , such at CMU, UIUC, and UCSC, used multiple sources of entropy, much like the LPRNG. This is seen as the optimal. For example, UCSU used a mixture of uninitialized memory, clock drift between the hibernation RTC and CPU clock, a secret seed generated per device, and ADC output from the internal temperature sensor.
- Some teams generated a one seed at build time, not generating other seeds for the rest of the systems runtime.
- WPI generated one seed at build time and never seemed to generate another one again, even after the boards are reflashed. This means that the random nonce value will be the same for different packets, nullifying the reason to use a nonce in the first place. This allowed for replay attacks to be executed against their system.
- (reference the table) 6 out of 20 teams had this error where they generated the seed at build time, or used a hardcoded seed. 
- One entropy-related pitfall specific to the eCTF is reading from seemingly uninitialized memory as all memory is initialized by the MITRE-provided bootloader. This is something teams are not allowed to modify. This would not apply to general embedded development as the memory would likely not be initialized.

- It would be interesting to see how much adding multiple sources of randomness actually increases entropy.
- Does mixing the uninitialized memory (which isn't actually uninitialized )
- Entropy majorly depends on when system seeds the PRNG.

It seems that many of the mistakes that teams make are based on an attempt to apply knowledge of general purpose operating systems to embedded systems. Embedded Systems

*Note: make sure to replace the "some teams" with the specific examples of teams that did this*

## Questions
1. I find myself making claims that teams that used pooled entropy have greater entropy than those who used a single source. How can we measure the entropy of the generated
2. It would be interesting to see how much adding multiple sources of randomness actually increases entropy.
3. 
## Sources
1. https://ieeexplore.ieee.org/abstract/document/1624027
2. ERHARD-RNG


### Takeaways (lessons to be learned)
Teams need some source of unpredictable numbers to generate keys, nonces, and pins to prevent attackers from replaying existing communications or constructing fake ones. The most accepted way to Finding decent sources of entropy can be more challenging on embedded systems than on general purpose systems. This is because 
1. Entropy is hard (these are the problems)
	1. embedded systems have a lot less sources of randomness than general purpose systems
	2. entropy sources that are present may only have a few bits of randomness
		1. Combining multiple samples is crucial in seeding PRNG for embedded systems because the available sources may produce very few bits of randomness with each sample. Almost all teams who implemented some sort of entropy-gathering to seed their PRNG combined multiple samples.
		2. One solution to the problem of limited sources of randomness, sometimes with less entropy than would be ideal, is to combine multiple sources. With concatenation and hashing, samples of random data from multiple sources can be used to prevent lack of randomness due to the use of an entropy source that is less random than expected. For example, UCSC used 4 sources in total, three were random: drift between the Real Time Clock(RTC) and CPU clock, a device-specific seed, and ADC measurement of internal temperature. However, their fourth source was uninitialized memory, which is initialized to all 0xFF bytes by the bootloader provided by MITRE. Since they combined this source that contained no bits of entropy with 3 sources that did contain entropy, the resulting seed was still unpredictable to the attacker.
		3. However, there are ways to combine sources that can increase the overall entropy of the final seed. Some hashing algorithm is usually involved in the generation of a seed. This allows for entropy by shuffling around the position of the random bits while preserving the amount of bits of entropy as much as possible. Some teams concatenated the samples into an entropy pool and then hashed the bit string to generate their seed. This is the way the Linux PRNG algorithm handles combination of samples and many teams utilized this, for example UCSC and Purdue. CMU implemented this a different way, by hashing the sampled values and XORing them together. Both methods appear to accomplish the goal of preserving the inputted randomness as much as possible.
	3. seed files can't exist on a system that does not have a disk
		1. Teams can designate a portion of uninitialized memory at build-time to use as the seedfile. This provides a good initial-seed that can be mixed with other sources once they are gathered. Purdue attempted this with a portion of SRAM, but since SRAM was initialized upon build, this means that the initial seed was predictable. However, they implemented reseeding by mixing the value at the portion of memory designated as the seedfile with 
	4. build-time randomness is not sufficient
		1. 7 teams only generated a seed from randomness present at build-time. This means the value of the seed, and thus the values generated with the PRNG algorithm it is used with, will remain the same until the system is rebuilt. This means that the nonce will always have the same initial value. The attacker may be able collect the first packet sent, reboot the system, and resend that packet and it 
		3. 
2. Combining sources is good
	2. 
3. build-time randomness is not sufficient
4. take multiple samples and mix them
5. 


### Questions
1. Did Teams Re-seed?
2. Did Teams make mistakes gathering data?
	1. only used one sample
3. Did teams make mistakes combining data?
	1. some teams might have not combined data at all
	2. Issue: a single sample might only have a few bits of entropy
4. What is Timer0 and Systick??