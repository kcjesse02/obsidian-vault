- What is the structure of .elf files like?
- What different variants are there


## Static vs Dynamic Linking
https://stackoverflow.com/questions/311882/what-do-statically-linked-and-dynamically-linked-mean
Do you remember what Linking is you idiot?
- Its how a file references/obtains other code that it uses from other files or other libraries!
- Involves searching a bunch of paths (you need to set the `INCLUDEPATH` env variable)

**Dynamic**: Linking at runtime
- Doesn't require that all code is inserted into the binary at compile time. 
- Instead, upon running it, external libraries are "brought up" and kind of gestured to by the binary
- Remember the Global Offset Table from 4401? The GOT is how Dynamically linked libraries do this.
- Most linux programs/binaries do this. 
- Dynamically Linked binaries are vastly more common

**Static:** Linking at Compile time
- Code is "pasted" into the file directly
- Makes the binaries very large
- Used on embedded systems
	- because where is the file system
- Also provides more security, since external libraries can't be manipulated after its compiled
- No need to a GOT, just calls the address directly
	- also completely eliminates vulnerabilities associated with it. 