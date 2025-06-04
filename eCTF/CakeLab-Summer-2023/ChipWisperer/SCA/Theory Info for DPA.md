*Notes taken from the hardware hacking handbook*

Most of the time, we want to measure change in voltage across a capacitor. This will allow us to detect weather the data is in a low or high state
- if current change is low-to-high, we should see positive current flow
- if it changes high-to-low, we should see negative current flow.

Hamming Weight: the number of 1 bits in a data value
- power will be different for data of different hamming weights

voltage value vs hamming weight
![[Screenshot 2023-07-26 at 12.38.14 PM.png]]
voltage was measured from resistors off of Vcc

## Example
Scenario
- circuit xors each input byte with some unknown secret byte-long constant. Then, it uses a lookup table to transform that value
- (this is one round of AES?)
![[Screenshot 2023-07-26 at 12.57.59 PM.png]]

- we get different power traces for different bytes sent and we can use these to recover the secret key!![[Screenshot 2023-07-26 at 1.42.21 PM.png]]


### DPA attack
Why Power Analysis and not brute-forcing?
- Power analysis is useful when the output is not available.
- we can use power traces to guess the output
- can break larger keys because we gain confidence the more we test

### Leakage assumption
- we have to assume that the system leaks all known hamming weights
- In the example we are using, we only know the *total* power consumed and thus can only know the *total* Hamming Weight.

### DPA algorithm
*For one guessed byte*
```
diffarray = []
1 each key guess i of the secret key in range {0x00, 0x01, ..., 0xFE, 0xFF}:
    zerosarray = new array
    onesarray = new array
    2 for each trace d in range {0,1, ..., D-1}:
        3 calculate hypothetical output h = lookup_table[i XOR p[d]]
       4 if the LSB of h == 0:
            5 Append t[d] to zerosarray[]
        else:
            6 Append t[d] to onesarray[]
7 difference = mean(onesarray) – mean(zerosarray)
    append difference to diffarray[]
```
1. iterate over all possibilities for byte guessed
2. loop over power traces for each guessed byte
3. generate a hypothetical output, the result that would be produced if the guess was correct hw_lookup(byte_guess ^ current trace)
4. look at the LSB in the hypothetical output
5. add to one_list if the LSB is one
6. add to zero_list if the LSB is zero

##### If the bit is incorrect
the mean of the one and zero list should be about the same. The difference between them will be close to 0.

##### If the bit is correct
there will be a power difference between where the LSB is one and where it is zero. The diff will be significantly greater than zero.

![[Screenshot 2023-07-27 at 10.41.10 AM.png]].
![[Screenshot 2023-07-27 at 10.41.37 AM.png]]

It is also notable that the surrounding of the incorrect bytes decreases
![[Screenshot 2023-07-28 at 11.08.53 AM.png]]
LiveOverflow video on breaking AES with differential power analysis(dpa): https://www.youtube.com/watch?v=FktI4qSjzaE

## Important Points
- Differential Power Analysis is a method to prune power trace data to identify the entry of correct vs incorrect key bytes.
- 


