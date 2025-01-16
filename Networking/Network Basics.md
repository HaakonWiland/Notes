#TTM4100 #ComputerNetworksATopDownApproach

### Definitions and terminology 
- **Networks:** Collection of devices, routers, links etc
- **Internet:** "A interconnected Network of networks" or "Infrastructure that provides services to applications."
- **Packet Switches:** An device on a network which take a packet arriving on one of it incoming communication links and forwards that packet outgoing communication links. Ex. routers and link-layer switches. 
- **(ISP) - Internet Service Providers:** Gives end systems access to the internet. There are different tiers of ISPs, regional, national, global etc. 
- **Protocol:** Defines the format and order of communication between two or more entities.
- **Throughput:** Rate (bits/time) at which bits are being sent from sender to receiver. 


### Access Networks:
Connects end systems to router, example home networks, enterprise networks, data center networks.  

### The Network Edge:
The end nodes, such as a client workstation or an enterprise datacenter. 

### The Network Core:
Packets and links that interconnects the Internets end-systems.

- Data being sent from a source end system to a destination end system is broken down into smaller chunks of data, know as **Packets.** 
- We measure the speed at which the packets as transmitted in: **L/R** 
*if a source end system or a packet switch is sending a packet of L bits over a link with transmission rate R bits/sec, then the time to transmit the packet is L / R seconds.*

- **Store and forward Transmission:** The whole packet data is first being sent to the packet switch, before being forwarded to the destination. 

- **Packet switching:** Resources used for transmission is used on demand, and are not reserved. This means communication have to wait in queue for access to a communication link. 
![[Pasted image 20250110114436.png]]

- **Circuit switching:** Resources used for transmission are reserved for the duration of the communication session. 
![[Pasted image 20250110114325.png]]


### Network performance:

- **Delay or loss:** Can come from **Queueing delay**=Package queue in router, or **Transmission delay** =Packages is taking time traveling. 


