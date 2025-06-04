http://unixwiz.net/techtips/iguide-kaminsky-dns-vuln.html

### DNS
- Translate IP addresses to 
![[dns tree.png]]

Zone: domain, pairs of names/ips all managed together.

Nameserver: some external lookup table that can be used as a name to ip "translation guide".

Authoritative Nameserver: a well trusted Nameserver (google and cloudflare!)

Resolver: part of application side that makes a request to the nameserver

Recursive Nameserver: nameserver that requests other nameservers until the ip of website is found

#### DNS query
1. client sends request for name of website and request is directed to Nameserver through ISP, which is the closest one
![[Screenshot 2024-04-05 at 2.45.43 PM.png|400]]
2. recursive nameserver as a list of 13 root servers. It will check these servers for the IP address. It picks one at random.
![[Screenshot 2024-04-05 at 2.46.37 PM.png|400]]
3. sends the request to the top level domain. For wpi.edu, it will send it to the .edu domain. Includes a list of referal servers for top level domains. these are called glue records.
 ![[Screenshot 2024-04-05 at 2.48.00 PM.png|400]]
 4. With the helpful referral from the root servers, this nameserver chooses one of the authoritative servers at random — here, **c.gtld-servers.net** — and sends off the same query: "I was told to ask you: what's the A record for **www.unixwiz.net**?".
 5. GTLD server doesn't know our request exactly, but is able to narrow it down and refer to 

#### DNS Packet
![[Screenshot 2024-04-05 at 2.52.45 PM.png|400]]
**Query ID**: unique id left intact by the server sending the reply. allows server making the request to associate an answer with a question. helps Nameserver to reply to the correct host.
**QR**: set 0 by a client, 1 for server response
**Opcode**: 0 for standard query
**AA**: 1 is server answer is authoritative, 0 if not

... 
**Question Record Count**: the question, specifying what the asker is looking for
![[Screenshot 2024-04-05 at 2.57.01 PM.png|400]]


##### Question types
![[Screenshot 2024-04-05 at 2.58.27 PM.png]]

one name can have multiple resource records
```shell 
www.example.com. IN A 192.168.1.3
www.example.com. IN A 192.168.7.149
```

### ARP
- only used for local network
- allows A to find Bs mac address when A only knows Bs IP
- ARP table is a simple cache