 Goal: elimiate secure channel from symmetric crypto
- no need for another channel

![[Screenshot 2024-09-23 at 6.05.33 PM.png]]
- provides confidentiality
	- does not provide authenticity
- two different keys
	- pk - public key; publicly available, everyone can encrypt
	- sk - secret key; private; only bob can decrypt
	- these are somehow related, we can have a function that can generate both
- Require additional algorithm to generate key pair
	- $Gen(.) \left (pk, sk)$

# Factorization problem
### One Way Functions
- Easy to calculate result
- difficult to revert
![[Screenshot 2024-09-23 at 6.12.43 PM.png]]
- Factorization is a one-way function
	- requires an exhaustive search to find factors
	- do: 7 \* 3 -> this is the base function; produces 2
	- 
We are doing this on modulo numbers too
- $7\mod15$
- Factors 2 and 11
- Factors 4 and 13
- infintie factor pairs (since the values overflow)
- more search space

#### Inverse Number
- per operation; produces the identity element; the element by which something produces itself when operated on with that element
	- 0 for addition
	- 1 for mult
	- 
- -3 is the inverse of 3 for the addtion set
- 1/3 is the inverse of 3 for multiplication
- mod numbers have a lot of inverse elements
	- ex: 1mod17: 1, 17, 
	- difficult to find one inverse element
	- we need **relatively prime numbers**
		- relatively prime numbers
		- numbers a and b, such that the greatest common divisor is 1

#### Exponentiation
- $x^{t} \mod N$
- number will not get bigger, but will randomly change
- you cannot order results
- you can't compute logarithm on modular spaces
	- discrete logarithm problem
- kind of exp problem for RSA
	- $5^{x}=1\mod17$
- Euler's Theorem
	- Fundamental theorem of algebra: you can write a decompositiontion of every number and modulous
	- $Q^{x}=1 \mod N$ N is a coprime number
	- $x = \Phi(N)$ = a coprime number
	- $\Phi$ is Euler's function
	- $| \Phi (17) | = 1*16-16$ 
	- ![[Screenshot 2024-09-23 at 6.47.56 PM.png]]
	- 
- Fermat Theorem
	- $Q^{x}=1\mod N$ N is a prime number
	- $Q^{N-1}=1\mod N$ for every Q

Now we are ready for..

# RSA
- Rivest, Shamir, and Adleman in 1977
- Widely used for digital signatures and public key encryption
	- phased out for some things
	- continuously needing to increase key size
- Based on modular exponentiation of large integers
- relies on the factoring problem

#### Textbook Definition
- Choose $N=p*q$ (p and q are larve primes $> 2^{512}$)
- Encryption $c = m^{e} \mod N$
	- e is public key
- Decryption $m = c^{d} \mod N$

#### Key Gen
![[Screenshot 2024-09-23 at 7.12.00 PM.png]]
- $ed = 1 \mod {\Phi(N)}$

Called textbook RSA because it is not used
not very secure

How would
![[Screenshot 2024-09-23 at 7.25.06 PM.png]]