#networking 

### ISO OSI MODEL
![[Pasted image 20241113213159.png]]

### TCP/IP MODEL 
![[Pasted image 20241113213301.png]]


The 3 ranges for private IP addresses:
- `10.0.0.0` - `10.255.255.255` (`10/8`)
- `172.16.0.0` - `172.31.255.255` (`172.16/12`)
- `192.168.0.0` - `192.168.255.255` (`192.168/16`)


#### NAT: Network address translation:
- Translating private ip addresses to public once  
![[Pasted image 20241114194722.png]]


### Transport layer protocols

#### UDP:
- Allows a client to reach a specific process on the target host. 
- Connection less -> "Mail service with no delivery confirmation"
- Faster then transport protocols that provides confirmation 
- Often used when sending streaming video or audio 

#### TCP: 
- Connection oriented -> Need a TCP connection before data can be sent. 
- Confirmation of packages 


### Encapsulation
*Encapsulation refers to the process of every layer adding a header (and sometimes a trailer) to the received unit of data and sending the “encapsulated” unit to the layer below.*

![[Pasted image 20241113214250.png]]



### Some other protocols:

#### DHCP: Discover offer request acknowledge 
- Issues an ip address for the client when it connects to a new network 

1. **DHCP Discover**: The client broadcasts a DHCPDISCOVER message seeking the local DHCP server if one exists.
2. **DHCP Offer**: The server responds with a DHCPOFFER message with an IP address available for the client to accept.
3. **DHCP Request**: The client responds with a DHCPREQUEST message to indicate that it has accepted the offered IP.
4. **DHCP Acknowledge**: The server responds with a DHCPACK message to confirm that the offered IP address is now assigned to this client.


#### ARP: Address Resolution Protocol
- Makes it possible to find the MAC address of another device on the Ethernet. (Works on both wired and wireless local networks)

#### ICMP: Internet Control Message Protocol
- Used for network diagnostics and error reporting. 
- Commands as ping and traceroute is reply on this protocol


#### Default ports of common protocols:
![[Pasted image 20241114201909.png]]

- Note the protocols on the left is application layer protocols (All of them?) and are build using TCP/UDP. 


### tcpdump:
![[Pasted image 20241115220015.png]]
![[Pasted image 20241115223259.png]]
![[Pasted image 20241115224637.png]]


#### nmap:
![[Pasted image 20241115232709.png]]
![[Pasted image 20241115233321.png]]
![[Pasted image 20241115233631.png]]
![[Pasted image 20241115234018.png]]


### Network Security:

#### Passive recon:
![[Pasted image 20250115131728.png]]


Usefull commands:
- **whois:** `whois <domain/ip>` Gives us lots of info on a spesific domain such as registry, whois-server, etc..
- **nslookup:**  `nslookup OPTIONS DOMAIN_NAME SERVER` , finds the ip address of a domain. We can also specify different ISP-DNS-server: This command can be useful in a pentest setting, where we test the security of different IPs of the domain. 
![[Pasted image 20250115125251.png]]
- **dig:** `dig @SERVER DOMAIN_NAME TYPE` , Similar to nslookup, but usually gives more information and it has more options. 

Useful sites:
- **DNSdumpster:** Find subdomains.
- **Shodan.io:** IP address, Hosting company, geo location, server type etc ... Checkout:  https://tryhackme.com/r/room/shodan


#### Active recon:

**Basic CLI tools:** 
![[Pasted image 20250116123015.png]]


**NMAP SCANNING:**

Scanning range example: 
```
nmap 10.10.12.13/29
-> Scans the range: 10.10.12.8 ... 10.10.12.15
WHY: 32-29 = 3 and 2^3 = 8, so the block sizeis 8. Then the network address(the one we start to scan) is: ⌊13/8⌋⋅8=8 i.e we start at 10.10.12.8. 

```
