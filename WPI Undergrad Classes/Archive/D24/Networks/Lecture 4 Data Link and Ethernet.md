## Framing
![[yet another osi model.png|450]]

#### Sentinal Based
![[sentinalframing.png]]


Uses the beginning seq to indicate arrival of ethernet packet
- Problem: Beginning seq could be in the payload

- More common for Ethernet

#### Counter Based
![[counterframing.png]]
- include count in payload
- commonly used in TCP/IP headers, less so in ethernet
- Problem: What is length is corrupted
	- CRC: Cyclic Redundancy Check. This can detect an error in the packet
## Medium Access
What medium do we use at the Datalink layer?
#### Ethernet
- CSMA/CD
	- Carrier Sense: Can determine if another transmission is already going on
	- Multiple Access: multiple access to different hosts
	- Collision Detection
- Frame format
![[ethernet frame.png]]
Addressing
- 48but address assigned to each NIC
- Broadcast: all 1s
- Multicast: first bit is 1
Length: 2500meters

Transmission Algorithm
- If line is idle, transmit immediately
- If line is busy, wait until transmission is finished

Collisions
![[error check.png]]

How to handle collisions
- send 32 bit JAM, then stop transmitting
	- attempts to interrupt so other party will stop transmitting
- Delay and try again
	- 1: 0 - 51.2microsec
	- 2: 0, 51.2, or 102.4 microsec
	- nth time: k \* 51.2microsec
	- Ethernet is bad at fairness and collisions

Why do we use it
- cheap
- easy to manage
- not used for multiple access and long distance really

##### Ethernet Networks
- Bus topologies

#### Wireless
- radio or infared
- Avoiding interference
	- Frequency Hopping
	- Direct Sequencing
##### Ad Hoc Wireless
- Multiple Access Collision Avoidance 
	- Request to send (RTS)
	- Recipient sends a clear to send signal (CTS)
- Possible scenerios
	- you see CTS but you aren;t the sender?
- Handshaking process
	- Mobile device sends probe
	- repiles w/ probe response
	- sends with 
- Beacons
	- Short tranmissions from access points to inform possible uses resources are available

#### Token Ring
![[ring.png]]

- ring of computers
- signal goes in the same direction
- Multi-station access unit: dynamic relays, like star topology
- Access Control
	- has 24 bit token
	- sent in frame header
## Switching and Forwarding
#### Packet Switching
- using switching to determine the destination of a packet
![[Packet Switching.png|450]]

- large networks can be constructed with switches
- can connect hosts/switches via point to point links



#### Datagram switching
![[datagram switching.png]]

- connectionless/immediate service
- Never knows when the network is overloaded
- Datagrams may take different routes to the same host

#### Source Routing
- switching info in header of packet
- makes packet very large
- not great for large networks
- Forwarding info should be in the switches

#### Preformance Impacts
- machines w/ multiple network cards
- Direct memory access
- I/O memory bandwidth limiting network
- small packets require more header lookup
- specially designed switches to avoid 
## Switch Design
Fabric: take packet from input port to output port
- Shared bus
- Shared mem
- Crossbar
	- every input direct channel to output
![[crossbar.png|300]]
	- High cost
- Self-routing
	- heirarchy of crossbar switches
![[self routing.png|300]]
- path isn't completely pre determined

#### Circuit Switching
- seeking out and establishing a physical copper path from end-to-end
	- for landline
- requires need to set up physical connection before information transfer takes place


a) curcuit switching.  b)packet switching
![[circuit v packet switching.png]]

Connection oriented virtual circuits
- path is not predetermined
- but still connection oriented

Store and Forward Networks
- Routers implement this
- intermediate processors store and inspect data along the path


#### Message Switching
- Store and forward network where the block of transfer is a complete message
- messages can be quite large
	- can cause buffering problems
	- 


## Error Detection

## Lab 2 table

| Bridge Port | Bridge interface, Mac   | Hostname, Mac           |
| ----------- | ----------------------- | ----------------------- |
| 1           | eth1, 02:2c:57:76:b2:c8 | eth1, 02:0a:ea:94:0c:3e |
| 2           | eth2, 02:f7:cf:61:ed:76 | eth1, 02:66:8f:6b:5c:12 |
| 3           | eth3, 02:1c:ac:0c:96:28 | eth1, 02:99:18:7a:bb:76 |
| 4           | eth4, 02:72:7e:8c:f1:a5 | eth1, 02:1d:3a:d6:44:d3 |

