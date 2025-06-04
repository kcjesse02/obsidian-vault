collecting a power trace with chipwhisperer lib
``` python
%matplotlib notebook
import matplotlib.pylab as plt

wave = capture_trace() #captures power trace as a Trace object

plt.figure()
plt.plot(wave, 'r')
plt.show()
```

no instructions, just return
![[Pasted image 20230719120436.png]]
the first part is what idle execution looks like

repeated multiplication of variable by 2
```c
volatile long int A = 0x2BAA;
    A *= 2;
    A *= 2;
    A *= 2;
    A *= 2;
    A *= 2;

    A *= 2;
    A *= 2;
    A *= 2;
    A *= 2;
    A *= 2;

    A *= 2;
    A *= 2;
    A *= 2;
    A *= 2;
    A *= 2;

    A *= 2;
    A *= 2;
    A *= 2;
    A *= 2;
    A *= 2;
```
![[Pasted image 20230719120727.png]]

The code execution actually has varying power consumption

Changing it to a loop instead of subsequent multiplications
```c
volatile int i = 0;
volatile long int A = 0x2BAA;
    
while(i < 20){
    A*=2;
    i+=1;
}
```

![[Pasted image 20230719120223.png]]
 you can see that the execution period is longer


Expensive instruction: divide
```c
    volatile long int A = 0x2BAA;
    volatile int i = 0;
    while(i < 20){
        A /= 2;
        i+=1;
    }
```
![[Pasted image 20230719122222.png]]

lots of power consumption

>[! note]
> the way the chip whisperer measures power is voltage drop over a shunt resistor. This means that the power trace is inverted. low spikes are actually high power consumption