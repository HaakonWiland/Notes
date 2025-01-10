

**Tunneling** refers to the process of securely transmitting data from one network to another by encapsulating it inside another protocol. It's commonly used to securely transfer data over unsecured networks, such as the internet, by encrypting the communication and forwarding traffic between different endpoints.

- **Local Port Forwarding**: Allows you to access **services on the remote machine** from your local machine. It forwards traffic from a local port to a remote port.
- **Remote Port Forwarding**: Allows you to expose **services running on your local machine** to the remote machine. It forwards traffic from a remote port to your local port.


After the nmap we find a ftp and a SSH service. We can without authentication connect to the ftp: 

```
ftp ftp://10.129.228.195:21
```

The download the files with `get` commad. There we find a password and some login creds, which we use to login into the SSH. There we look at the services running at localhost:

![[Pasted image 20241023212145.png]]

Q: Why can we not connect to the postgresql directly from the SSH? 
-> The machine we are SSH'ed into does not have psql installed, and we dont have premisstion to install it. 

We make a tunnel from out local machine to the remote machine 
![[Pasted image 20241023214223.png]]

Verify that it runs: 
![[Pasted image 20241023214235.png]]

Connect to the database via the tunnel. 
![[Pasted image 20241023214916.png]]


Explore the database, and get the flag. 
![[Pasted image 20241023220227.png]]

![[Pasted image 20241023220239.png]]