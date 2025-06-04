**Transport Layer**: provides *logical communication* between application processes running on different hosts
- exists at the edge of the network
- can provide host to host delivery
- sender: breaks messages into segments
	- sends down to Network Layer
- receiver: reassembles segments into messages, passes to the application layer
	- demultiplexes message up to app via socket
Uses TCP and UDP

##### Transport Vs Network Layer
*12 kids in Anne's house sending letters to 12 kids in Jose's house*
house = hosts
processes = kids
app messages = letters
transport protocol = Anne and Jose who demux to in house siblings
network layer protocol = postal service

### Multiplexing and Demultiplexing
- Multiplexing at sender: handling data and multiple sockets
- Demultiplexing at reciever: distributes data to multiple sockets
- uses the port numbers
ex: UDP demuxing
![[demux.png]]
- for TCP, include 2 port numbers, src ip, and dest ip (connection!)
![[tcpdemux.png]]
- mux and demux occur at all OSI layers
### TCP vs UDP

**TCP**: Transmission Control protocol
- reliable, in-order processing
	- info is guaranteed to arrive at the application in order
- congestion and flow control
	- prevents the link from becoming saturating
	- requires acknowlegements from hosts
	- congestion: sender end
	- flow: reciever end
- connection setup

**UDP**: User Datagram Protocol
- unreliable, unordered delivery
- best effort IP
	- data arriving in order is not garunteed
	- data arriving is not garunteed
- no garuntees on delays or bandwidth
- connectionless

##### More about UDP
What uses UDP
- streaming apps
- DNS
- SNMP
- HTTP/3
Fields of Header
- source
- dest
- length
- checksum
- and payload (application Data) :)
![[datagrammmmm.png]]
**Sender**: 
- SNMP messages
- adds UDP header
- creates UDP segement
- sends to IP

**Receiver**:
- receives datagram from IP
- checks checksum
- extracts application data
- demux message to 