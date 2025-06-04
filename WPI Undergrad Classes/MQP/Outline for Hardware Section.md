
1. can you explain the hardware challenges on a high level (think talking to someone who doesnt know cybersecurity)
	1. Some context: going in, we only had a vague idea of what challenges would look like, but nothing concrete was formulated
		1. We were doing this from a cs angle
		2. a lot of time was spent getting that concrete ideas of what challenges could look like
	2. Overall Design - My main contribution
		1. Focus on the challenge manager 
	3. General Challenge walkthrough
		1. ![[Pasted image 20250411103737.png]]
		2. Understanding the target
		3. Getting the Firmware
			1. Requires some data connection to the board, CPU or Flash
			2. sometimes, targets will have exposed UART or JTAG pins that allow for a connection that can control the device!
			3. Use physical attacks like glitching to bypass read protections!
			4. Read code directly off of non-volitile flash memory!
		4. Bypassing Firmware Encryption
			1. key may be in flash
			2. hijack external cryptographic processer to decrypt the firmware and collect the output
		5. Bug Hunting/ Reverse Engineering
			1. look at the code and find any exploits (buffer overflows ect)
		6. Running an exploit
			1. create an exploit on the 
		7. Its difficult to 
	4. System Design
		1. ![[Pasted image 20250411103324.png]]
		2. 
2. results
	1. I created a framework of what a lot of these challenges could look like
	2. 
	3. I started to create a hardware chalenge
3. deep dive a bit more into the technology you used to make these challenges (both hardware and software)
	1. Raspberry Pi Pico
		1. Includes ARMv6 architechture.
			1. has a Memory Protection Unit.
		2. Requires and SWD connection to debug.
		3. limited security features
	2. Platform IO
		1. PlatformIO is a toolchain manager and an open source plugin for VScode (and other IDEs). It lets you compile the same code for multiple archtectures and platforms
			1. It helps you avoid needing to download a proprietary IDE for every different piece of hardware you want to compile for.
		2. Uses an version of the arduino library to control the
			1. 
	3. Some other resources I used to learn about Embedded Devices and attacks
		1. Microcontroller exploits book
		2. Matt Brown's youtube channel

