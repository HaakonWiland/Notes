
Default nmap scan: ? 
`nmap -sC -sV <ip>`

Basic hydra: (-L, -l) (-P, -p) 
`hydra -L usernames.txt -p 'passowordexample' {target_IP} {protocol}`

socket statistics: popular options: 
`ss`
-t: display tcp ports, 
-l: display listening sockets 
-n: resolve service names. ei. show the name of the service insted of the port. 

Connecting to ssh: 
`ssh user@<ip> -p 22`
