Can display electrical signals in waveform.
- useful to visualize the waveforms
- 
**Attacks**
- Can be used to track uart information
- You can manually decode the information!
	- If there is a hardcoded key, weak entropy key, or non-encrypted data, we can get that data being sent!
- Side Channel Attacks!
	- We can only visualize 2 AES rounds with the chip whisperer
		- also easier to use than the chip whisperer
	- Would allow us to visualize all 10-14.
	- should be able to capture 30-40k samples
	- a full AES encryption is 200k samples - upper limit
**Debugging**
- Can be used to monitor uart information
	- see the actual Data passed through
- could've been used to track the uart bug

### Pros
- Used in ECE labs
- easier to use
- Some oscilloscopes have logic analyzers built in
- The tool for analyzing analog waveforms
### Cons
- More general
- Logic analyzer is what we want, is designed specifically to analyze digital signals that encode data
- annoying to manually decode data
- We might not want an ocilliscope if we can use ones in the ECE lab...
- Can be very expensive: ~$2000 - ~$10000

Kind used in ECE labs (old model): Tektronix TDS 2004B