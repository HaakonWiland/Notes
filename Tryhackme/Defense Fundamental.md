

### People:
SOC team with different responsibilities 

### Process:
**Alert Triage:**
- WHO 
- WHAT
- WHERE
- WHEN
- WHY

### Technology:
Tools for automating detection and response, example:
- **SEIM: Security information and event management** 
- **EDR: Endpoint Detection and response**
- **Firewall:**
- **IDS:** Intrusion detection system 


#### IDS: 
"Detection behind the firewall and inside the system"

**Types:**
- HIDS: (Host intrusion detection system): On a specific host such as a computer 
- NIDS(Network intrusion detection system): On the whole network

**Modes:**
- **Signature based IDS:** Identify unique signature behind a known attack
- **Anomaly-Based IDS:** Compare current state from baseline -> Big differences get detected
- **Hybrid based IDS:** Use both signature and anomaly 

**IDS example: Snort:**
- Packet sniffer mode, packet logging mode, **Network intrusion detection system mode**

```
All related to Snort can be found in /etc/snort 
- (Fill in typical commands.) 



```

Rule format: 
![[Pasted image 20241128213807.png]]


#### Vulnerability scanning:
We need know potential "holes" in our systems such that we can fix them, vulnerability scanning helps us identify those holes. 

**Types of scanning:**
- Authenticated: We get credentials to the system we scan 
- Unauthenticated: We do not get the credential to the system we scan
- Internal: Look for holes on the inside of our network
- External: Look for holes on the outside of our network. 

**Common tools for scanning:**
- Nessus: https://www.tenable.com/products/nessus
- Qualys: https://www.qualys.com/
- Nexpose: https://www.rapid7.com/products/nexpose/ 
- OpenVAS: https://www.openvas.org/




#### Forensics tools:
- **FTK imager**: Tool for taking images of windows disk.
- **Autopsy:** forensics platform to do analysis of disk images:  https://www.autopsy.com/ 
- **Dumplt:** Tool for taking memory image https://www.toolwar.com/2014/01/dumpit-memory-dump-tools.html
- **Volatility:** Tool for analyzing memory images, works for windows, linux, macOS and android https://volatilityfoundation.org/


- **pdfinfo:** CLT for checking metadata of a .pdf file. 
- **exiftool:** Find the GPS coordinates of an image 

Tip: If you want to seach on GPS coordinates on google maps, have it in this format:
```
EX:
51° 30' 51.90" N, 0° 5' 38.73" W
```


#### Incident response:

**SANS framework for IR:**

![[Pasted image 20241127205904.png]]

#### Logs:

**Different types of logs:**
![[Pasted image 20241127211206.png]]


**Windows Event Viewer IDs:**
![[Pasted image 20241127212654.png]]


#### Firewalls:

![[Pasted image 20241128142800.png]]


Windows firewall: **Windows defender firewall**
- Can make rules for private network and public network (network profiles)
- View, turn off/on, and make new  firewall settings  

**Linux:**
Common firewall solution:

**Netfilter:** A framework inside Linux OS for firewall functionalities, is the basis for: 
- iptables: Most common 
- nftables: Successor of iptables, with enhanced packet filtering and NAT capabilities.
- firewalld: 

Easy way to interact with iptables: **ufw**
```
- uwf status: Firewall on or off
- uft enable / ufw disable: 
- uft default allow outgoing: Allow all outgoing traffic 
- uft deny 22/tcp: Dont allow (incomming?)traffic on ssh port
- ufw status numbered: show all active rules. 
```

