# Block Cipher Design Principles
- Encrypts data in uniform blocks
![[Screenshot 2024-09-09 at 6.21.53 PM.png]]
- is keyed randomization function
	- block encryption is always the same
	- but blocks get shifted around
	- works in "rounds"
		- each round has a sub key or round key
			- 16 rounds, 16 different sub keys
			- in every round you 
	- Decryption is the reverse of Encryption: $D(x) = E^{-1}(x)$ 
Main Principles
- Confusion: obscure message and key symbols by key replacement (sbox)
- Diffusion: mixing and reordering information, then making local changes global
- each round should work in the 
![[Screenshot 2024-09-09 at 6.29.07 PM.png||450]]
# DES: Data Encryption Standard
- first encryption standard!
- NIST predecessor calls for proposals
- Proposed by IBM
	- IBM Lucifer: with reduced key size
	- 16 rounds of a Feistel network
- kept until 1997
- replaced with AES is 2001

- 64 bit key (8 bytes)
- each round has multiple permutations
![[Screenshot 2024-09-09 at 6.55.37 PM.png||500]]
![[Screenshot 2024-09-09 at 6.56.04 PM.png||500]]
- inital permutation: shuffle around order of bits
	- ![[Screenshot 2024-09-09 at 6.57.19 PM.png||300]]
- final permutaion: inverse of initial permutation
	- ![[Screenshot 2024-09-09 at 6.57.56 PM.png||300]]

### Basic Structure

![[Screenshot 2024-09-09 at 6.30.08 PM.png||450]]
- Left and right halves
- Left becomes right side
- right side: input with the f function (probably a PRNG), xor with 
- Confusion: the f function, Diffusion: flipping the two halves
	- confusion and diffusion is also in the f function
- two rounds shown 
- How do we reverse this?
	- $L_1=R_{0}$
	- $R_{1} \ocross k_{2}=L_0$
	- Generate keys in reverse order
### What is the F function?

##### One example Design
![[Screenshot 2024-09-09 at 7.03.25 PM.png||500]]

Inputs: Right text $R_{i-1}$ and round key $k_i$
1. Expansion E
	- increase diffusion
	- shuffle bits around and repeat them
	- uses a map like init and final permutations
	- ![[Screenshot 2024-09-09 at 7.01.16 PM.png||250]]
	- repeat left and right columns, shuffle them around
2. Xor with round key

3. sbox substitution
	- 8 separate lookup tables $S_{1}-S_8$!
	- 6 bits input, 4 bits output
		- removes repeated bits from expansion
4. permutation
	1. ![[Screenshot 2024-09-09 at 7.11.52 PM.png||300]]
	2. introduces diffusion lol
### Genreating Round Keys
- derives 16 round keys of 48 bits
- 56 bit key and 8 parity bits
- permutation
	- some bits aren't used
	- ![[Screenshot 2024-09-09 at 7.15.18 PM.png||300]]
- spilt key into two halves
	- in rounds 1, 2, 9, and 16, rotate keys by one bit
	- 
### Attacks
- Differential Cryptanalysis
	- analyze ciphertext for differences in plaintexts
	- this can reveal info about the key
		- Ciphertext attack: $2^{47}$
		- known plaintext attack: $2^{55}$ pairs
- Linear Cryptanalysis
	- approximate non-linear building blocks as linear function
	- analysis on many pairs like Diff
		- KPA: $2^{43}$ pairs, if s-box is not protected
- motivates you to refresh your key!
- Also, on modern computers, just do a brute force attack on the key!
###### Legacy Solution
- Triple DES
	- encrypt 3 times with three different keys
	- 112 bits security
	- secure, but slow
	- some applications still use it, but it is slowly losing support.

**Questions for the final:** why not double DES? Why do we need triple DES?


# AES - Advanced Encryption Standard
- 128 bit block cipher
- three possible key sizes: 128/192/256 with 10/12/14 rounds respectively
- uses a substitution permutation network
	- xor-add and table look ups

### Substitution Permutation Network (SPN)
![[Screenshot 2024-09-09 at 6.48.32 PM.png]]
- xor key with block of plaintext
	- uses 128 bit chunks (of plaintext and of key (if larger than 128))
- sub with item in the sbox
	- change in one bit input should change two bits of sbox output
- Diffusion: more rounds

## Example Animation for AES
Round
important: imagine each block as a matrix
1. SubBytes
	1. sub bytes with the sbox
	2. iterate through all 16 bytes in the block
	3. table is reverable: you can revert byte easily
		1. no collision in the table
2. ShiftRows
	1. rotate over two bytes
	2. shift rows in the matrix
3. MixColumns
	1. most complicated part
	2. multiply each column of the block by a fixed matrix
	3. matrix is made such that all multiplication can be done in shifts and xors!
	4. do this for all the columns
4. AddRoundKey -  not used in the last round
	1. xor 128 block with the 128 round key
	2. round key is found with the AES key scheduling algorithm

Do this for x rounds for each block!
- this is fast!
- 

### Key Scheduling
1. xor first column with randomization table
2. xor each column in the key with first generated column
	1. next key will generate the 

### Finite Field Arithmetic
**Modular Arithmetic**
- used in finite field arithmetic
- clock math, get the remainder
- what is $3*x=1 \mod 7$
	- if mod is prime, always have one solution
		- if mod 1 of a prime number, x will be equal to the inverse of the coeffiecent
	- if coefficeint is the greatest common factor, no solution is possible
	- if its co-prime, you can find the solution
		- euclidean algorithm
	- number of solutions is the number of factors between the coefficent and the mod value
- Polynomials
	- $(1+x+3x^{2}+4x^{4}) + (3+2x+3x^{2}+54x^{4}) = 4+3x+3x^2+3x^3+58x^4$
	- add relevant terms
	- what about modulo?
		- apply to every coefficients
		- ${4+3x+3x^2+3x^3+58x^4}\mod17={4+3x+3x^2+3x^3+7x^4}$
	- How do we multiply polynomials in the modulo space?
		- used in MixColumn in AES (modulo 2)
		- you can apply after multiplication 


- you can do it with polynomial division
- to reduce it, you need reduction polynomial
- since we just want a byte, the polynomial must be degree 8 $x^8$
- 