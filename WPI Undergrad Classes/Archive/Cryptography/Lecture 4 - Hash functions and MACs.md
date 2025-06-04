### What is a hash function
- keyless function
- takes input x of variable length
- y of fixed length n
- same x should give the same y
- Cryptographic hash function
	- Goal: obscure the input completely
		- behaves more like a random number generator
	- Preimage Resistance
		- if you are given the output, you should not be able to find the input
		- one way function
		- best hope is searching the whole space
			- a brute force attack would require $2^n$ operations
	- Second Preimage Resistance
		- every input should have a unique output
		- $x_1$ and $x_2$ should produce different outputs
		- there may be some inputs that produce the same output, but that can only be found with a brute force search
		- real example: passwords are stored as hashes. it can be trusted that two passwords wont be stored as identical hashes. Or a different password can be entered and pass because it produces the same hash.
	- Collision Resistance
		- hard to find two inputs $x_1$ and $x_2$ such that $h(x_{1})=h(x_{2})$
		- for collision resistance, you don't define the output as a constant. You look for any identical hash values
		- search space $2^{n/2}$ due to birthday paradox
	- Birthday Paradox
	![[Screenshot 2024-09-16 at 7.15.55 PM.png]]
	for Birthdays the search space is 365. What is the space for hash funct?
	- $2^{128}$ for bdays
	$$\frac {\pi(2^{128}-i)} {(2^{128})^t}$$

##### Hash Function Uses
- Digital Signatures
- PRNG
- Key Derivation

## Block Ciphers as Hash Function
- similar to block ciphers: divided into blocks
- we can use block ciphers as 
- uses compession function: compressed block is called $h_i$
![[Screenshot 2024-09-16 at 7.27.04 PM.png||500]]
- use message blocks as the "key"
![[Screenshot 2024-09-16 at 7.27.54 PM.png||500]]

### MD4
![[Screenshot 2024-09-16 at 7.28.49 PM.png]]
- like fiesel network, divided into 4 sections instead of 2
- block size 512 -> message digest size of 128
- 48 rounds
- retired in 2012: collision resistance broken

### MD5
![[Screenshot 2024-09-16 at 7.30.44 PM.png||500]]
- also broken

### SHA-1
 look at the notes Boy

# SHA-3
- does not use a feisel structure, uses a sponge type structure
- ![[Screenshot 2024-09-16 at 7.41.01 PM.png]]
- f function is very complicated
	- divides data into a cube
	- shuffles around like a cube!

## Applications
### Passwords
- we do not store passwords in plaintext. store them as (username, h(password))
- attacks: exhaustive search
	- dictionary attack: using a wordlist!
		- countermeasure: use SALTS
			- hash(password || salt)
		- repeatedly hash password for n times
	- attacks on the hash function itself
- Password based key derivation
	- $DK = PBKDF2(PRF, Pass, Salt, c, dkLen)$
	- $PRF$ - prng that take two inputs
	- c - number of iterations
	- dkLen - length of key generated
	- WPA2 standard
		- $DK = PBKDF2(HMAC - SHA1, passphrase, ssid, 4096, 256)$

### Authentication
#### Mac
- What if someone tries to alter or fake a message
- Tag Gen: $t \in MAC_{k}(m)$ t is tag(or MAC)
	- send tag with message
- Verification code: $b := Vrfy_{k}(m, t)$
	- verification runs mac on message
	- checks if the output is the same as the tag
- both sides have the same key!

![[Screenshot 2024-09-16 at 8.08.28 PM.png||500]]

- no privacy, used on top of encryption; message can be a ciphertext!

#### Hash Based MAC (HMAC)
idea: $MAC_{k}(m) = h(k,m)$
- vulnerable to message extension attack  with Merkle-Damgard construction
	- we are able to find valid message-tag pair for a concatted value $h(k|m_1)||m_{2}$

another: $MAC_{k}(m) = h(k,m)$

**MAC for authentication on a server**: DDOS attack
- attacker sends random authentication requests
- so many that it overloads the server


1. Mac and Encrypt
	1. ![[Screenshot 2024-09-16 at 8.17.34 PM.png||500]]
	2. used for SSH
	3. decrypt, get the plain t
2. Mac then Encrypt
	1. ![[Screenshot 2024-09-16 at 8.17.54 PM.png||500]]
3. Encrypt then Mac
	1. ![[Screenshot 2024-09-16 at 8.21.53 PM.png||500]]
	2. current version we are using
	3. secure against DDOS, does auth first
	4. check the hash of the ciphertext with the mac, then decrypt 