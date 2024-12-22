
### Connecting and listening for connections

#### Netcat  (nc):

Connecting to port:
```
nc <ip> <port>
```

- **-l:** listen 
- **-p**: spesify port we want to listen from 

#### Network mapping:

Scanning a larger network: (/16 and down)
```
nmap -p <port> <ip range> --min-rate 1000
```

### Tcpdump:

Displays and handles tcp packages

- **-i:** Specify the network interface we want to listen on, "any" means we listen to all.

Human readable format, real time in terminal:
```
sudo tcpdump -i any port <ip>
```

Raw package data, saved in a file:
```
sudo tcpdump -i any port <ip> -w data.txt
```

