
---
tags:
- #IT 
- #networking
---


```
wlp3s0: flags=4xxx<UP,BROADCAST,RUNNING, ....>  mtu 1500
        inet 192.168.x.xxx netmask 255.xxx.xxx.x  broadcast 192.168.x.xxx
        inet6 fe80::681e:1102:1292:aa98  prefixlen 64  scopeid 0x20<link>
        ether xx:xx:xx:xx:xx:xx  txqueuelen 1000  (Ethernet)
        RX packets 17606  bytes 4547153 (4.5 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2566  bytes 306579 (306.5 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

- flags: Provide information about current state and capabilities of network interface. UP: means it is active, BROADCAST: means if allows data to be sent to all devices on the same network etc... The number for flags=4163 is a binary rep. of the flags associated with the network. 

- inet: The IPv4 address of the our device on our current network. 
- netmask: Determines the range of IP addresses that are considered part of the local network. 
- broadcast: IP address used for broadcasting messages to all devices on the local network.  

- ether: MAC (Media Access Control) address of out network interface, globally unique for out device. 



- NETWORK: Collection of devices or systems that can communicate and share resources with each other. 
- NETWORK INTERFACE: Hardware or software component that enables a device to connect to a network. 
