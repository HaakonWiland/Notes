#web #CWES #Reconnaissance


#### Goal of reconnaissance:
- Identify accessible components: pages, subdomains, ip addresses, with more. 
- Identify hidden information: backup files, config files, etc
- Analyzing attack surface 
- Gather information: Information that can be used for further exploitation. 

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
- WHOIS lookup: Find information about the domain 
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

#### Virtual hosts:
- Allows multiple sites or applications to be hosted on a single server, hence 1 IP can have multiple sites. 
- configured within a web server, not in the DNS. 
- Websites can have subdomains that does not appear in the DNS records, VHOST fuzzing can be used to discover public and non-public subdomains and Vhosts. Done by testing different hostnames (HTTP header) against a known IP address. 
![[Pasted image 20251202172009.png]]

**Types of VHosts:** (Start here.)
