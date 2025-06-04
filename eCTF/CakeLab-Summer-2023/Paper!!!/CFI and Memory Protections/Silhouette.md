An extension of freeRTOS that allow for control-flow integrity

pointers are stored in some memory safe region where they can't be overwritten by the user of the system

### Parts
1. Write Protected Shadow-Stack
	1. second stack stored in isolated region of memory
		1. isolated using the ARM privileged/unprivileged modes
	2. How to protect it?
		3. in general systems: just hide it! The way ASLR uses uncertainty
		4. hiding doesn't work on embedded systems
		5. Use hardware privilege levels
			1. configure corse-grain mem regions to read-only for unprivledge code
2. Forward Branch CFI
	1. protects call instructions
	2. checks call instructions that use registers (that can be changed)
	3. add some instructions before indirect branch (called instrumentation)
		1. labels legal jump locations
		2. prevents from jumping to locations with the wrong label
		3. How to add label: add additional instruction at start of functthat acts as a label
3. Store hardening
	1. all code is privledged mode
	2. transforms store instructions that should be in unprivledged to unprivileged store instructions
	3. ensures that they can't access privileged sections (the shadow stack)
	4. scans for privileged instructions
4. MPU configuration
	1. define 8 regioins
	2. read-write-ex permissions for regions and priv/unpriv mode
	3. how to configure mpu -> write value to an MPU register


>[!Questions]
> 1. could a shadow stack be described as a GOT with RELRO on for return addresses on the stack?
> 2. What are store instructions in ARM?
> 3. What is the HAL library?
> 4. 