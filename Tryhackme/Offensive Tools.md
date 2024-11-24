


#### Hydra:


**HTTP POST Example:** 
```
sudo hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.10.220.149 http-post-form "/login:username=^USER^&password=^PASS^:F=Your username or password is incorrect" -V 
```

Bruteforce molly's password using rockyou.txt, we spesify the ip and that this is a post request. We also have to spesify the site we are on(in the url we see the /login, then spesify the parameters that we want to bruteforce). Also we have to spesify the error we get when trying wrong credentials. 

**ssh Example:**
```
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.10.220.149 -t 4 ssh -V
```



#### Gobuster: 
It enumerates web directories, DNS subdomains, vhosts, Amazon S3 buckets, and Google Cloud Storage by brute force, using specific wordlists and handling the incoming responses


**Basic directory enumeration:**
```
root@ip-10-10-63-65:/usr/share/wordlists/dirbuster# gobuster dir -u "http://www.offensivetools.thm" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -r 
```

Note: By default, the dir mode does not work recursively e.i. it does not go into subdirectories. 

Useful:
```
gobuster -h: Overview over differnt commands
gobuster dir -h: Overview over different flags for dir command
```


**Basic DNS subdomain enumeration:** 
```
root@ip-10-10-205-161:/usr/share/wordlists/SecLists/Discovery/DNS# gobuster dns -d offensivetools.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt 
```


#### Vhost vs DNS subdomains: 

**vhost:** Virtual hosts are a server-side configuration mechanism used by web servers (like Apache or Nginx) to host multiple websites or applications on a single server or IP address

**Subdomains:** DNS subdomains are sub-level domains created within the Domain Name System hierarchy. They are part of the domain name and used to organize resources under a parent domain.
![[Pasted image 20241124200929.png]]

In gobuster: 
- `vhost` mode will navigate to the URL created by combining the configured HOSTNAME (-u flag) with an entry of a wordlist.
- `dns` mode will do a DNS lookup to the FQDN created by combining the configured domain name (-d flag) with an entry of a wordlist.


Basic vhost example:
```
root@ip-10-10-205-161:/usr/share/wordlists/SecLists/Discovery/DNS# gobuster vhost -u "http://10.10.106.172" --domain offensivetools.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain --exclude-length 250-320
```

Since we sending actual webrequests (insted of doing dsn lookup) we have to spesify a few more flags then for the dns scan. 

Notice we need to spesify both the url and the domain! The --append-domain flag does also need to be set for it to work.  


Basic vHost enumeration:
