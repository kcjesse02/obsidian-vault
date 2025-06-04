## Quiz 1 Review
1. Packets are the most basic unit sent over network
2. Copper wire is a common material network transmission technologies use
3. WPI sends out a weather alert to the entire campus
	1. broadcast transmission
	2. not multicast, multicast requirements are too specific, must be on the same network
3. DSL vs Cable
	1. DSL uses phone line to connect and transmit data

# Network Layer Model

![[OSI communication.png|600]]
Application: Where the application communicates with the network model
Transport Layer: transports data from endpoint to LAN
Network Layer: routing of data from source to destination (outside of lan)
Link Layer: transfers data between neighboring network elements
- This class will be about understanding what each of these layers does
- Each layer will transform and add data to the packet to complete what it needs to do.
- ex: network layer has an address with the data, physical layer just represents data as raw bytes
- We use the TCP/IP model
	- First 3 layers are combined

presentation layer: allows applications to interpret meaning of data
session layer: synchronization, check-pointing, recovery of data exchange
- these all occur in application layer in TCP/IP

### Why Layered Design
- provides abstraction
- simplifies design process
- grouping functions of complex progress
- modularity
	- makes updating and maintaining code easier


![[IP-TCP model.png]]

![[OSI.png]]

## Protocol
Application Layer:
- FTP, SMTP, HTTP
Transport Layer
- TCP (has error checking, gets a receipt back), UDP(no error checking)
- TCP has packets
- UDP has datagrams
Network:
- Routing protocols, IP
Link: Data transfer between neighboring network elements
- PPP, ethernet

### Internet Protocol
- Provides best effort service
	- packets may be lost
- Kess Core internal operations simple
- Operate over any network
- Designed to scale
- IP addresses Used for routing
	- IPv4: 32 bits
	- IPv6: 128 bits
- we give IP addresses a mnemonic
	- www.cs.wpi.edu
	- Heirarchical names
	- use DNS to translate between IP address and written name
#### More Info about IP addr
- IPv4
	- first 6 numbers: network id
	- last 4 numbers: host id
- IPv6
	- represented by a group of hex digits separated by colons
	- leave out groups of zeros by simply writing between colons
```
2000:fdb8:0000:0000:0001:00ab:853c:39a1

... becomes ...

2000:fdb8:::1:ab:853c:39a1
```

## What is protocol

HTTP and TCP
![[HTTP example.png]]

Ports: 
- used for utilizing different services on the same host
- certain ports expect, or listen for, certain types of communication

### Internet: Network of Networks
- Teir 1 ISP: big companies
	- cover
- Tier 2 & 3 ISPs
	- last hop
	- closest to end system
- Local ISP
	- customers of Teir 1 isp
![[ISP diagram.png]]

level 3 vs Comcast
- Netflix is transmitting data to customers via comcast network
- Comcast wants Netflix to pay
- Netflix already pays Tier 1 ISP fees
# TCP Sockets

#### IPv4 Socket Structure
```c
struct in_addr {
  in_addr_t   s_addr  /* 32-bit IP address */
};             /* network byte ordered */

struct sockaddr_in {
  uint8_t      sin_len;    /* length of  structure (16) */
  sa_family_t sin_family; /* AF_INET */
  in_port_t   sin_port;   /* 16-bit TCP or UDP port number */
               /* network byte ordered */
  struct in_addr  sin_addr;     /* 32-bit IPv4 address */
                     /* network byte ordered */
  char  sin_zero[8];       /* unused */
};
```

#### Sockets on the OS Level
- OS switches to kernel mode to manage communication protocols
![[socket functions.png|600]]

lots of system calls: TCP system calls
![[socket function calls.png|600]]
- reminder: check the return of the functions

UDP socket calls
![[more socket functions.png]]

TCPEchoClient.c
```c
#include <stdio.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

#define RCVBUFSIZE 32  
void DieWithError(char *errorMessage); /* Error handling function */

int main(int argc, char *argv[])
{

    int sock;
    struct sockaddr_in echoServAddr;
    unsigned short echoServPort;
    char *servIP;
    char *echoString;
    char echoBuffer[RCVBUFSIZE];
    unsigned int echoStringLen;
    int bytesRcvd, totalBytesRcvd;

    if ((argc < 3) || (argc > 4))
    {

/* Socket descriptor */
/* Echo server address */
/* Echo server port */
/* Server IP address (dotted quad) */
/* String to send to echo server */
/* Buffer for echo string */
/* Length of string to echo */
/* Bytes read in single recv()

   and total bytes read */
/* Test for correct number of arguments */

exit(1); }

servIP = argv[1];
echoString = argv[2];

/* First arg: server IP address (dotted quad) */
/* Second arg: string to echo */

/* for printf() and fprintf() */  
/* for socket(), connect(), send(), and recv() */ /* for sockaddr_in and inet_addr() */  
/* for atoi() and exit() */  
/* for memset() */  
/* for close() */

/* Size of receive buffer */

fprintf(stderr, "Usage: %s <Server IP> <Echo Word> [<Echo Port>]\n", argv[0]);

if (argc == 4)  
echoServPort = atoi(argv[3]); /* Use given port, if any */

else

/* Create a reliable, stream socket using TCP */  
if ((sock = socket(PF_INET, SOCK_STREAM, IPPROTO_TCP)) < 0)

    DieWithError("socket() failed");

/* Construct the server address structure */  
memset(&echoServAddr, 0, sizeof(echoServAddr)); echoServAddr.sin_family = AF_INET;  
echoServAddr.sin_addr.s_addr = inet_addr(servIP); echoServAddr.sin_port = htons(echoServPort); /* Server port */

/* Establish the connection to the echo server */  
if (connect(sock, (struct sockaddr *) &echoServAddr, sizeof(echoServAddr)) < 0)

DieWithError("connect() failed");  
echoStringLen = strlen(echoString); /* Determine input length */

echoServPort = 7; /* 7 is the well-known port for the echo service */

/* Zero out structure */
/* Internet address family */
/* Server IP address */

/* Send the string to the server */

if (send(sock, echoString, echoStringLen, 0) != echoStringLen) DieWithError("send() sent a different number of bytes than expected");

/* Receive the same string back from the server */  
totalBytesRcvd = 0;  
printf("Received: "); /* Setup to print the echoed string */ while (totalBytesRcvd < echoStringLen)  
{

/* Receive up to the buffer size (minus 1 to leave space for a null terminator) bytes from the sender */

if ((bytesRcvd = recv(sock, echoBuffer, RCVBUFSIZE - 1, 0)) <= 0) DieWithError("recv() failed or connection closed prematurely");

totalBytesRcvd += bytesRcvd; /* Keep tally of total bytes */ echoBuffer[bytesRcvd] = '\0'; /* Terminate the string! */ printf(echoBuffer); /* Print the echo buffer */

}

    printf("\n");

    close(sock);

exit(0); }

/* Print a final linefeed */
```

TCPEchoServer.c
```c
#include <stdio.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

#define MAXPENDING 5

/* for printf() and fprintf() */
/* for socket(), bind(), and connect() */
/* for sockaddr_in and inet_ntoa() */
/* for atoi() and exit() */
/* for memset() */
/* for close() */

/* Maximum outstanding connection requests */

void DieWithError(char *errorMessage); void HandleTCPClient(int clntSocket);

int main(int argc, char *argv[])
{

    int servSock;                    /*
    int clntSock;                    /*
    struct sockaddr_in echoServAddr; /*
    struct sockaddr_in echoClntAddr; /*
    unsigned short echoServPort;     /*
    unsigned int clntLen;            /*

/* Error handling function */
/* TCP client handling function */

if (argc != 2) /* Test for correct number of arguments */ {

fprintf(stderr, "Usage: %s <Server Port>\n", argv[0]);

exit(1); }

echoServPort = atoi(argv[1]); /* First arg: local port */

/* Create socket for incoming connections */

if ((servSock = socket(PF_INET, SOCK_STREAM, IPPROTO_TCP)) < 0) DieWithError("socket() failed");

Socket descriptor
Socket descriptor
Local address */
Client address */
Server port */
Length of client address data structure */

for server */
for client */

/* Construct local address structure */ memset(&echoServAddr, 0, sizeof(echoServAddr)); echoServAddr.sin_family = AF_INET; echoServAddr.sin_addr.s_addr = htonl(INADDR_ANY); echoServAddr.sin_port = htons(echoServPort);

/* Zero out structure */
/* Internet address family */
/* Any incoming interface */
/* Local port */

/* Bind to the local address */  
if (bind(servSock, (struct sockaddr *) &echoServAddr, sizeof(echoServAddr)) < 0)

    DieWithError("bind() failed");

/* Mark the socket so it will listen for incoming connections */ if (listen(servSock, MAXPENDING) < 0)

    DieWithError("listen() failed");

for (;;) /* Run forever */
{

    /* Set the size of the in-out parameter */
    clntLen = sizeof(echoClntAddr);

    /* Wait for a client to connect */

if ((clntSock = accept(servSock, (struct sockaddr *) &echoClntAddr, &clntLen)) < 0)

            DieWithError("accept() failed");

        /* clntSock is connected to a client! */

printf("Handling client %s\n", inet_ntoa(echoClntAddr.sin_addr));

        HandleTCPClient(clntSock);
    }

    /* NOT REACHED */
}
```