#networking #protocols #TTM4100 #HTTP #DNS




Topmost layer in the OSI and TCP/IP network model, responsible for directly interacting with user-facing software applications. 

When writing a program that communicates over a network, we have different paradigms of how to implement the way the program interacts over the network. 

#### Client-server Paradigm: ex. HTTP, FTP, IMAP
- Client communicates directly with the server.
- Server has permanent IP address.
- Client communicate via the server, not directly with each other.

#### Peer-Peer Paradigm: ex. P2P file sharing 
- Client can communicate directly with each other, no need to go via a server 
- Peers request service from other peers, provide service in return to other peers -> Self scalability 
- Peers intermittently connected and change ip address. 


####  Transport services:
Different application need different properties 
![[Pasted image 20250116111305.png]]

The need for different transport properties, introduces the need for different transport protocols:
See: [[Transport Layer - Layer 4]] , [[Networks and Network Security]]
- TCP 
- UDP

#### Application layer protocols:
##### **HTTP:** Hypertext transfer protocol 
- Built on TCP -> Prevents loss of data, which is desired when browsing the web. 
- Stateless: Server do not maintain information about past clients 
- Have many versions; 1.0, 1.1, 2.0, 3.0,  persistent and non-persistent 
![[Pasted image 20250116112024.png]]

**Claim:** Communication delay heavily relay on how the protocol acts. 

**General format of a HTTP request:**
![[Pasted image 20250116112305.png]]

**Different request messages:**
![[Pasted image 20250116112517.png]]

**Different response status codes:**
![[Pasted image 20250116112549.png]]



**HTTP/1.1:**
- Improvement: Multiple, pipelined GETs over single TCP connection. 
- Problems: Server responds in-order (FCFS) -> meaning small object may have to wait for transmission behind larger objects. (HOL blocking)


**HTTP/2:**
Motivation: Decreased delay in multi-object HTTP requests. 
- Improvement: Increased flexibility at server in sending objects to client.
- Transmission order of objects from server to client now client specified i.e. can choose to prioritize smaller objects
- Can now divide objects into frames, and schedule frames to mitigate HOL blocking. 

![[Pasted image 20250120105855.png]]

####  Cookies:
- Identify users, such that the site remembers the user next time he visits. 
- Usecases: Authorization, shopping carts, recommendations etc.. 
- Debate: "Third party cookies" tracks the users through multiple sites, which have privacy concerns. 

#### Web Caches 

- Goal: Want to satisfy client request without involving origin server-> Reduces response time for client, reduce traffic on origin server. 
- Less and less common.
![[Pasted image 20250120104918.png]]



##### SMTP: Simple mail transfer protocol. 
![[Pasted image 20250120110202.png]]

- Sender uploads email message to her mail server
- The email is sent to the receivers email sever, which the receivers can get via different protocols(Today often HTTP)


##### DNS: Domain name system.
Function: Translate IP-addresses to "site-names"

- Possible to have multiple domains pointing to the same IP. 
- By the same logic as having multiple layers to ISPs, we have different layers with DNS servers. Single server would not be able to handle the network load. 
![[Pasted image 20250120110654.png]]
- The top level domain server are responsible for translating all domain/IPs with "their ending, ex .com". 
- In practice a client uses a local dns server to query the IP of a domain. We do this in 2 ways:
**Iterated DNS query**
![[Pasted image 20250123103931.png]]

**Recursive query (default way):**
![[Pasted image 20250123104147.png]]


- We also do **Caching** of DNS records, and we specify the length of the caching by a **Time to live(TTL)** parameter in the DNS record. 
- We also have different types of DNS record:
![[Pasted image 20250123104608.png]]

##### IMAP: Internet message access protocol. 

#### Peer 2 Peer:
**File distribution in Client-server:**
![[Pasted image 20250123110108.png]]
- The max function find the bottleneck in the process, from the server to the network or from the network to the client. It is hard to get a function for the exact distribution time, but we know for sure the bottleneck is a lower bound.  

**File distribution in P2P:**
![[Pasted image 20250123110928.png]]
- The added term represents the case where the file which is to be distributed is on all other peers. This way we use all the peers in the network to distribute N copies of a file of size F. 


#### Video streaming and content distribution
- Video streaming ~80% of internet traffic 
- Q: How to scale this with >1B users where each user may have different capabilities in terms of download speed and hardware? 
- A: Distributed, application-level infrastructure

**Multimedia - Video**
- An image is an array of pixels, and each pixel is represented by bits.
- When we have a video, we are streaming a sequence of images, in at least 24 images/sec 
- There are different ways of coding this; the naive way: stream the video image by image, a smarter way:  Stream the change of pixels between each image. 

One problem in sending video is the bandwidth variety from the server to the client, one fix is to:
- Stream the video with delay, such that the client views early parts of the video while the server still sending the later parts. 

**DASH: Dynamic Adaptive streaming over HTTP**

**CDN: Content distribution networks**
