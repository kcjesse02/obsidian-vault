1. How many packets are in the capture?  
60 packets. The packet number in the capture is specified with the first column in Wireshark

2. How long in seconds did the capture last?  
9.198384 seconds. This is the time the last packet was captured

3. The capture is an email message. What is the mail sender’s email address?  
gurpartap@patriots.in

4. The email contains a data portion. What does it look like the sender is transmitting? 
The contents of the email is represented as plain text html with ascii text encoding. There are mutiple data packets. The first says this:
--------
Hello


I send u smtp pcap file

Find the attachment


GPS

------
As the email says, there is an attachment in the email!
in the third data packet, there is a piece of data, a variable named \tname indicating that this file is called NEWS.txt

This file contains update information for a piece of software

One thing that I find interesting is the contents of the file are transmitted the same way the text of the email is, through unobfuscated ascii text. 

5. There are several types of protocols in the Protocols column. What are the protocols?

The most common protocol is the SMTP protocol, which stands for Simple Mail Transfer Protocol. This manages everything to do with sending an email, including sending and recieving address

At the beginning, there is DNS protocol to resolve the website for the email address (patriot.in).

There are also TCP protocol packets present in this capture as well. These are primarily used for testing the connection rather than transferring information.

There are also Internet Control Message Protocol (ICMP) packets



