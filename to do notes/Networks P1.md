1. Start with examining the TCP dump header for basic statistics.
2. Use an Ethernet header to print out the associated addresses
3. Determine whether ARP or IP is in use. For ARP, print out the protocol fields.
4. Print out the IP sources and destinations.
5. Determine whether a packet carries UDP and, if so, print out the UDP ports.
6. Rather than print per-packet statistics, store things in a data structure to print final statistics. (Hint: look at the C++ STL map data structure).
    
7. Print out the final statistics using your populated data structures.