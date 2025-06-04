### Recap Layer 2 Data link
MAC address: identifies a machine on a local network
- 48 bit addr, 6 groups of 2 hex digits
- a physical address on the device, the real address
	- first 3 groups are know as the OUI
	- specific to device (CPU) manufacturer
- Broadcast Address: FF:FF:FF:FF:FF:FF
IP address: identifies machine outside local network

### Layer 1
##### Repeater
- Layer 1
- takes a signal and retransmits it

##### Hubs
- multiple inputs and outputs that allow multiple devices to connect
- broadcasts to all devices connected


### Layer 2
##### Bridge
- Learns Mac Addresses
- Uses Contnent Addressable Memory (CAM) table to store port and MAC
- software frame forwarding
- Learns mac address upon *recieving* packet

##### Switch
- similar to a bridge
- every device connected gets broadcast messages
- every switch is on the same LAN

### Layer 3
##### Routers
- transmit packets over the internt
- use routing algorithms to decide the path packets take
	- uses a routing table to create a graph, with IP addr connected to an Interface


### Broadcast and Multicast

Broadcast: send to all but sender
Multicast
- better trick: use learning bridge method
- uses network traffic to assocoate host devices with LAN segments. One it learns, it will direct traffic to the correct area of network
- Uses Virtual LANs

#### Virtual LANs
- multiple logical LANs operating on the same physical network
- allows for isolation of different systems
- each VLAN has a different IP address
- By default, they do not talk to eachother
	- require a Layer 3 Network device: Router