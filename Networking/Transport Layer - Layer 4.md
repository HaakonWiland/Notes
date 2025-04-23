<<<<<<< HEAD
#TTM4100 #TransportLayer #TCP #UDP 


Basic idea:
- Sender: Breaks application messages into segments, passes these to network layer 
- Receiver: Reassembles segments into messages, passes to application layer 

- Network layer: Logical communication between **hosts** 
- Transport layer: Logical communication between **processes** 


### Protocols:
#### TCP: Transmission control protocol
- **Point to point:** (one sender, one receiver i.e no broadcasting)
- **Reliable, in order delivery:** (no message boundaries, data is delivered without errors in the same order it was sent)
- **Full duplex data:** (bi directional data flow in same connection without waiting for the other)
- **Cumulative ACKs:** (possible to ack mulitple packets/bytes, instead of having to send individual acks. Works by "acking the last good byte")
- **Pipelining:** Congestion control(Adjusts sending rate based on perceived network health) and Flow control(sender will not overwhelm receiver)
- **Connection setup**: (Handshaking - initializes sender/receiver state before data exchange) 

Segment structure:
![[Pasted image 20250414124426.png]]


Q: How to set TCP timeout value? -> Has to be longer then RTT, but RTT varies. 
A: Time multiple RTTs, and compute a exponential weighted moving average:
![[Pasted image 20250414124842.png]]
- alpha can vary, but is typically 0.125 -> Think of it as the weight of new latest RTT sample. 
Still, we want some safety margin, since this is an average:
![[Pasted image 20250414125042.png]]
![[Pasted image 20250414125051.png]]

**TCP Re-transmission cases:**

![[Pasted image 20250414125635.png]]
![[Pasted image 20250414125643.png]]
![[Pasted image 20250414125653.png]]

**TCP - flow control:**
Q: What happens when network layer delivers data faster than application layer removes data from socket buffer? 
A: Receiver control sender, so sender wont overflow receiver buffer by transmitting to much, too fast. 
- Free buffer space is specified in the **Receive windows in the TCP packet**.

**TCP - Connection management:**
2 way handshake does not work -> Why? 

3 way handshake - establishing a connection: 
![[Pasted image 20250414131509.png]]

closing a connection:
![[Pasted image 20250414131632.png]]


**TCP - Congestion control:**
Congestion: "Too **many sources** sending **too much data too fast** for network to handle"

Network assist with network congestion control: 
- Routers provide direct feedback to sending/receiving host with flows passing through congested router

START HERE! 2. siste powerpoint. 


#### UDP: User Datagram Protocol
- **Unreliable, unordered delivery** (Think of it as sending a letter without tracking)
- **no-frills extension of "best effort" IP**: No reliability, flow control or congestion control
- **Connectionless:** (No handshaking between sender and receiver). Packets handled individually, no state kept on sender/receiver. Packet can arrive out of order, not at all, or all at once.  
![[Pasted image 20250412110139.png]]

**Advantages of UDP:**
- Lightweight segments(8 byte vs 20+ byte header in TCP). 
- Good when we want: Speed over reliability, low overhead or custom control over data.
- It is possible to add reliability functionality on Application layer. 

Goal: Want to detect errors -> Checksum:
Sender:
- Treat contents of UDP segment as sequence of 16 bit integers 
- Checksum: Addition of segment content 
- Checksum value put into udp checksum field
Receiver:
- Compute checksum of received segment
- Check if computed checksum equals checksum field value -> If not equal, there is an error

#### Multiplexing and Demultiplexing:
![[Pasted image 20250412104621.png]]

**Demultiplexing:**
Host receive IP datagrams which contains 
- Source IP address, destination IP address, Transport-layer segment, each segment has source,destination port number.
- Host uses IP addresses and port numbers to direct segment to appropriate socket 
![[Pasted image 20250412104900.png]]

**Connectionless demultiplexing:**
- Uses UDP(since connectionless)
- Datagram must spesify (Dest IP, Dest Port)
- NOTE: UDP datagrams with same Dest Port, but different source IP (or source Port) will be directed to the same socket at receiving host 
![[Pasted image 20250412105824.png]]
=======
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
>>>>>>> main



**Connection-oriented demultiplexing:**
- TCP socket identified by 4-tuple: (Source IP, Source port, Dest IP address, Dest port)
- Demux: Receiver uses 4-tuple to direct segment to appropriate socket 
- Server may support many simultaneous TCP sockets(Each socket associated with a different connecting client)
![[Pasted image 20250412105432.png]]

#### Principles of reliable data transfer:

![[Pasted image 20250412111352.png]]

![[Pasted image 20250206152107.png]]

#### rdt1.0:
#### rdt2.0:
- Introduces the recovery of error

#### rdt3.0:
- What happens if the unreliable channel looses packets? 

**RDT: Reliable data transfer protocol**

**RDT 2.0:**
Q: How to detect bit errors: -> Checksums 
Q: How to recover from errors:
- Acknowledgements(ACKs): Receiver explicitly tells sender that pkt received OK 
- Negative Acknowledgements(NAKs): Receiver explicitly tells sender that pkt had errors
- Sender re-transmits pkt on receipt of NAK
SO: Sender sends one packet, then waits for receivers response 

**RDT 2.1:**
FLAW in 2.1: What happens if ACK/NAK corrupted? 
2.1 fixes this with:
- Sender re-transmits current pkt 
- Sender adds sequence number to each pkt 
- receiver discards duplicate pkt's 

**RDT 2.2:**
- Uses ACKs only 
- Instead of NAK, receiver sends ACK for last pkt received OK (which include seq number of pkt being ack'ed)
- Duplicate ACK at sender results in same action as NAK -> re-transmit current pkt. (This is what TCP does)

**RDT 3.0:**
Up till now we have assumed that the underlying channel using RDT has not bit erros, and no loss of packets. 

RDT 3.0 assumes the underlying channel can also lose packets(data, ACKs). How do deal with this:
- Sender waits "reasonable" amount of time for ACK
- Re-transmits if no ACK received in this time
RDT 3.0 in action:
![[Pasted image 20250414121923.png]]
![[Pasted image 20250414122139.png]]

Negative aspects of RDT 3.0:
- It is slow, and limits the performance of underlying channel 

Ways to improve this issue:
- **Pipelining:** Sender allows multiple "in flight"-yet-to-be-acknowledged packets.
- To pipeline, we need to increase the range of sequence numbers and add a buffering at sender/or receiver. 

NOTE: Lots of more stuff on RDT, but it is very technical and detailed. Don't thinks its relevant for the exam. 


