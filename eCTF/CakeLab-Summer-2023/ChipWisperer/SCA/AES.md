## Components
- key: value that data is x-ored with
- sbox: a fixed 2D array for the AES encryption scheme


```
1. key^input = index_sbox
2. sbox[index_sbox]
```
https://www.geeksforgeeks.org/advanced-encryption-standard-aes/#
 - For many of the chipwhisperer challenges, we are given an sbox, but for a real implementation, we will have to recover it from the code.
## Steps
#### SubBytes
- Where the sbox is used
- substitute bytes with values from sbox
#### ShiftRows
- left-circular shift
#### MixColumns
- Last round doesn't do MixColumns
- each column is multiplied with a different matric
#### Add Round Key
- Output of the previous stage XORed with corresponding round key.
![[Screenshot 2023-07-31 at 10.02.23 PM.png]]
To Decrypt: do operations in reverse

LiveOverflow video on breaking AES with differential power analysis(dpa): https://www.youtube.com/watch?v=FktI4qSjzaE

#### A hardware note about attacking AES with SCA
- the most straightforward attacks require the board we are working with to be in Electronic Code Book (ECB) mode.
	- this means that the blocks are unencrypted going into the the AES encryption, so it is relatively straightforward if you can identify the AES rounds
- Most of the time, systems use other modes, such as cipher block chaining (CBC) and Galois Counter Mode (GCM)
- It is possible to deal with these other modes, but it is best to start with ECB.