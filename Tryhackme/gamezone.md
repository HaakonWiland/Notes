#web #tryhackme 
Lessons:
- Can ssh tunnel to interact with services that are not normally accessable: 
```
-L is a local tunnel (YOU <-- CLIENT). If a site was blocked, you can forward the traffic to a server you own and view it. For example, if imgur was blocked at work, you can do **ssh -L 9000:imgur.com:80 user@example.com.** Going to localhost:9000 on your machine, will load imgur traffic using your other server.

-R is a remote tunnel (YOU --> CLIENT). You forward your traffic to the other server for others to view. Similar to the example above, but in reverse.
```

- Metasploit still a bit messy for me. What exploits to use, and what payloads to choose. I think just try different combinations that seem to fit the info i know about the target. 

-------


- We have a website with login, we can sql injection with:
```
username field:' or 1=1-- -

NOTE: ' or 1=1-- 
Does not work! Some databases require that we have a space after we are declaring a comment. 
```

- We then get redirected to another page: `/portal.php`
- On this page there is a single input field, we send some data into it and save the request:
![[Pasted image 20250209144313.png]]

- We send this request to sqlmap:
```
sqlmap -r request2.txt --dbms=mysql --dump
```
- (We assume the data base manager is mysql.. and it is)
- Here we find that the searchitem post paramter is vulnerable to sql injection, and retreve a password hash:

DATABASE:
- Database: db 
- Table: post
- Cols: id, name, description 

We find 5 entries 


- Database: db
- Table: users
- Cols: pwd, username 

We find 1 entry


We crack the hash via crackstation:
username: agent47
password: videogamer124


- We login with this new login info. We get redirected /portal.php again. Session id of the user: `shpu5h2njc76505fbilq959lk4`

- The same login info worked on the ssh services on the box. 

We got the user flag, now we want the root flag. We are given a hint that we want to use ssh tunnels to achive this. So we start by checking the tcp sockets:
```
ss -tulpn
```

![[Pasted image 20250213141957.png]]

- Observe a non standard tcp port (10 000) is listening, but it is blocked via a firewall from the outside(can check this with `iptables` or `ufw` but we need sudo to do this)

We can ssh forward the traffic to this service:
`ssh -L 10000:localhost:10000 agent47@10.10.85.39`

- this lets up interact with localhost:10000 on our local machine, and the data we send to here is forwarded to localhost:10000 on the target machine. 

We now can look at the services hosted at 10000 on the target, by typing: `localhost:10000` in our browser. We find some CMS(content management system) service, and try to login as agent47.

![[Pasted image 20250213144149.png]]


using metasploit we find a module which fits the webmin version: 
![[Pasted image 20250213144513.png]]

Then we have to spesify the payload:
![[Pasted image 20250213153234.png]]

- The target has python installed, so we can use: `cmd/unix/reverse_python`
![[Pasted image 20250213153623.png]]

- Notice: we set RHOSTS to localhost, since we need to forward the payload. Since we have establiched the ssh tunnel between the target and our local machine, anything we send to localhost:10000 gets sent to the target:10000. 
- Notice: We use LHOST to the ip address of our local machine on the target network. It does not work if we use our eth0 ip address. 
- Once the exploit creates a session for us, we can easily get the root flag 