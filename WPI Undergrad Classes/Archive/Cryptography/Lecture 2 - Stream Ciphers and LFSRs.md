# Stream Ciphers
- how do we design stream ciphers?

### Difference from Block Ciphers
- decrypt data as a stream (bit by bit, constantly incoming)

### Vernam Cipher
- xor of message and key stream
- one time pad system
- if the key is used only once, and is perfectly random, then it is perfectly secure
- is impossible to implement

The basic Stream Cipher construction
![[Screenshot 2024-09-05 at 6.46.02 PM.png]]
#### PRNG
- random seed -> random looking but consistent algorithms
- generates same number from same seed in the same iteration (key symmetry)
- what should a PRNG be?
	- deterministic: produces the same output for the same input, function of the seed
		- called psuedorandom because of the deterministic property
	- unpredicatble/random: given bit n, it is impossible to predict n+1, n+2, etc
- inputs are called seed
	- seed should be random and unpredictable
- **Golomb's Postulates on Psuedorandomness**
	- developed using a coin flip
	- a run: sequence of consequitive ones or zeros
		- run of 1s: block
			- of length k is k consecutive 1s flanked by 0s
		- run of 0s: gap
			- of length k is k consecutive 0s flanked by 1s
	- period: length of the sequence
	- *R1*: In a period of the sequence: the number of 1s and 0s should differ at most by 1
	- *R2*: In every Period, half the runs have length 1, 1/4th of length 2, 1/8th of length 3, ect, as long as the number of runs indicated exceeds 1
	- *R3*: The out-of-phase aut correlation of the sequence always has the same value
		- take any 2 equal length substrings, xor them, the output should be the same 
		- should not provide any information about the order of the bits
- **NIST Randomness Test Suite**
	- 15 statistical tests to evaluate PRNG
Randomness
- Random Bit Stream should be like a series of coin flips
	- each flip has equal probability of producing 0 or 1
	- flips are independant
	- forward unpredicability: cannot guess future bits
	- backward unpredicability: cannot guess pass bits
#### TRNG
- uses an entropy source with a processing function (entropy distillation process) to dispurse the randomness equally over the bit stream
- Nondeterministic: entropy source collects independent bits




## Linear Feedback Shift Registers (LFSR)
- m storage elements (flip-flops) with a feedback network
- connected to clock, shift over each clock tick
- last flip-flop is a XOR-sum of previous flipflops
![[Screenshot 2024-09-05 at 7.02.02 PM.png]]

$k_{1}\bigotimes k_{2} = k_{0}$
then shift

feedback will be the last bit, first is the shift state
- how do we decide which register to use? which to use?
- feedback from just one register: create loop
- period of sequence: 
- system is deterministic: same inital state will produce the same sequence of states
- you can use a recursive function to represent an LSFR
	$z_{i+3}=z_{i+1}+z_{i}\mod2$

![[Screenshot 2024-09-05 at 7.14.33 PM.png]]

$C_{i}$ are the feedback coefficients
$C_{i}=0$ indicates a open gate (not included in feedback, ignore)
$C_{i} = 1$ indicates closed gate
can represent it as a polynomial!

the key consists of switch positions, shift registers, and the number of registers

Theoretical max length: $2^{m}-1$

**How can we achieve max outputs?**
only certain switch positions provide max length
- needs a constant term
- degree of polynomial determines number of registers
- degree of individual terms determine the tappoints

##### Linear Complexity
- infinite binary sequence $s$
- $L(s)$ is the the length of LFSR to generate s
- $L(s)=0$ is the zero sequence
- $L(s) = \inf$ 

find the period of an LFSR
- convert states into decimal
- if we hit the same state again, it has started repeating
- 



### LFSR and Known-Plaintext attack
- attack based on knowledge of some plaintext and its corresponding ciphertext
- assumes $m$ is known
- plaintext = $(x_{0}, x_{1}, ... x_{m-1})$
- ciphertext = $(y_{0}, y_{1}, ... y_{m-1})$
- you can construct the keystream bits: $z_{i}=x_{i}+y_{i}\mod2$
- Goal: find the feedback coefficient
- 
you can use the recursive formula
- find polynomial for each i
- matrix of plaintext, mult by tappoints, solve for Coefficients
![[Screenshot 2024-09-05 at 7.50.09 PM.png]]

![[Screenshot 2024-09-05 at 7.50.36 PM.png]]


	