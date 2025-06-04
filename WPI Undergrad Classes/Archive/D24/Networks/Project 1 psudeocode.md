## Task
1. Map
2. inet_ntoa, inet_ntop, ntohs, ntohl

```
inet_ntoa: converts from in_addr struct to byte for ip address
inet_ntop: converts from in_addr struct to string for ip adresses

ntohs: for short integers; converts from network endianness (big) to machine endianness (little)
ntohl: for long integers
```


### Net/netinet
`user/include/netinet`
```c
#include <netinet/ip.h>
#include <netinet/if_ether.h>
#include <net/ethernet.h>
#include <netinet/tcp.h>
#include <netinet/udp.h>
#include <netinet/inet.h> //allows you to find MAC->ip
```


### pcap_loop
![[Screenshot 2024-04-02 at 2.51.21 PM.png]]ethernet header: 14 bytes
ip header: variable, found in the Hlen field, 20 or more
UDP: 


### MAP data structure
https://en.cppreference.com/w/cpp/container/map

example: 
- unique key is addresses :D
- value is the number of times it appeared



