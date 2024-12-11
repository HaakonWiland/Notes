


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


#### Netcat alternatives:

- rlwarp with nc: `rlwarp nc -lvnp 443` Improves the shell, lets us use arrowkeys etc
- Ncat: `ncat -lvnp 4444` improved netcat with SSL 


#### Shell payloads:
With unrestricted file upload vulnerability, we can upload one of these to get a web shell:
https://www.r57shell.net/index.php



#### SQL injection - SQLmap:

Basic example:
```

Normal query: 
SELECT * FROM users WHERE username = 'John' AND password = 'Un@detectable444';

SQL injection:
Username: John
Password: abc' OR 1=1;-- -

SELECT * FROM users WHERE username = 'John' AND password = 'abc' OR 1=1;-- -';
```


automate the task with sqlmap:

```
Basic synatx with get parameters:

sqlmap -u "http://site?email=test&.." 

Then we can add flags such as 
--dbs: Databases 
--tables: Tables 
-D: Spesify a spesific database
-T: Spesify a spesifc table 
--dump: Dump the content of a table

```


#### OSINT:
Overview of tools: https://osintframework.com/
The Harvester: https://github.com/laramies/theHarvester
email finder: https://hunter.io/


#### Web applications and the browser 
"Sometimes automated scanners miss vulnerabilities, that we could have identified doing manual work"

- We can use the **inspector** tool to change the page it our browser, example change a css display property from "block" to "none".
- Using the **debugger**, we can view the .js code running on the page, can even set breakpoints when fuzzing. 
- The **network tab** shows us requests, can inspect these to understand the flow of the application. 
