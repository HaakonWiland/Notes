#networking #TTM4100 


Lingo:
- **Datagram:** independent unit of data. Can think of it as a term for packets specifically in the network layer. 
- **Switching rate:** Rate at which packets can transfer from input to output. Often a multiple of input/output line rate. 
- **Packet scheduling:** Deciding which packet to send next on link 


---------
#### Key function of the network layer:
- **Forwarding:** move packets from router input to router output'
- **Routing:** Determine packet route from source to destination

#### We split the Network layer into two planes:

**Data plane:**
- Local, per ruter functionality 
- Determines how datagram arriving on the router input port is forwarded to router output port. 
**Control plane:**
- network wide logic 
- Determine how datagram is routed among routers, from source to destination host. 


![[Pasted image 20250220090942.png]]


**Software defined networking (SDN) control plane:**
- Alternative way to implement the control plane 
- Centralized implementation 

#### Network service model 

.....

### Routers:
**Overview of router architecture:**
![[Pasted image 20250220101412.png]]


#### Input port functions:
![[Pasted image 20250220101540.png]]


##### Forwarding methods:
**Destination based forwarding:** Forward based only on destination IP address.
- longest prefix matching 

**Generalized forwarding:** Forward based on any set of header field values.

(TCAM NOT part of the exam)

**Input port queuing:**
- If switch fabric slower then input ports combined -> queuing may occur at input queues
![[Pasted image 20250220103331.png]]

- **Head of line blocking(HOL blocking):** Queued datagram at front of queue prevents others in queue from moving forward. 

**Output port queuing:**
![[Pasted image 20250220103520.png]]

- Buffering is required when datagrams arrive from switch fabric faster than link transmission rate. But what if the buffer is full? -> Which datagram should we drop?
- Tail drop: Drop the last arriving packet.
- Priority: Drop/remove on priority basis, ex. videostreaming packet more time sensitive then email. 

Packet scheduling methods:
- **First in first out(FIFO):**
- **Priority:** Classify packets based on header fields, then send the packets with the highest priority class first.
- **Round Robin:** Classify packets, but repeatedly send one complete packer from each class in turn.
![[Pasted image 20250220104501.png]]
- **Weighted fair queuing**: Weighted round robin, each class gets weighted amount of service in each cycle. Provides minimum bandwidth guarantee per class. 



#### Switching fabrics: transfer packet from input link to appropriate output link

![[Pasted image 20250220102131.png]]


![[Pasted image 20250220102514.png]]

- Memory: Packet copied to systems memory 
- Bus: Datagram from input port memory to output port memory via a "shared bus"
- Interconnection network: using multiple switching "planes" in parallel 










