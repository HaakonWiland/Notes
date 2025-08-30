#BlueTeaming #tryhackme

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
- **SIEM: Security information and event management** 
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
```
- vol3 -f <memory image file> <argument about what you want to look at> 
```

- **pdfinfo:** CLT for checking metadata of a .pdf file. 
- **exiftool:** Find the GPS coordinates of an image 

Random-Tip: If you want to search on GPS coordinates on google maps, have it in this format:
```
EX:
51° 30' 51.90" N, 0° 5' 38.73" W
```


- oledump.py: Python tool for analysing OLE2 files(Structured Storage files)
```
- oledump.py <filename>
- oledump.py <filename> -s <number> --vbadecompress: investegate a spesific data stream in the file.
```

- INETSim: Internet server simulation suite - used to simulate real network interactions. 
- FlareVM: A standard VM which contain A LOT of preinstalled tools 
```
MOST IMPORTENT IN FLARE VM
- procmon
- procexp
- PEstudio
- Wireshark 
- CFF Explorer 

Random tips: It can be smart to verify findings from one tool with another tool.
```
- Virus total: Allow us to compare hash of a file to a database of known malware. https://www.virustotal.com/gui/home/upload


#### CAPA: Identifying malicious capabilities in files 
"Automates the tasks for reverse engineering for malware in a file"

```
capa.exe <file we want to analyse>
```


Overview of MBC (Malware behavior catalog ): https://github.com/MBCProject/mbc-markdown/blob/main/mbc_summary.md 

Malware has different aspects to them:
- Objective 
- Behavior 
- Methods 

CAPA categorize capabilities of the file by different namespaces(Think of them as just categories with under categories): https://github.com/MBCProject/capa-rules-1?tab=readme-ov-file#namespace-organization 

We can convert the output of a --vv output(verbose) to a .json file, then upload this to: https://mandiant.github.io/capa/explorer/#/. This way we can practically get an overview of the analysis of the file, including viewing the reason why a certain property as flagged.  





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

