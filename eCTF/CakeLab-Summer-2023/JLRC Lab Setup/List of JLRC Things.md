### Hardware
1. [[Logic Analyzer]]
	1. Used to decode signal protocol (UART)
	2. UB used this to conduct replay attacks
	3. Can be used to debug uart 
	4. Recommended by CMU/Lots of resources available: https://usd.saleae.com/products/saleae-logic-8
		1. Prices: $500(8 channels, 100MS/s), $1000(8 channels, 500MS/s), $1500(16 Channels, 500MS/s)
2. [[Oscilloscope]]
	1. Can be used to capture power traces for side channel attacks
	2. We can do this with the Chip Whisperer, but this is less difficult to set up
		1. plus, ECE classes give people experience with the oscilloscope.
	3. min 30k samples per capture, min 200MHz sample capture rate.
	4. Some nice ones have a logic analyzer built in
	5. Propaganda from Tektronix: https://www.tek.com/en/blog/which-oscilloscope-to-buy
	6. Ones used in the ECE lab: Tektronix TDS 2004B
		1. Those are discontinued when I search for them
		2. This one satisfies what we need: https://www.tek.com/en/products/oscilloscopes/tbs2000
			1. price range: $1,640 - $3,640
		3. This one does not include digital analysis tools
		4. This one does :)) : https://www.tek.com/en/products/oscilloscopes/2-series-mso
			1. Price Range: $1,900 - $2,870
3. [[Multimeter]]
	1. Useful to identify different ports of pins if we are attacking a piece of hardware we don't have a lot of information on.
4. Soldering Iron
	1. used to securely connect wires to side-channel analysis/glitching setup.
5. Resistors
6. Screwdrivers
7. Circuit-board holders
### Books/Resources
1. The Hardware Hacking Handbook - https://www.amazon.com/Hardware-Hacking-Handbook-Breaking-Embedded/dp/1593278748
	1. We have a pdf in bakers Handbook!

