#networking #LinkLayer #TTM4100 #ARP

 **Link layer has responsibility of transferring datagram from one node to physically adjacent node over a link**

Link layer packet: Frames that encapsulates datagrams.

#### Services:
**Framing, link access:**
- Encapsulation 
- Channel access if shared medium
- MAC addresses in frame headers identifying source and destination 


**Reliable delivery between adjacent nodes:**

**Flow control:**
- Pacing between adjacent sending and receiving nodes 
- Same concept as in the Transport layer 

**Error detection:**
- Errors caused by signal attenuation, noise.
- Receiver detects errors, signals re transmission or drops frame. 

**Error correction:**
- Receiver identifies and corrects bit errors without re transmission 

**Half-duplex and full duplex:**
- Full duplex, both ends can communicate at the same time 
- Half-duplex, only one end can communicate at the time 


#### Physical mediums:
![[Pasted image 20250310084255.png]]


### Error detection and correction:

**Setting:**
![[Pasted image 20250310084657.png]]


#### Parity checking:
![[Pasted image 20250310090545.png]]
- Basically, just make each row and column have even amount of 1's.  


#### Cyclic Redundancy check (CRC):
**Dont need to understand all parts, but  need to understand basic principal and properties!**

NOTE: calculating CRC is relevant for EXAM! 

**CRC of r bits can detect:**
- All burst errors < r+1 bits 
- All odd number of bit errors 
- Burst error > r+1 detection with probability 1^(-0.5)*r 

![[Pasted image 20250310085727.png]]


**Step by step - how to find CRC bits:**
![[Pasted image 20250310090345.png]]

![[Pasted image 20250310085706.png]]

Example:
- We append 3 zeros to D, since g has order 3 
![[Pasted image 20250417141021.png]]




### Multiple Access link, protocols:
**Distributed algorithm that determines how nodes share channel, i.e. determine when a node can transmit. Note: communication about channel sharing must use channel itself!**


**Point to point:**


**Broadcast (shared wire or medium):**
- 4G/5G, satellite 


#### Ideal multiple access protocol:
![[Pasted image 20250310093508.png]]


#### 3 Broad classes of MAC(Medium access control)
- Used to manage how multiple devices share a communication medium. 

NEED TO KNOW: Difference between different MAC protocols, why we have them and benefits of each of them. Dont need to know everything in detail. 


1. **Channel partitioning:**
- Divide channel into smaller pices(time slots, freq, code etc)
- Allocate piece to node for exclusive use 
- Inefficient at low load: Delay in channel access, 1/N bandwidth allocated even if only 1 active node.

**TDMA: time division multiple access:**
- Access to channel in "rounds"
- Each station gets fixed length slot (length=packet transmission time) in each round 
- Unused slots go idle 

**FDMA: Frequency division multiple access:**
- Channel spectrum divided into frequency bands
- Each station assigned fixed frequency band 
- Unused transmission time in frequency bands go idle 

2. **Random access:**
- Channel not divided, allow collisions 
- Mechanism for recovering from collisions 
- Basically send at random, but have ways to fix when something goes wrong 
- Efficient at low load: Single node can fully utilize channel
- High load: More collision overhead 

**Slotted ALOHA (Improved Pure ALOHA):**
![[Pasted image 20250310101923.png]]

Pros:
- Single active node can continuously transmit at full rate of channel 
- Highly decentralized: only slots in nodes need to be in sync 
- simple 
Cons:
- Collisions, wasting slots 
- idle slots 
- Nodes may be able to detect collisions in less than time to transmit packet 
- Clock synchronization 

It can be shown: at best channel used for useful transmission ~37% of time. 


**Pure ALOHA:**
- Collision probability increases with no synchronization 
- Efficiency improved to ~18%. 


**CSMA: Carrier sense multiple access**

**Simple CSMA:** Listen before transmit 
**CSMA/CD:** CDMA with collision detection; collisions detected within short time. 

![[Pasted image 20250310102933.png]]

3. "**Taking turns:**"
- Nodes take turns, but nodes with more to send take longer turns
- Best of both worlds of (1) and (2)

**Polling:**
- A master node "invites" other nodes to transmit in turn 
- Typically for "dumb" devices 
- Cons: Pooling overhead, latency, **single point of failure!** 
![[Pasted image 20250313082825.png]]



**Token passing:**
- Control token passed from one node to next sequentially 
- A client need to wait in turn to get the token to be able to "speak"
- Same cons as pooling! (Think about what happens if a client which has the token goes down-> the network would go down)


![[Pasted image 20250313082815.png]]




### Cable access network:

![[Pasted image 20250313083415.png]]

**key point:** 
- Use the exciting network, splitting it to provide network access for multiple devices 



### LANs:

**MAC-addresses:**
- 48 bit address
- Unique to the actual hardware 
- Used to locally to get frame from one interface to another physically-connected inferface (Same subnet, in ip addressing sense)

Private WiFi address option: "fake" MAC address, such that when we visit public networks, we dont give our unique mac to each network we connect to.  

Q: How to determine interfaces MAC address, knowing it IP address?
A: ARP table! -> Each ip node (host, router) on LAN has table **(IP, MAC, TTL)**


#### ARP protocol example:

**A wants to send datagram  to B:**

![[Pasted image 20250313085423.png]]

![[Pasted image 20250313085513.png]]

![[Pasted image 20250313085551.png]]

Summarized:
- If A wants to send a datagram to B, and they are on the same subnet, he can do so directly if A knows Bs MAC address
- A uses ARP to find the MAC of B. 
- A do not need to go via any router. 



**ARP example 2  - Routing to another subnet:**

QUESTIONS about this will come on exam! 

![[Pasted image 20250313091759.png]]

- The IP address of the first gateway probably gained by DHCP 
- A gained information about the MAC address of R, probably did a ARP with the router previously 

![[Pasted image 20250313092243.png]]

- Once the datagram reaches the router, we remove the previous frame, and encapsulates it with another frame 

![[Pasted image 20250313092414.png]]

- In this example, we router already has the MAC of B, so the router can easily make the new frame 

![[Pasted image 20250313092652.png]]

Summarized:
- If A wants to send B a datagram, and B is on another subnet, A cannot send the datagram directly to B.
- Instead A has to send the datagram to the router.
- To send the datagram to the router, A has to know the MAC of the router(Use ARP to find it, if A has not cached it)
- A build a ethernet frame with destination IP of B, and the MAC address of the router.
- Now the router can send the datagram to B (It need to know Bs MAC, can ARP to find it, if it is not cached)


#### Ethernet:
- Dominant wire LAN technology 
- Connectionless: No handshaking between sending and receiving NICs
- Unreliable: Receiving NIC doesn't send ACKs or NAKs to sending NIC 

**Physical topology:**
![[Pasted image 20250313092953.png]]

- Bus - NOT COMMON ANY LONGER
- Switches 

Sending interface via Ethernet encapsulates IP datagram in Ethernet Frame:
![[Pasted image 20250313093419.png]]

- Type: specifies higher layer protocol, usually IP
- CRC: Just error detecting, no correction 

**Ethernet standards:**
There are multiple Ethernet standards, they differ in **Speed** and **Physical layer media.**

#### Switches:
- Store, forwards Ethernet frames 
- Hosts is unaware of the presence of switches(If we do traceroute, we would not see the switching steps)
- Full duplex
- We can switch A-B and C-D simultaneously without collisions 

Switch table contain (MAC, Interface in the switch, TLL):
![[Pasted image 20250313102240.png]]

**Switch vs router:**
Both store and forwards
- Routers: Network layer device
- Switch: Link-layer device 


### Steps in sending a web request:
Setting: A pc wants to connect to a uni network, and from here request google.com.

**STEP 1:** The PC need to get an IP on the network: Make use of DHCP 

![[Pasted image 20250313102520.png]]

![[Pasted image 20250313102530.png]]

**STEP 2:** Before we can send a HTTP request, we need the IP address of google.com: Make use of DNS.
Before we can send the DNS request, we need the MAC address of the router interface, so we need to use ARP:
![[Pasted image 20250313102851.png]]

- Now the client knows the MAC address of the first hop router, we it can now send the frame containing the DNS query.
- The client sends the DNS query via the first router hop, which routes it to the ISP, which then answers with the ip address matching google.com
![[Pasted image 20250313103231.png]]

**STEP 3:** To send the HTTP request, the client first opens a TCP socket to the web server of google. They preform the 3-way handshake and establishes a TCP connection. 

![[Pasted image 20250313103424.png]]

**STEP 4:** Now we can finally send the HTTP request into the TCP socket, routed to google.com.
- The google web server responds with a HTTP replay based on the request, containing the actual web page. 
- The client can now view the web page from their browser
![[Pasted image 20250313103637.png]]
