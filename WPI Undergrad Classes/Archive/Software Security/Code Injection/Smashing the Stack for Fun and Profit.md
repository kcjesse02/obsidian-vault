[[Stack3]]
## Shellcoding
```
<-bottom of memory
DDDDDDDDEEEEEEEEEEEE EEEE FFFF FFFF FFFF FFFF top of memory-> 89ABCDEF0123456789AB CDEF 0123 4567 89AB CDEF 

		buffer                sfp   ret    a     b     c

<------ [SSSSSSSSSSSSSSSSSSSS][SSSS][0xD8][0x01][0x02][0x03] ^|
		|____________________________|
top of stack                                   bottom of stack
```
- the return address should be overwritten with the address to the top of the buffer (why [[Stack4-64]] checks to see if ret is a stack address)
- the buffer should contain the code we want to inject
