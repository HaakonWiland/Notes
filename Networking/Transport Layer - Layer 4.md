#TTM4100 #TransportLayer #TCP #UDP 


Basic idea:
- Sender: Breaks application messages into segments, passes these to network layer 
- Receiver: Reassembles segments into messages, passes to application layer 

- Network layer: Logical communication between **hosts** 
- Transport layer: Logical communication between **processes** 


### Protocols:
#### TCP: Transmission control protocol
- Reliable, in order delivery 
- Congestion control
- Flow control
- Connection setup 

#### UDP: User Datagram Protocol
- **Unreliable, unordered delivery** 
- **no-frills extension of "best effort" IP**: Segments may be lost, delivery out of order. "send and hope for the best"
- **Connectionless:** (No handshaking between sender and receiver), each udp segment handled independently of others 
![[Pasted image 20250412110139.png]]

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



**Connection-oriented demultiplexing:**
- TCP socket identified by 4-tuple: (Source IP, Source port, Dest IP address, Dest port)
- Demux: Receiver uses 4-tuple to direct segment to appropriate socket 
- Server may support many simultaneous TCP sockets(Each socket associated with a different connecting client)
![[Pasted image 20250412105432.png]]

#### Principles of reliable data transfer:

![[Pasted image 20250412111352.png]]


**RDT: Reliable data transfer protocol**

**RDT 2.0:**
How to detect bit errors: -> Checksums 
Q: How to recover from errors:
- Acknowledgements(ACKs): Receiver explicitly tells sender that pkt received OK 
- Negative Acknowledgements(NAKs): Receiver explicitly tells sender that pkt had errors
- Sender re-transmits pkt on receipt of NAK
SO: Sender sends one packet, then waits for receivers response 

FLAW: What happens if ACK/NAK corrupted? 
- Sender re-transmits current pkt 
- Sender adds sequence number to each pkt 
- receiver discards duplicate pkt's 

**RDT 2.2:**
- Uses ACKs only 
- Instead of NAK, receiver sends ACK for last pkt received OK (which include seq number of pkt being ack'ed)
- Duplicate ACK at sender results in same action as NAK -> re-transmit current pkt. (This is what TCP does)

**RDT 3.0:**
START HERE


![[Pasted image 20250115163329.png]]