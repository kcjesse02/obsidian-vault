# Routing Algorithms
- The "Best" path looks different for different implementation
- most of the time, we want to implement the policy

Link state algorithm: all routers have complete topology info
Distance Vector: iterative process of computation exchange info with neighnots

static: router changes slowly over time
dynamic: routes change more quickly

#### Dijstras Algorithm

#### Bellman Ford


## NAT: Network Address Translation
- translates external packets from outside a network

- Outgoing datagrams: replace src ip address of outgoing packets with NAT IP


NAT is not needed with IPv6

Contreversy: NAT is Layer 3 device, should not be dealing with ports, violates end-to-end argument