#TTM4100 #networking 


Transport protocols actions in end systems:
- Sender: Break application messages into **segments** and passes them to the network layer 
- Receiver: Reassembles the segments into messages, passes them to the application layer 


A way to think about it:
- Network layer: Logical communication between hosts 
- Transport layer: Logical communication between processes 


### Transport layer protocols
See also: [[Networks and Network Security]]

#### TCP:
- Point to point: 1 sender, 1 receiver 
- Byte oriented: "View the communication as a continues stream of bytes, not independent packages". 
- congestion control
- flow control: Sender will not overwhelm receiver 
- connection setup: handshaking before data exchange 
- Pipelining: 
- Cumulative ACKs:

![[Pasted image 20250212125255.png]]


- **Sequence number:** Tells the recipient where this packets data belong in the full TCP stream.  
- **Acknowledgement number:** Tells the the sender where the next packet in the TCP stream is expected to start. This ensures if a packet is lost, it is possible to backtrack to the last successfully delivered packet. 

Example:
![[Pasted image 20250212133245.png]]


**TCP round trip time and Timeouts:**
- Since TCP is connection based, it needs to know when to "stop" the connection. 
- The Timeout obviously has to be longer than RTT, but RTT varies. So how do we deal with this? 
- The solution is to take the time from a segment has been transmitted until we receive a ACK, and average these out:
![[Pasted image 20250212134756.png]]

Think of alpha as the weigh of the last segment sample.  

- But we can see there is still a lot of variation in the EstimatedRTT, so we want to have some safety margin for the timeout. 
![[Pasted image 20250212135053.png]]



#### UDP: User Datagram Protocol, (RFC 768)
- unreliable, unordered delivery 
- "no-frills": Segments may be lost, delivered out of order  
- best effort service: "Send and hope for the best."
- connectionless: No handshaking between UDP sender and receiver. Each UDP segment handled independently for the others. 

**Packet structure:**

![[Pasted image 20250206151047.png]]

Checksum: Detect errors in transmitted segments.  Receiver recomputes the checksum and verify that its equal to the checksum in the packet. 

NOTE: Adding bits:
```
0+0=0
1+0=1
0+1=1
1+1=10
```

**Use cases:**
- streaming multimedia. (not all services, some uses TCP)
- DNS
- SNMP
- HTTP/3 


### Multiplexing / Demultiplexing 

**Demultiplexing of TCP:**
- Uses a 4-tuple: (source IP, source PORT,  destination IP, destination PORT) 

**Demultiplexing of UDP:**
- Uses just destination port number. 


### Reliable data transfer protocol (rdt):

- Sending data over unreliable channel make cause errors and changes of the packets, rdt helps us detect and recover from said errors. 




![[Pasted image 20250206152107.png]]

#### rdt1.0:
#### rdt2.0:
- Introduces the recovery of error

#### rdt3.0:
- What happens if the unreliable channel looses packets? 


