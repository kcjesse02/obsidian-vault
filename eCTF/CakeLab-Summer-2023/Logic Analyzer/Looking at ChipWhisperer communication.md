


## UART Transmission for Incorrect Password Entry
the chipwhisperer interfaces with the firmware of the target board through uart. It will send it certain characters to activate certain functions. 

For example, the first piece of code used to test sends the letter 'p' over UART to prepare the board to accept a password, and then sends the password over the next uart transmission:

This program sends an incorrect password and prints the result
```python
scope.arm()

#incorrect password
pw = bytearray(b'l'*5)

#send data
target.simpleserial_write('p', pw)

#if the scope returns 1, then no data is captured and something is wrong
ret = scope.capture()
if ret:
    print("Timeout")
trace = scope.get_last_trace()


val = target.simpleserial_read_witherrors('r', 1) glitch_timeout=10)#For loop check
valid = val['valid']
if valid:
    response = val['payload']
    raw_serial = val['full_response']
    error_code = val['rv']

print(val)
```

result
```
{'valid': True, 'payload': bytearray(b'\x00'), 'full_response': 'r00\n', 'rv': 0}
```
from 
Rx (From CW) 
![[Screenshot 2023-07-14 at 6.54.20 PM.png]]

first byte: 0x70 (p)
next 10 bytes: 0x3663 (x5) (6c (l))


Tx (To CW)
this should be the data sent in this snippet of code from the firmware:
```c
// param 1: command
// param 2: buffer size
// param 3: location of buffer
simpleserial_put('r', 1, (uint8_t*)&passok);
```
![[Screenshot 2023-07-14 at 6.53.30 PM.png]]

0x72 - r (read command)
0x7A - z (acknowledge)
I can see that the r and z are both followed by two zeros (probably represents 0x00), which makes sense because the incorrect entry of a password returns a 0 for false.

How does this change with a successful password entry


## Correct Password entry
result
```
{'valid': True, 'payload': bytearray(b'\x01'), 'full_response': 'r01\n', 'rv': 0}
```

Rx
![[Screenshot 2023-07-17 at 1.09.08 PM.png]]
- pretty much the same structure, just with different values for the 5 chars corresponding to the correct password, "touch"

Tx
![[Screenshot 2023-07-17 at 1.11.15 PM.png]]
- The values after the z are unchanged, perhaps that just communicates that the firmware has completed its task and the ChipWhisperer can send the data to the computer.
- the first part does appear to have

## Possible Next Steps?
- Find something to possibly preform a replay attack on
	- That was the idea behind using the eCTF boards but they possibly could be broken
	- Find another piece of Technology
- Look at another protocol on the ChipWhisperer board
	- possibly the microusb between the computer and the chipWhisperer
	- I tried this already, but it will require a lot of precision. It can be done



*For potential later exploration*
## USB Protocol
A USB has two data transfer pins `D+` and `D-`. 

The values between these two pins determine the value of the signal. 
*with D+ pullup which is most common for modern USBs, J and K states will be inversed for D- pullup*

| signal | D+   | D-   | Description                                |
| ------ | ---- | ---- | ------------------------------------------ |
| J      | high | low  | equivalent of 0 bit in signal              |
| K      | low  | high | equivalent of 1 in bit signal              |
| SE0    | low  | low  | indicates end of packet or detached device |
| SE1    | high | high | should never happen, an error              |

![[Screenshot 2023-07-14 at 10.30.32 AM.png]]
https://en.wikipedia.org/wiki/USB_communications#/media/File:USB_signal_example.svg

So, we need to find the D+ and D- pins in the microusb if we want to look at communication between the computer and the cw