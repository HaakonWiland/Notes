#TTM4100 #firewalls #TLS #IPsec


Goals:
- Confidentiality
- Authentication: Not just person to person, also machines, servers etc. 
- Message integrity
- Access and availability 

Relevant question(s) to ask:
- "What is the risk associated with a communication "

Network attacks:
- Eavesdrop
- Impersonation
- Hijacking
- Denial of service

Mitigation and preparation: 
- ROS analysis 


#### Public key Certification Authorities(CA):
Problem: We want to know how owns a public key, and trust that they are who they claim they are. 

Solution: CA binds public key to a particular entity(PC, person, server, browser etc)
The assumption is that we can trust the CA.
![[Pasted image 20250324092644.png]]

So if Alice wants Bobs public key, she gets bob certificate. Ex: if bob is a webserver, alice get the certification at connection time. 

In major web browsers, a copy of the public key of some CAs exists, and when visiting a website, the browser check if any of public keys work. 


#### Transport Layer Security(TLS):
- Provides secure stream of ANY data. 
- Protocol between TCP and application layer 
![[Pasted image 20250417132010.png]]


Provides:
- Confidentiality: Via symmetric encryption
- Integrity: Via hashing
- Authentication: Via public key cryptography  

Steps - roughly:
1. Handshake: Use certificates and private keys to exchange shared secret 
2. Key derivation: Alice and bob derive key from shared secret 
3. Data transfer: Stream data transfer, as encrypted data 
4. Connection closure: Special message to close the connection 


### Network layer security:

Problem: Institutions want private networks for security resons. This is costly because separate routers, links, DNS infrastructure..

Solution: VPN - virtual private network 
![[Pasted image 20250327081942.png]]

- NOTE: the communication may be secure, but the single devices by not be secure(someone can just take their pc, and communicate over the vpn)


#### IPsec
For exam: Only need to understand the basic aspect and differences between options 

Provides: 
- Confidentiality (datagram encryption)
- Origin authentication
- data Integrity
- Replay attack prevention 

**Transport mode:**
- **Only datagram payload** is encrypted, authenticated 

**Tunnel mode:**
- **Entire datagram** is encrypted, authenticated 
- Encrypted datagram encapsulated in new datagram with new IP, header, tunneled to destination 


**Two IPsec protocols:**

Authentication Header protocol (AH):
- Provides source authentication and data integrity but **NOT CONFIDENTIALITY**. 

Encapsulation Security Protocol (ESP):
- Provides source authentication, data integrity and confidentiality 
- Most common version! 



### Security in wireless and mobile networks:
Details on how key sharing are not very impotent for exam 

#### 802.11 security:
Arriving mobile must:
- Associate with access point 
- Authenticate to network 

Steps:
**STEP 1:** Discovery of security capabilities 
![[Pasted image 20250327083237.png]]
- AP advertises it presence, forms of authentication and encryption provided 

**STEP 2:** Mutual authentication and shared symmetric key derivation 
![[Pasted image 20250327083459.png]]
- AS and mobile derive symmetric session key to be used for frame encryption. They have to communicate shared secrets, nonces and hashes to do this. 

Ex. 802.11 WPA3 handshake:
![[Pasted image 20250327083941.png]]

**STEP 3:** Shared symmetric session key distribution 
![[Pasted image 20250327084224.png]]


**STEP 4:** Encrypted communication between mobile and remote host via AP:
![[Pasted image 20250327084323.png]]


#### 4G/5G security:

Arriving mobile must:
- Associated with BS(Base station)
- Authentication itself to network and authenticate the network 

Noteable differences from wifi:


STEPS:

STEP 1: Authntication request to home network HSS


STEP 2: HSS use shared in advance secret key to derive authentication token, and expected authentication response token

STEP 3: Authentication response from mobile 

STEP 4: Mobile is authenticated by network 

STEP 5: Mobile and BS determine keys for encryption data and control frames over 4G wireless channel 


**Differences when moving to 5G:**
![[Pasted image 20250327085507.png]]


### Operation security: Firewalls 
This part is impotent for exam! 

**Firewall:** Isolated organizations internal network from larger internet, allowing some packets to pass, blocking others.

goals:
- Prevent denial of service attacks
- prevent illegal modification/access of internal data 
- allow only authorized access to inside the network 

Types of firewall:
- Stateless packet filters 
- Stateful packet filters 
- Application gateways 

**Stateless packet filter:**
![[Pasted image 20250403082142.png]]

- Do not look at the sequence / pattern of packets, just filter packet by packet based on configuration. 
- Ex:![[Pasted image 20250403082705.png]]

- Access Control Lists(ACL): Table of rules![[Pasted image 20250403083206.png]]


**Stateful packet filtering:**
- Added filtering: Track status of packets and connections 
- Ex:![[Pasted image 20250403083749.png]]

**Application gateways:**
- Filter packets on application data as well as on IP/TCP/UDP fields.

**Limitations of firewalls:**
- IP spoofing - router dont know where the data actually comes from 
- If multiple apps need special treatment, each has own app gatway 
- Client software must know hot to contact gateway 