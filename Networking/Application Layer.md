#networking #protocols #TTM4100




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
See: [[Transport Layer]] , [[Networks and Network Security]]
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


**HTTP/2:**
Motivation: Decreased delay in multi-object HTTP requests. 






####  Cookies:


#### Web Caches 

