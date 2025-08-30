#IncidentHandeling #SOC #BlueTeaming #hackthebox


Definitions:
- An `event` is an action occurring in a system or network.
- An `incident` is an event with a negative consequence. One example of an incident is a system crash. Another example is unauthorized access to sensitive data. Incidents can also occur due to natural disasters, power failures, etc.



Additional resources:
Incident Handling Guide NIST: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



### Cyber kill chain:
![[Pasted image 20250217145328.png]]

- **Recon:** gather information about the target, ex: os, Antivirus, networking technology etc.
- **Weaponization:** Creating / fitting malware. 
- **Delivery:** Payload is delivered to the target: ex. phishing emails.
- **Exploitation:** Execution of payload on target.
- **Installation:** Installation of malware such as backdoors, droppers, rootkits etc.
- **Command and control:** Gaining better foothold 
- **Action:** Achieving the objective of the attack, example steal data. 


### IH process:
![[Pasted image 20250217150432.png]]

Main activities: `investigating` and `recovering`.
- Discover the initial 'patient zero' victim and create an (ongoing if still active) incident timeline
- Determine what tools and malware the adversary used
- Document the compromised systems and what the adversary has done

**Preparation stage:**
- DMARC: for phising mails:  https://dmarcly.com/blog/how-to-implement-dmarc-dkim-spf-to-stop-email-spoofing-phishing-the-definitive-guide#what-is-dmarc 
- End point hardening: 
- Network protection - IDS / IPS systems:
- Password management
- Vulnerability scanning:
- User awareness:
- Active directory security assessment:
- Purple team exercises:

**Detection and Analysis stage:**
Essential questions to answer for the initial investigation: 
```
- Date/Time when the incident was reported. Additionally, who detected the incident and/or who reported it?
- How was the incident detected?
- What was the incident? Phishing? System unavailability? etc.
- Assemble a list of impacted systems (if relevant)
- Document who has accessed the impacted systems and what actions have been taken. Make a note of whether this is an ongoing incident or the suspicious activity has been stopped
- Physical location, operating systems, IP addresses and hostnames, system owner, system's purpose, current state of the system
- (If malware is involved) List of IP addresses, time and date of detection, type of malware, systems impacted, export of malicious files with forensic information on them (such as hashes, copies of the files, etc.)
```

Questions to answer for the incident severity:
```
- What is the exploitation impact?
- What are the exploitation requirements?
- Can any business-critical systems be affected by the incident?
- Are there any suggested remediation steps?
- How many systems have been impacted?
- Is the exploit being used in the wild?
- Does the exploit have any worm-like capabilities?
```

We generally divide the detection and analysis stage into 3 phases which we cycle through. 
```
1. Creation and usage of indicators of compromise (IOC)
2. Identification of new leads and impacted systems
3. Data collection and analysis from the new leads and impacted systems
```

IOC: "An indicator of compromise is a sign that an incident has occurred. IOCs are documented in a structured manner, which represents the artifacts of the compromise. Examples of IOCs can be IP addresses, hash values of files, and file names."


**Containment, eradication and recovery stage:**

Containment:
- Goal: Contain the damage
- Short term example: place a system in a separate VLAN  
- Long term example: changing user passwords, apply firewall rules, apply system patches. 

Eradication:
- Goal: Eliminate the root cause of the incident to ensure the attacker is out of the systems and networks.
- Example: Remove malware, rebuilding systems. 

Recovery: 
- Goal: Bring system back to normal operation.
- Verify all systems are up, and necessary data in place 
- Apply added logging and monitoring on systems that where compromised. 

**Post-Incident Activity stage:**

Reflecting on the incident, what we learned and so on. 

Content that should be in a report:
```
- What happened and when?
- Performance of the team dealing with the incident in regard to plans, playbooks, policies, and procedures
- Did the business provide the necessary information and respond promptly to aid in handling the incident in an efficient manner? What can be improved?
- What actions have been implemented to contain and eradicate the incident?
- What preventive measures should be put in place to prevent similar incidents in the future?
- What tools and resources are needed to detect and analyze similar incidents in the future?
```

