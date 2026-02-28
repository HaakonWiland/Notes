#web #CWES #Reconnaissance


#### Goal of reconnaissance:
- Identify accessible components: pages, subdomains, ip addresses, with more. 
- Identify hidden information: backup files, config files, etc
- Analyzing attack surface 
- Gather information: Information that can be used for further exploitation. 

#### Info gathering automating tools:
![[Pasted image 20251207140130.png]]


**Active recon:** Attacker interact directly with the target system
- Port scanning 
- Vuln scanning (ex with nessus)
- Network mapping: To map the targets network topology, can example use traceroute or nmap
- Banner grabbing 
- OS fingerprinting
- Service Enumeration: 
- Web Spidering: Identify web pages, directories and files using burp / zap. 


**Passive recon:** Gather information about the target without interacting with it
- google dorking 
- WHOIS lookup: Find information about the domain, NOTE: ONLY WORKS FOR PUBLIC DOMAINS, will not work for domain inside a box. 
- DNS: Analyze DNS records to identify subdomains, mail servers etc. Can use `dig` , `nslookup`, or other tools.
- Web Archive analysis 
- SOME analysis
- Code analysis: Check github. 

#### DNS
See other notes on DNS.
![[Pasted image 20251201171401.png]]


**Host files:** Override the DNS process manually
- `C:\Windows\System32\drivers\etc\hosts`
- `/etc/hosts`

Can be used for testing connectivity, or blocking unwanted domains to non-existing IPs (0.0.0.0)

**DNS zones:** Distinct parts of the domain namespace, typical example a subdomain. 

- DNS can reveal information that is useful for pentesting; subdomains, mail servers, or other stuff giving information about the target infrastructure. 

**Tools for DNS recon:**
![[Pasted image 20251201171457.png]]
![[Pasted image 20251201172317.png]]

#### Subdomains:
- We want to map these out, it can contain: Dev environments, hidden login portals, legacy applications, sensitive information. 

**Active subdomain enum:**
- Brute-force / scan for subdomains: dnsenum, ffuf, gobuster. 
When scanning, usually have a wordlist: this can be a general-prupose, targeted for an industry, or custom made based on keywords, patters or other information gathered. 

**Passive subdomain enum:**
- Certificate transparency logs, public repos of TLS certs. 
- Google search, with filters s.a. `site:`
- Other online tools 

**DNS zone transfer:**
- If DNS is configured incorrectly, we can request a DNS transfer. 
- This could potentially give us the full DNS record including a full list of all subdomains.  

1. Figure out the authoritative for the domain `dig ns <domain>` (if domain only exist in a box: `dig ns <box ip> <domain>`)
2. Try to preform a zone transfer: `dig axfr <authoritative server> <domain>`, for a htb box: `dig axfr <box ip> <domain>`

**Certification Transparency logs:**
- Transparent log of issuance of TLS certs. 
- Can be used to find subdomains
![[Pasted image 20251203174158.png]]
- Can use websites of the tools above, use use their APIs:
```shell
curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[]
 | select(.name_value | contains("dev")) | .name_value' | sort -u
```

Will give an output like this:
```shell
*.dev.facebook.com
*.newdev.facebook.com
*.secure.dev.facebook.com
dev.facebook.com
devvm1958.ftw3.facebook.com
facebook-amex-dev.facebook.com
facebook-amex-sign-enc-dev.facebook.com
newdev.facebook.com
secure.dev.facebook.com
```


#### Virtual hosts:
- Allows multiple sites or applications to be hosted on a single server, hence 1 IP can have multiple sites. 
- configured within a web server, not in the DNS. 
- Websites can have subdomains that does not appear in the DNS records, VHOST fuzzing can be used to discover public and non-public subdomains and Vhosts. Done by testing different hostnames (HTTP header) against a known IP address. 
![[Pasted image 20251202172009.png]]

##### Types of VHosts: 
**Name based virtual hosting**
- Relies solely on the HTTP host header to distinguish between websites 
- Does not require multiple IP addresses. 

**IP based virtual hosting:**
- Unique IP address to each website hosted on the server. 
- "better isolation" between the websites 
- Does not rely on the Host header

**Port-based virtual hosting:**
- Use different ports to host different websites 
- Kind of rare 

##### Tools to discover Vhosts:
![[Pasted image 20251203163810.png]]

Example gobuster with vhost:
`gobuster vhost -u "http://inlanefreight.htb:59327" -w DNS/subdomains-top1million-110000.txt --append-domain`

Example gobuster with dir:
`gobuster dir -u "http://inlanefreight.htb:53701" -w directory-list-2.3-small.txt`

- **Keep in mind we might have to edit out /etc/hosts file, if the domain is "inside" the box.** 
- Also, subdomains we find might to be visible in the browser, try to add them to /etc/hosts with the ip of the main domain

#### Fingerprinting 
- Extracting technical details about technologies powering a website or web application 

**Stuff we want to know:**
- webservers
- OS versions 
- programming language 
- WAF - does it exist, and what does it block 

**Methods:**
- Banner grabbing 
- Analysis of HTTP headers: Server, X-Powered-by, etc. 
- Probing: Sending spesific requests, and looking at the response of the app, which can revel something about the technology behind the app.
- Source code analysis. 

**Tools:**
![[Pasted image 20251203180025.png]]
- Nikto: Tries to identify old software, insecure files and configs.

#### Crawling:
- Exploring and mapping out the structure of the web application; different sites, directories, subdomains and so on. 
- Can use tools or scripts to automate this process
- There is also: **robots.txt**, **.Well-Known/** which are special files/directories which can have useful information. 


#### Skill assessment:

```
http://web1337.inlanefreight.htb:38450/admin_h1dd3n

HTTP/1.1 301 Moved Permanently
Server: nginx/1.26.1
Date: Sun, 07 Dec 2025 16:02:05 GMT
Content-Type: text/html
Content-Length: 169
Location: http://web1337.inlanefreight.htb/admin_h1dd3n/
Connection: keep-alive
```

- Go to the redirected site (WITH a trailing /, the browser cannot figure that out itself apparently)
- We get an API key `e963d863ee0e82ba7080fbf558ca0d3f`

Things i have tried:
- Look for subdomains; but internal dns, cannot do a lookup 
- OWASP spider to crawl: but no links to follow, dont find any directories 
- gobuster dir: bruteforce dirs, 2-3-big.txt and common.txt, no findings 
- gobuster vhosts: bruteforce vhosts, find web1337, find the robots.txt and the admin panel. 
- gobuster dir the web1337-vhost: dont find anything with dirb/big.txt  , only robots.txt (without -f option)

Getting some help:
- **You can look for vhost, to the vhost!** (nested vhosts?), we try scanning for vhosts in the web1337 vhost. 
- Speed up gobuster with -t 60, for more threads

- We then find a dev.web1337, which has some html files and we find what we are looking for doing a systematic bruteforce. 
- Could also have tried reconspider: 
```
python3 ReconSpider.py http://dev.web1337.inlanefreight.htb:52590
```

NOTE: Trailing slash, the normal thing to do is have the slash when it is a directory and remove it when it is a file. BUT is it not a rule 