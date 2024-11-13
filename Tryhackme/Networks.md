

### ISO OSI MODEL
![[Pasted image 20241113213159.png]]

### TCP/IP MODEL 
![[Pasted image 20241113213301.png]]


The 3 ranges for private IP addresses:
- `10.0.0.0` - `10.255.255.255` (`10/8`)
- `172.16.0.0` - `172.31.255.255` (`172.16/12`)
- `192.168.0.0` - `192.168.255.255` (`192.168/16`)


### Transport layer protocols

#### UDP:
- Allows a client to reach a specific process on the target host. 
- Connection less -> "Mail service with no delivery confirmation"
- Faster then transport protocols that provides confirmation 

#### TCP: 
- Connection oriented -> Need a TCP connection before data can be sent. 
- Confirmation of packages 


### Encapsulation
*Encapsulation refers to the process of every layer adding a header (and sometimes a trailer) to the received unit of data and sending the “encapsulated” unit to the layer below.*

![[Pasted image 20241113214250.png]]


