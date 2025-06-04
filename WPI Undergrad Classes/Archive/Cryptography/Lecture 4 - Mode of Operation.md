Galois Field - all dimensions are the same; this is what we used last class for AES

#### Padding
- ways to encrypt data that does not divide evenly into block size
- all zeros
	- how do we distinguish between zeroes at the end of the data 
- pad with single 1: append 1 and then all zeros
	- does not garnet the original message is preserved
- length strngthening: append the length of the data in the block to the reserved space at the end and then all zeroes
- combo single 1 and strengthening
	- what if there are not enough blocks for the length?
	- add another block for length
	- always reserve 10 bits for padding in block

**Modes of Operation**: extra steps to add more security with Block Ciphers
Questions for MoO
- can we parallelize operations
- how will errors propegate
### Electronic Codebook (ECB) mode
- Deterministic ciphertext
- rule: you can create a lookup table to save time on repeat operations. Plaintext and ciphertext pairs. 
![[Screenshot 2024-09-16 at 6.17.42 PM.png||500]]\
Potential issue with EBC
- what if you are encrypting an email?
- first block will always be the indicator of an email header
- attacker can distinguish emails from other types of data!

### Cipher-Block Chaining 
- ![[Screenshot 2024-09-16 at 6.19.35 PM.png||500]]
- xor previous ciphertext with 
- initialization vector must always be different for each \

Encryption: $C_{i}= E_{k}(P_{i} \bigotimes C_{i-1})$
Decryption: $P_{i}= D_{k}(C_i)\bigotimes C_{i-1}$
- you can paralellize decryption, but not encryption
- error propagation

### Cipher Feedback (CFB)
![[Screenshot 2024-09-16 at 6.34.04 PM.png||500]]
- encrypt previous cipher text then 
- decryption: encrypt previous ciphertext, xor with current ciphertext block
- Encryption: $C_{i}=E(C_{i-1})\bigotimes P_i$
- Decryption
- Paralell?
	- you only need two adjacent ct blocks
- Error prop
	- encryption and decryption will cancel eachother out

### Output Feedback () (Stream Cipher Mode)

![[Screenshot 2024-09-16 at 6.42.33 PM.png||500]]
- Generate masking vectors with block cipher
- Encrypt: $C_{i} = E(O_{i-1}) \bigotimes P_i$
- Paralell?
	- only paralizable if you create masking vectors 
- Errors
	- wont propagate

### Counter Mode (CTR)
- use counter as an input
![[Screenshot 2024-09-16 at 6.46.33 PM.png]]
- highly paralellizable
	- each block has its own counter that can be calculated without 
- no error propagation