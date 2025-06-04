- Dividing Address spaces into smaller parts, pages
- Solves Internal and External Fragmentation
- Traditional Address translation requires fetching information from mem whenever we complete it
### Linear Page Tables
- Addresses have a page number, and an offset within the page
- takes up a lot of memory
- 
##### How we speed up address translation?
- add a Translation-lookup buffer to the MMU
- also called the **Address Translation Cache**: at the start of address translation, this is checked first to see if it contains a desired translation
	- its just cashing again
	- Stores `VPN`, `PFN`, `other bits`
	- 
	- TLB Hit: when the TLB already has the virtual page number, we can directly go to physical memory
	- TLB Miss: If the TLB does not have the thing in it. We have to do slow address translation
	- 
#### TLB Control Flow
```
VPN = (VirtualAddress & VPN_MASK) >> SHIFT
(Success, TlbEntry) = TLB_Lookup(VPN)
if (Success == True)   // TLB Hit

    if (CanAccess(TlbEntry.ProtectBits) == True)
        Offset   = VirtualAddress & OFFSET_MASK
        PhysAddr = (TlbEntry.PFN << SHIFT) | Offset
        Register = AccessMemory(PhysAddr)

    else
        RaiseException(PROTECTION_FAULT)

else                  // TLB Miss
    PTEAddr = PTBR + (VPN * sizeof(PTE))
    PTE = AccessMemory(PTEAddr)
    if (PTE.Valid == False)

        RaiseException(SEGMENTATION_FAULT)
    else if (CanAccess(PTE.ProtectBits) == False)
        RaiseException(PROTECTION_FAULT)
    else

        TLB_Insert(VPN, PTE.PFN, PTE.ProtectBits)
        RetryInstruction()
```
## Example: access an Array
![[Screenshot 2023-02-01 at 1.08.47 PM.png]]
- VPN: page id or Number, represented by the most significant bits
- TLB fits one page
- Hit Rate: 70%
- Miss Rate: 

```
VPN = (VirtualAddress & VPN_MASK) >> SHIFT
(Success, TlbEntry) = TLB_Lookup(VPN)
if (Success == True)   // TLB Hit

    if (CanAccess(TlbEntry.ProtectBits) == True)
        Offset   = VirtualAddress & OFFSET_MASK
        PhysAddr = (TlbEntry.PFN << SHIFT) | Offset
        Register = AccessMemory(PhysAddr)

    else
        RaiseException(PROTECTION_FAULT)

else                  // TLB Miss
    RaiseException(TLB_MISS)
```
OS Handled TLB Control Flow Algorithm

#### Handling TLB misses
- Hardware Handled
	- Hardware knows where the page tables are in memory, and what format
	- Older Architecture
- Software Managed
	- You guessed it, theres a trap-handler
	- Hardware raises an exception and OS takes over
	- There is a trap handler specifcally for handling TLB misses
### Context Switching
- There is only One TLB per machine
- There are multiple processes running on the machine
- we need to make sure another process doesn't override page information from another process
- Each address space has an id (same as processes)
 ![[Screenshot 2023-02-01 at 2.36.50 PM.png]]
 ![[Screenshot 2023-02-01 at 2.38.11 PM.png]]


# Smaller Page Tables
- How do we make them smaller
- Simple Solution: bigger pages
	- leads to waste within each page: **Internal Fragmentation**
	- some architectures now support multiple page sizes
- Paging and segments
![[Screenshot 2023-02-02 at 12.55.47 PM.png]]

- Most of the page table is unused!!
- On a TLB miss, the hardware uses the seg bits to choose base-bound pair, indicating table size
 ![[Screenshot 2023-02-02 at 12.58.00 PM.png]]
 - VPN is still the page address
```
SN           = (VirtualAddress & SEG_MASK) >> SN_SHIFT

VPN          = (VirtualAddress & VPN_MASK) >> VPN_SHIFT //IMPORTANT
- 
AddressOfPTE = Base[SN] + (VPN * sizeof(PTE))
- finds the entry.
```
- Don't forget to check for 
- Calculate physical address: `PhysAddr = (PTE.PFN << PFN_SHIFT) | Offset`
	- PFNshift would be 4 if the offset is 4 bits
- Translations are very slow. How do we speed them up
### TLB
	- Translation lookaside buffer (TLB) its a cache its just a cache
		- Keep track of recently looked-up pages
		- What should we store
- What does a page table entry look like? (put picture here)
### Multi-level page table
![[Screenshot 2023-02-02 at 1.03.02 PM.png]]
- Table containing other tables!
- Higher time cost on a miss
- example of space-time trade off
- two lvl page table
	- page directory entries point to sections of the page table
	- says whether they are allocated or not
Page Directory Index
Page-table index
![[Screenshot 2023-02-02 at 1.23.48 PM.png]]

### More than two levels
- Requires larger VPN id
![[Screenshot 2023-02-02 at 1.28.02 PM.png]]
- 

# Mechanisms
- **What if an address space doesn't fit into physical memory?**
	- Larger memory is normally slower
	- The Hard disk drive is a good place to store lots of things
- Why do we want an address space?
	- For convenience, allows programs to be written without having to worry about using too much memory
	- Or, at least we want the **illusion of a large address space**
- Memory Overlays: required coder to manually move code in and out of specific memory locations
- Swap space: used for transitioning pages in and out of memory
![[Screenshot 2023-02-05 at 3.50.40 PM.png]]
- present bit: a bit in each page table entry that is set if the page is in physical memory
	- Helps the hardware know status of page
##### Page Faults
- 
##### What if memory is full?
- Simply replace old page with new page
- Page replacement policy: how the OS makes a decision on which page to remove to add a new page

## Smaller Page Tab;es
- How many pages fro a 32-bit address space? assume 4KB page tables
	- 2^12 address, 12 bits for Offset
	- 2^20 addresses!!
- Larger pages:
	- Table size decreases by same factor as page size increases. TLB is now more effective as well
	- But, lots of internal fragmentation
	- Only a small portion of address space is being used
- Multi-level page tables
	- Logic: majority of linear page table is not going to be used
	- Page Table for our page table: The Page Directory
		- Allocates and deallocates portions of the page table
	- Free Up Wasted Space
	- Add page directory -> stores subset of total pages
## Address Translation With MLPT
1. Calculate the VPN: `VPN = {VirtualAddress & VPN_MASK} >> SHIFT`
2. Check the TLB `{Success, TlbEntry} = TLB_Lookup(VPN)`
	1. make sure memory access is legal
	2. If legal, preform and access memory: `PhysAddr = (TlbEntry.PFN << SHIFT) | Offset`
	3. Page table index: How many page table entries fit in a page.
3. If TLB Miss, look through the different levels of the page table for the entry
4. Access the PTE
	1. Ensure that the PTE is valid
	2. Also ensure that the memory access is legal
![[Screenshot 2023-02-02 at 1.29.38 PM.png]]
- More time is spent on a miss
- But TLB should theoretically hit more
- Its a Tradeoff(tm)
### More than two levels
- Add more space to the VPN, sections for the index of each level