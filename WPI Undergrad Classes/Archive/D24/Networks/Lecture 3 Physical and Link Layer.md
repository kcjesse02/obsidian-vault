## Moving Bits across the Wire
![[overawire.png]]
modulation rate: number of times/sec the signal changes its voltage
baud: number of changes per second
bandwidth: range of freq that is passed through a channel
- affects transmission speed
channel capacity: amount of data that can be transmitted

![[modulation rate.png|550]]
Modulation rate

Signal: electric or electromagnetic encoding of data
signaling: act of propgating a signal across a stable medium
- Can be analog(varying values) or digital(1 or 0)
- a digital signal is a sequence of voltage pulses over an electical wire medium

### Multiplexing
- combing multiple analog inputs into one line
![[multiplexanddemultiplex.png]]
in b) signals are multiplexed and dimultiplexed
- These are duplex channels
	- can both transmit and receive
- can be denoted by Frequency
	- FDM: Frequency Division Multiplexing
	- 
- can be denoted by Time
	- TDM: Time Division Multiplexing
	- each channel gets its own time
	- like a scheduler

![[multiplexingtypes.png|500]]

also wavelength division (for long-haul fiber!)
![[fibermult.png|600]]

### Cabling
- Fiberoptic
- Category 5/5e/6
	- Twisted copper pair wire
	- Cat 6 is most common
	- pulling the wires is the most expensive part
	- Standard ethernet cable
- Category 7 and 8 are out there too
	- the conduit is the only thing that has to be upgraded
	- upgrading from 6->7 does not require pulling new wires
- Leased Lines
	- renting lines from telephone company
	- existing cables

##### Speed
![[cablespeed.png|600]]

#### Carier cables 
Twisted pair cable: most common cabling for 

guided media: signals propogate over solid media, wires
unguided: signals propogate freely, radio waves
