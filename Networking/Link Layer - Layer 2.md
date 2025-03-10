#networking #link-layer #TTM4100 #ARP

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



### Multiple Access link, protocols:
**Distributed algorithm that determines how nodes share channel, i.e. determine when a node can transmit. Note: communication about channel sharing must use channel itself!**


**Point to point:**


**Broadcast (shared wire or medium):**
- 4G/5G, satellite 


#### Ideal multiple access protocol:
![[Pasted image 20250310093508.png]]


#### 3 Broad classes of MAC
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

**Slotted ALOHA:**
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


**Token passing:**









