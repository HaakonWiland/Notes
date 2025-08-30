#SOC #ELK #KQL #hackthebox #BlueTeaming 
lingo:
- **Adversary** 
- **TTP:** Tactics, Techniques and Procedures 
- **Indicator:** Technical data + contextual information -> Give us a indicator as to what have happened 
- **Threat:** Intent, capability and opportunity 
- **Campaign:** Collection of incidents that share TTPs and are believed to have comparable collection requirements. 
- **IOC:** A signpost of specific adversary or malicious activity 
- **Pyramide of pain:**
- **Diamond model**
- **CTI:** Cyber threat intelligence 



-----

Questions:
- How can i filter for file path in KQL with wildcard characters? Since file.path: C:\Users\Public* does not work? Same with file.directoy. 
- What is the difference between event id and event code? 
-----

**Some nice KQL queries:**
- Check for events where the registry value has been set and the path of the registry has `SOFTWARE\Microsoft\Windows\CurrentVersion\Run` in it. This is a useful query for checking for persistence via registry run keys:  https://attack.mitre.org/techniques/T1547/001/
```
event.code: 13 AND registry.path:*SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Run*
```

- The event code 4104 is really useful to check event of suspicious powershell code being executed. 
```
event.code: 4104
```

-----

#### Stages of the treat hunting process 
**Setting the Sage:** 
- Planning and preparation
- get an understanding of the treat landscape, 
- the business critical requirement
- configuring tools(SIEM, IDS, EDR)

**Formulating Hypotheses:**
- Educated prediction to guide our treat hunting. Base on resent threat intelligence, industry updates, alerts from security tools.
- Make the hypotheses testable 

**Designing the hunt:**
- Develop the hunting strategy based on the hypotheses 
- Look in relevant data sources (logs)
- Sorting out methodologies and tools we are going to use 
- Defining IOCs and patters we are going to hunt for
- Develop custom scripts for hunting based on our hypothesis 

**Data gathering and examination:**
- Active threat hunting starts 
- Collect log files, network traffic, endpoint data etc.
- Analyze the data we collected 
- Goal is to find evidence that could support or falsify out hypothesis 

**Evaluating findings and test hypothesis:**
- What hypothesis hold and which hypothesis did we falsify 
- Understand the treats we detected 
- Identify affected systems 
- Determine the potential impact of the threats 

**Mitigating threats:**
- For the confirmed threats, undertake remediation actions 
- Isolating affected systems 
- Eliminating malware 
- Patching vulnerabilities 
- Modifying configurations 

**After the hunt:**
- Document and share findings, methods and outcomes 
- Enhancing detection rules 
- Refine incident response playbook
- Improve security policies 

**Continuous learning and enhancement:**
- Learn for the process


### Cyber Threat Intelligence:
4 pillars:
- **Relevance:** We are interested mainly in information that is relevant to our company / industry / partners. 
- **Timeliness:** Freshly discovered data is more valuable than aged data
- **Actionability:** We want information that yield actionable insight for our defense team. 
- **Accuracy:** Before disseminating any intelligence, we must verify for accuracy. If we are not sure, we should label the information with a confidence indicator. 

**Cyber treat intelligence:** Predictive 
**Cyber treat hunting:** Reactive and proactive 


**Other factors to consider:**
- Strategic intelligence 
- Operational intelligence 
- Tactical intelligence 


**Going through a Tactical threat intelligence report:**
1. Comprehending the scope and narrative 
2. Spotting and classifying the IOCs
3. Comprehending the attacks lifecycle 
4. Analysis and validation of IOCs
5. Incorporating the IOCs into out security infrastructure 
6. Proactive threat hunting 
7. Continuous monitoring and learning 


-----

#### Threat hunting example: 
- From a intelligence report we gained some information that we are looking for an initial attack vector in the form of a phishing mail with a OneNote file. The company uses gmail webinterface for mail 
- The file is supposed to have the name invoice.one and it is supposed 

- We start by searching for sysmon event 15 and file.name invoice.one. This check for browser file download events where the file has the name invoice.one. 
```
event.code:15 AND file.name:*invoice.one
```

- We find a few such events, but have to investigate a bit more before we can conclude anything. 

```
event.code:11 AND file.name:invoice.one*
```

- The query above is a bit more general, and will check for files created which starts with  invoice.one
- We can fine similar events as before, and take note of some essential details: hostname that downloaded the file, IP addresses, and timestamps.  

We now take a look at some network data, and select the "Zeek filter".  

```
source.ip:192.168.28.130 AND dns.question.name:*
```

- The query above checks the relevant source ip and picking logs that has a dns query name. (Since we the user would be typing in a DNS name rather then a ip). We also add filers for the relative timestamp. 
- NOTE: Depending on how much data we have, it can be a good idea to filter out some of the most common dns names such as google.com 

We now see something interesting: 

![[Pasted image 20250304220539.png]]

We can deduce that we host visited the web browser for gmail, them a file hosting site, and then some Edge defender kicks in, which indicates that the host actually downloaded some file.  

We can look a bit closer at the file.io logs, and find the relevant ip address to the dns name. 

- Then again with Zeek, we can search for the ip and filter for the relevant time frame, we can get some more information about the event. 

So now we can say: a invoice.one has been downloaded based on a email, via a file sharing website. What we now want to check is if the file was opened/ran buy the host.

```
event.code:1 AND process.command_line:*invoice.one*
```

- The query above check if a process was created with the command line argument including invoice.one somewhere. 

We fine some log that invoice.one has be opened by onenote.exe. This would mean that onenote.exe is subject for investigation. 

```
event.code:1 AND process.parent.name:"ONENOTE.EXE"
```

- The query above checks if any process has been created from onenote.exe.

And again, yes this is true. We find a log confirming that onenote.exe has created a cmd.exe process, and cmd has executed a invoice.bat file. We should now try to find out what this .bat file has done. 

```
event.code:1 AND process.parent.command_line:*invoice.bat*
```

The code above check if any process has been created from a parent process that has invoice.bat in its command line. 

We find a powershell.exe being created from the .bat file, and it is ran with some arguments:
![[Pasted image 20250304221900.png]]

We see that powershell.exe interacts with a pastebin.com site (an open text hosting provider). 

To further understand what the powershell process did, we can try filtering for the process pid. 

```
process.pid:"9944" and process.name:"powershell.exe"
```

This gives us a lot of information as to what have happened:
![[Pasted image 20250304222205.png]]

Here there is a lot of data and files we could investigate. This will take some time, but essessially follow the strategy as we have done before:

- **Check the individual files that have been downloaded, have the files been run? Have they created other processes or interacted with other parts of the system?** 
- **Check the IPs, DNS names, file hashes, are any of these know and what can they do?**
- **Based on the information you gather, make new hypothesis that is possible to falsify.** 

