Cryptology: includes two parts
- cryptography: designing secure system
- cryptanalysis: breaking secure system

Assumptions to make
- Kerckhoffs' Principle: Enemy knows everything about algorithm, except the key
	- or, security of the system must only depend on thekey
- Murphy's Law also applies, why should we perform cryptanalysis


#### Shannon Cipher
basic model for cipher

$$
\epsilon = (E, D)
$$
- $E$ = the encryption function
	- takes Key, $k$, and message $m$, produces ciphertext $c$
	- $c = (k, m)$
- What is Decryption?
$$
m = D(k,c)
$$
Decryption must produce the same value of the $m$ inputed into the Encryption function
$$
D(k,c) = D(k, E(k,m)) = m
$$


#### We need three sets
$K$ - the key set
$M$ - the message set
$C$ - the ciphertext set

## Defining Secure Cipher
- cannot determine $m$ from $c$, without $k$
- ciphertext should appear totally random
- you should not be able to tell anything about the algorithm

#### Perfect Security
$| Pr (𝐸 𝒌, 𝑚0 = 𝑐) − Pr (𝐸 𝒌, 𝑚1 )= 𝑐 | = 0$
- equal liklihood of each bit being equal to 1 or 0
	- completely unpredicatble
- no such system exists
- except one-time pad

##### One time pad
- must all be the same size

Shannon's Theorem
- Key space should be larger or equal to message space
$K \gt = M$

#### Computational Cipher
$| Pr (𝐸 𝒌, 𝑚0 = 𝑐) − Pr (𝐸 𝒌, 𝑚1 )= 𝑐 | = \epsilon$
$\epsilon$ = very very small value
- requires more flexible definition of security
- allowed to leak the length of the message

## Attack Scenerios
1. Ciphertext only attack: Attacker has access to ciphertexts
2. Known plainttext attack: has ciphertext message pairs
3. Choosen plaintext attack: attacker has an encryption device and can choose message and observe the output
4. Choosen ciphertext attack: has access to decryption device. Can decrypt choosen ciphertext

Historical cipher example: Ceaser cipher
- shift alphabet over by a given amount
- 25 possible keys: the amounts its shifted over on
- infinite message space

Substitution cipher
- replace each letter according to substitution table
- Key space: pretty large
- do frequency analysis of letters
	- use known frequency of letters in the english language

Vigenere cipher
- combine many different ceasar ciphers
- have a key word
- and a message
- use the key to determine each level of shift to use for each character
![[Screenshot 2024-08-26 at 7.53.05 PM.png|500]]

- less vulnerable to frequency analysis


# Basic Symmetric Key Encryption
![[Screenshot 2024-08-26 at 7.59.20 PM.png]]

- ciphertext is sent through insecure channel
- key is exchanged through secure channel
	- secure key is asymmetric

### Stream Cipher
![[Screenshot 2024-08-26 at 8.03.11 PM.png]]
- vernum cipher
- symmetrically psuedorandomly generated keys

###### Stream vs Block Ciphers
- stream ciphers encrypt one bit at a time
- block ciphers encrypt in chucks of a defined size

### Asymetric Encryption
- Public-key encryption: confidentiality
- Digital Signatures: Authentication
- 