#networking #TTM4100 #DHCP #Network-Layer


Lingo:
- **Datagram:** independent unit of data. Can think of it as a term for packets specifically in the network layer. 
- **Switching rate:** Rate at which packets can transfer from input to output. Often a multiple of input/output line rate. 
- **Packet scheduling:** Deciding which packet to send next on link 
- **IP address:**
- **Interface:** Connection between host/router and physical link. Routers typically have multiple interfaces. Hosts typically have 2, wired and wireless. 

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

**Output port queuing and Buffer management:**
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


NOTE: For a network with multiple routers, it is possible that each router have different packet scheduling methods. 
#### Switching fabrics: transfer packet from input link to appropriate output link

![[Pasted image 20250220102131.png]]


![[Pasted image 20250220102514.png]]

- Memory: Packet copied to systems memory 
- Bus: Datagram from input port memory to output port memory via a "shared bus"
- Interconnection network: using multiple switching "planes" in parallel 


### Data plane:

**IP Datagram format:**
![[Pasted image 20250224085251.png]]



**How to get a IP address:**
How does a host get IP address within its network?
- DHCP
- Sysadmin config (/etc/rc.config on unix)

How does a network get IP address for itself?
- From the ISP 

EX: Org 1 moves from on ISP to another, so the new ISP need to new include org 1s IP address in their address range. 
![[Pasted image 20250224121731.png]]



#### Subnets:
- Device interfaces that can physically reach each other **without passing through an intervening router.**

![[Pasted image 20250224115943.png]]



**Structure of IP address:**
*Detached islands of isolated networks.*


- Subnet part: Devices in the same subnet have common high order bits.
- Host part: The remaining low order bits that are not fixed for the subnet. 
- EX: 192.168.1.0/24: means the first 24 bits are fixed(192.168.1), and the last 8 bits is the host part. So this subnet can manage 2^8=256 hosts. NB: 1 and 256 are reserved, so in actuality its 254 hosts on the network. 

Subnet-mask: 
- ex: 255.255.255.0 
- Classic sizes of submask ranges: /24, /16, /8. 
- But we also have: /32, /31, ..... for situations where we just need a few ip addresses in the subnet. 



#### DHCP: Dynamic Host Configuration Protocol 
GOAL: **Host dynamically optains IP address from network server when it joins the network - we dont want to manually assign an IP each time a new host connects.**

Default port: 68 

Note: The networks DHCP server will typically be co-located in the router 

**4 steps to the protocol:**

![[Pasted image 20250224120903.png]]

**Other functions of DHCP:**
- Address of first-hop router for client 
- Name and IP address of DNS server
- Network mask 