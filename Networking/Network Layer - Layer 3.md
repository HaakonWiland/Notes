#networking #TTM4100 #DHCP #Network-Layer #ICMP 

Lingo:
- **Datagram:** independent unit of data. Can think of it as a term for packets specifically in the network layer. 
- **Switching rate:** Rate at which packets can transfer from input to output. Often a multiple of input/output line rate. 
- **Packet scheduling:** Deciding which packet to send next on link 
- **IP address:**
- **Interface:** Connection between host/router and physical link. Routers typically have multiple interfaces. Hosts typically have 2, wired and wireless. 
- **Bandwidth:** Theoretical maximum data rate of the connection
- **Throughput:** The actual data rate you're getting, which can be lower then bandwidth due to congestion, errors, etc. 

--------


#### Key function of the network layer:
- **Forwarding:** move packets from router input to router output'
- **Routing:** Determine packet route from source to destination

#### IP protocol:
- Connectionless 
- Best effort 
- => Does not guarantee packet delivery correct order or error recovery. 
- Uses IP addresses (IPv4 and IPv6)
- Uses NAT to manage between the internet and local network 

IP datagram format: 
![[Pasted image 20250416122939.png]]

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


#### Error detection in Link Layer:
EDC: Error detection and correction bits 

Parity checking:
**Single bit parity:**
- Detect single bit errors, by checking for even number of 1-bits: ![[Pasted image 20250403122701.png]]

**Two dimensional bit parity:**
- Detect and **Correct** single bit error: ![[Pasted image 20250403122755.png]]




#### Network service model 

What services do we expect to get from a network:
![[Pasted image 20250416124947.png]]

### Routers:
**Overview of router architecture:**
![[Pasted image 20250220101412.png]]


#### Input port functions:
![[Pasted image 20250220101540.png]]


##### Forwarding methods:
**Destination based forwarding:** Forward based only on destination IP address.

**Longest prefix matching**:
- We have a table which matches destination IP address patterns, with network interfaces.
- When the router gets a destination IP, and wants to know to what interface it should forward it to, it looks in this table.
- It picks the interface which has most bits in common with the destination IP 
![[Pasted image 20250526125512.png]]


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

**Checking if a subnet is a valid network:**
```
1) 172.16.4.127/26


6 bits for host addresses: 2^6 = 64
Subnetmask for /26: 255.255.255.192

So: 
172.16.4.127 is the network address 
172.16.4.128-190 are possible host addresses 
172.16.4.191 is the broadcast address. 

In general:
A valid /26 network needs to have start address: XXX.XXX.XXX.0 , .64, .128, or .192 

FROM THIS WE KNOW: The start address of the /26 subnet has to be a multiple of 64.

So we can just check: 

127 % 64 != 0 => its not a valid network. 

2) 172.16.4.155 /26: 155 mod 64 != 0 => not valid. 
3) 172.16.4.95 /27 95 mod 32 != 0 => not valid 
4) 127.16.4.207 /27 207 mod 32 != 0 => not valid 

So to check if a subnet is valid, just take the last octet, and check if it it divisible by the number of addresses in the subnet. 
```


**More about subnets:**
```
"A subnet has the have the prefix: 129.241.56/21", means: 

The subnet is a part of the range:
[129.241.56.0 - 129.241.63.255] (2048 values)

1: 129.241.56.0/22      
2: 129.241.60.0/23      
3: 129.241.62.0/23

Checking for collision:
1: 
has 1024 addresses 
1024 / 256 = 4 =>
Range:  129.241.56.0 - 129.241.59.255

2: has 512 addresses
512 / 256 = 2 =>
Range: 129.241.60.0 - 129.241.62.255

3: has 512 addresses
512 / 256 = 2 =>
Range: 129.241.62.0 - 129.241.63.255

HENCE: THERE ARE NO OVERLAP IN THE SUBNETS
```

**What subnet is the IP address part of:**
```
Question: Given an IP: 172.29.13.61, what /30, /29,.../26 etc subnet is it part of?

/30:
- In /30, there is 4 addresses. 
- floor(61/4)=15
- 15*4 = 60
- 172.29.13.61 could be in the 172.29.13.60/30 subnet. 

Follow same fomula, for each of them:
1. Find the total number of addresses in the subnet 
2. Take the last octet, and floor-divide it by the number of addresses. 
3. This is the octet in the CIDR subnet notation. 
```


**Create an IP plan with subnets:**
```
Things to think about:
- Different "IP-families" for different things, linknetwork, router addresses, host addresses, etc.
- We dont want to make the routing table bigger than nessesary, but we also want it to "mean something".

- Consider the network ranges of the different subnets, and how many ip addresses a given "family" needs. 
- Divide the subnets first roughly in families, then we can divide them more within the families to make it neater.  
```

#### DHCP: Dynamic Host Configuration Protocol 
GOAL: **Host dynamically obtains IP address from network server when it joins the network - we don't want to manually assign an IP each time a new host connects.**

Default port: 68 -> Uses UDP protocol 

Note: The networks DHCP server will typically be co-located in the router 

**4 steps to the protocol:**

![[Pasted image 20250224120903.png]]

**Other functions of DHCP:**
- Address of first-hop router for client 
- Name and IP address of DNS server
- Network mask 


#### Network address translation (NAT):
**All devices in our local network share just 1 IPv4 address as far as outside world is concerned - the NAT take case of the translation between local- and public addresses:**

![[Pasted image 20250303124017.png]]

**Advantages with this NAT:**
- just 1 IP address needed from the provider ISP for ALL devices 
- Can change addresses of host in local network without notifying outside world 
- Can change ISP without changing addresses of devices in local network
- Devices inside local network not directly addressable, visible by outside world -> good for security.

The NAT router must:
- Replace **outgoing source** (source IP,port) pair for each datagram to (NAT IP, new port)
- Replace **incoming** desination (NAT IP, new port) pair for each datagram to (source IP, port)

![[Pasted image 20250303124931.png]]

**Critic of NAT: NAT makes things messy, and complicates the peer-to-peer design**
- Routers "should" only process up to layer 3. With NAT routers modifies the IP header and sometimes work with the transport layer. 
- address "shortage" should be solved by IPv6. Since in this case all devices would have a unique address, which allows for end-to-end connectivity. This simplifies the architecture. 
- NAT traversal: What if client wants to connect to server behind NAT?


#### IPv6:
**Motivation: IPv4 is only 32bit, and we fear we will run out of addresses.**

IPv6 addresses are of 128 bit length. 

**IPv6 datagram format:**
![[Pasted image 20250303125436.png]]

Different from IPv4:
- Fixed header length(40 bytes)
- No checksum
- No fragmentation/reassembly of packets in the router, only sender can preform fragmentation. This means routers do not have to check for fragment packets, which makes them more efficient. 
- Replaces options headers with extension headers -> Improving efficiency 
- IPv6 supports SLAAC, allowing devices to generate their own addresses automatically without using DHCP 


**How will network operate with mixed IPv4 and IPv6 routers?**
A: Tunneling IPv6 datagram carried as payload in IPv4 datagram among IPv4 routers

![[Pasted image 20250303125822.png]]


SIDENOTE: Google: ~30% of clients access services via IPv6. It will take long time before all is using IPv6.


### Control plane:
Goal: We want to understand principles behind network control plane. 


**Network layer functions:**
- Forwarding: move packets from routers input to appropriate router output (Data plane)
- Routing: Determine route taken by packets from source to destination (Control plane)

**Two approaches to structureing network control plane:**
- **Per-router control(traditional):**
Individual routing algorithm components in EACH AND EVERY ROUTER interact in the control plane. 
- **Logically centralized control (Software defined networking)**

#### Routing protocols:
We want to determine "good path" from sending host to receiving host through network of routers. A good path can be:
- Least cost 
- Fastest 
- Least congested 

A comprehensive network problem, with multiple different strategies to solve. 

**Centralized routing algorithms:**
- Central authority to compute the routes 
- Assumes the knowledge of the entire network topology 


#### Internet control message protocol (ICMP):
**Routing protocol. Used by hosts and routers to communicate network-level information**

Information it can communicate:
- Error reporting: unreachable host, network, port, protocol etc 
- Echo request/reply 
- ICMP messages carried in IP datagrams 

Goal: Determine "good" paths from sending hosts to receiving host through network of routers. 


**ICMP message:** Type, code and first 8 bytes of IP datagram causing error 
![[Pasted image 20250303131924.png]]


