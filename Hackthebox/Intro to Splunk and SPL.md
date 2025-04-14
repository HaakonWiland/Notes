
"Splunk is a highly scalable, versatile, and robust data analytics software solution known for its ability to ingest, index, analyze, and visualize massive amounts of machine data. Splunk has the capability to drive a wide range of initiatives, encompassing cybersecurity, compliance, data pipelines, IT monitoring, observability, as well as overall IT and business management."

![[Pasted image 20250305160054.png]]

![[Pasted image 20250305160101.png]]


#### Splunk search basic commands:

HOT TIPS: Use the sidebar on the left to quickly filter for specific values. Ex:
![[Pasted image 20250305172131.png]]

Here we look at the all events with event code 4624(successful logon). From here we go to the left sidebar, and can filter further for such events where the account name was `SYSTEM`. Clicking it updates the SPL query:
![[Pasted image 20250305172412.png]]



```
| eventcount summarize=false index=* | table index
```
- Gives us a overview over the available data sources. 

```
|  metadata type=sources index=* 
|  table source
```
- Returns a list of all data sources in our splunk environment 

```
sourcetype="WinEventLog:Security" 
| table *
```
- Return all available tables for the source winlog security source. 

```
sourcetype="WinEventLog:Security" 
| fieldsummary
```
- Returns a summary over what information available in each field. 
![[Pasted image 20250305164816.png]]


```
sourcetype="WinEventLog:Security" 
| fields Account_Name, EventCode 
| table Account_Name, EventCode
```
- An example of displaying specific fields in a table format. Note we can call the table values what we want, but the field names must correspond to an actual field name if we want to show any data. 


```
EventCode=4624
| stats earliest(_time) AS first_login, latest(_time) AS last_login, count BY Account_Name
| eval duration = last_login - first_login
| where duration <= 600
| sort - count
```

- Check for successful login attempts, and group by Account_name. Then for each account name, first the earliest and latest successful login attempt. Then we calculate the duration between the first and last successful login attempt and require this to be less then 600 sec = 5 min. Then we sort the result by count.   
- Then we could add `| table Account_Name, count, duration` at the end to show specific columns. 
- This is a wierd one. 

```
`sysmon` EventCode=3 
| stats count by Image | fields Image, count
```
- This query filter for network connections detected, and uses `stats count` to compute the number of connections made per image. 
- NOTE: this was used in the splunk sysmon app, and we had spesified the datasource. If we had not, we would have to run:
```
index="main" sourcetype="wineventlog:sysmon" EventCode=3 
| stats count by Image | fields Image, count
```


#### Splunk application:
It is possible to install different application into our splunk environment to add new functionalities. For example, can we add sysmon application. 


#### Intrusion detection workflow:

**Initial steps - get an overview of the data:**
```
index="main" earliest=0
```
- Queries all of the available data. 

```
index="main" | stats count by sourcetype
```
- Queries all source types: sysmon, linux system, different windows logs etc. 

```
index="main" sourcetype=<source> EventCode=<code>
```
- We can check individual sources, and specific events that we are interested in. 


A typical thing to consider is the creating of images: 
```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1
| fields ParentImage, Image
| table ParentImage, Image
```
- Checking file creating events, and looking at the created process and the parent process. 
- The two SPL queries does generate the same content. 

Improved:
```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1
| stats count by ParentImage, Image
```
- Will also show the COUNT of events, example:
![[Pasted image 20250307115446.png]]


```
index="main" ImageLoaded="*clr.dll*"
| stats count by Image
```
- Looking for images that loaded a specific .dll. Some C# injections load clr.dll.   


```
index="main" CallTrace="*UNKNOWN*" 
| stats count by SourceImage, TargetImage, EventCode
```
-  calltrace=UNKNOWN can be used to find shellcode that has been executed in memory. False positives can occur but are limited to JIT processes. 


```
index="main" SourceImage="*rundll32.exe" CallTrace="*UNKNOWN*"
```
- By leveraging the queries above, we found some suspicious files that loaded **clr.dll**. Then we check if it has the UNKNOWN callstack. In this case rundll32.exe did both of these, which gives a strong indication of  injection. 

```
index="main" (SourceIp=10.0.0.186 OR SourceIp=10.0.0.91)
```
- If we know malicious servers/IPs we would like to know if there has been any connection from that IP to one of our machines. This query checks for this, from the result we could get an indication as to what host or port we would like to "isolate/turn off".



### Common SPLs based on known TTPs:

```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 Image=*\\ipconfig.exe OR Image=*\\net.exe OR Image=*\\whoami.exe OR Image=*\\netstat.exe OR Image=*\\nbtstat.exe OR Image=*\\hostname.exe OR Image=*\\tasklist.exe | stats count by Image,CommandLine | sort - count
```
- **Detection Of Reconnaissance Activities Leveraging Native Windows Binaries**

```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=22  QueryName="*github*" | stats count by Image, QueryName
```
- **Detection Of Requesting Malicious Payloads/Tools Hosted On Reputable/Whitelisted Domains (Such As githubusercontent.com)**


```
index="main" EventCode=3 NOT (DestinationPort=80 OR DestinationPort=443 OR DestinationPort=22 OR DestinationPort=21) | stats count by SourceIp, DestinationIp, DestinationPort | sort - count
```
- **Detection Of Using Non-standard Ports For Communications/Transfers**


```
index="main" EventCode=11 (TargetFilename="*.exe" OR TargetFilename="*.dll") TargetFilename!="*\\windows\\*" | stats count by User, TargetFilename | sort + count
```
- **Detection Of Executables or DLLs Being Created Outside The Windows Directory**

```
index="main" EventCode=1 | regex Image="C:\\\\Users\\\\.*\\\\Downloads\\\\.*" |  stats count by Image
```
- **Detection Of Execution From Atypical Or Suspicious Locations**


```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=11 Image="*powershell.exe*" |  stats count by Image, TargetFilename |  sort + count
```
- **Detection Of Utilizing PowerShell**

```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=11 Image="*msedge.exe" TargetFilename=*"Zone.Identifier" |  stats count by TargetFilename |  sort + count
```
- **MS Edge For Downloading Payloads/Tools**


```
index="main" EventCode=11 (TargetFilename="*.zip" OR TargetFilename="*.rar" OR TargetFilename="*.7z") | stats count by ComputerName, User, TargetFilename | sort - count
```
**Detection Of Utilizing Archive Files For Transferring Tools Or Data Exfiltration**

```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=18 Image=System | stats count by PipeName
```
- **Leveraging Sysmon Event ID 18 - We are looking for pipe communication, similar to socket communication.** 
- Specifically we are looking for named pipes that were created by the system process 


```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 (CommandLine="*psexe*.exe" NOT (CommandLine="*PSEXESVC.exe" OR CommandLine="*PsExec64.exe")) OR (ParentCommandLine="*psexe*.exe" NOT (ParentCommandLine="*PSEXESVC.exe" OR ParentCommandLine="*PsExec64.exe")) OR (ParentImage="*psexe*.exe" NOT (ParentImage="*PSEXESVC.exe" OR ParentImage="*PsExec64.exe")) OR (Image="*psexe*.exe" NOT (Image="*PSEXESVC.exe" OR Image="*PsExec64.exe")) |  table Image, CommandLine, ParentImage, ParentCommandLine
```
- **Detection Of Misspelling Legitimate Binaries**


### Detect PSexec usage:
Common event ids:
- ID 11: file created 
- ID 13: Registry value set
- ID 17: Creation of named pipe
- ID 18: Connection to named pipe 

One can start simple, and do a general search for psexec.exe:
```
index="main" psexec64.exe
```
- Look at the commandline, image and EventCode variable.
- Example, if we see psexec in a downloads folder it is sus. 

ALTERNATIVE: we can use a more robust query:
```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 (CommandLine="*psexe*.exe" NOT (CommandLine="*PSEXESVC.exe" OR CommandLine="*PsExec64.exe")) OR (ParentCommandLine="*psexe*.exe" NOT (ParentCommandLine="*PSEXESVC.exe" OR ParentCommandLine="*PsExec64.exe")) OR (ParentImage="*psexe*.exe" NOT (ParentImage="*PSEXESVC.exe" OR ParentImage="*PsExec64.exe")) OR (Image="*psexe*.exe" NOT (Image="*PSEXESVC.exe" OR Image="*PsExec64.exe")) |  table Image, CommandLine, ParentImage, ParentCommandLine
```
- It covers cases with misspelling of psexec. 



```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=11 Image=System | stats count by TargetFilename
```
- Looking for files where the **System process** was responsible for creating a file 

```
index="main" EventCode=8 rundll32.exe
| table SourceImage, TargetImage
```
- Look for a process that has created a remote thread in rundll32.exe

```
<some other query before> earliest="MM/DD/YYYY:HH:MM:SS"
```
- If we want to specify dates in splunk. Can also use latest instead of earliest. 
Case:

From: `index="main" PSEXESVC.exe EventCode=13` We found that the  PSEXESVC.exe have changed the registry value of `HKLM\System\CurrentControlSet\Services\PSEXESVC\ImagePath`


From: `index="main" sourcetype="WinEventLog:Sysmon" EventCode=11 Image=System | stats count by TargetFilename`, we find:
File created from system process:
`C:\Windows\PSEXECSVCS.exe`

From: `index="main" sourcetype="WinEventLog:Sysmon" EventCode=18 Image=System`, we find that the System process created a named pipe called: `\PSEXESVC`

New case:
- We know rundll32.exe was used for malicious use, and we know randomfile.exe created a remote thread in rundll32.exe.  
- We know explorer.exe is the parent image of randomfile.exe 
- explorer.exe has parent image powershell.exe, and was created with the command line: `["C:\Windows\explorer.exe" C:\temp\20221108T1252_DCSYNC\]`, that is, the folder path was opened in explorer browser. 
- From here, a file was opened from the folder with notepad.exe: `["C:\Windows\system32\NOTEPAD.EXE" C:\temp\20221108T1252_DCSYNC\CUSTOMER\20221108T1252_DCSync_NTLM_CUSTOMER_Importfile_FINAL.txt]`
- We know 11/8/22-12:12:46, a powershell process was created by `PSEXECSCVCS.exe`  with the command for ps to download and execute a script for a remote server. The server is 10.0.0.229:8080, and the downloaded file was Invoke-DCSync.ps1
- 11/6/22-11:17.49 a powershell process was created to download PsExec64.exe from the same server. 

- First Connection to 10.0.0.229: **2022-11-06 09:45:24 AM** , From source 10.0.0.253
- Downloading more files from 10.0.0.229: **2022-11-6 11:17:49 AM -> 12:22-01 PM**, rundll32.exe is the parent of the PS process that downloads all these files. 
- .dll's are loaded, **2022-11-6 09:46 - 12:30**, randomfile.exe loads some dlls 
- Creating remote threads: **2022-11-6 10:14:44 AM**  , randomfile.exe creates the theads in rundll32.exe 
- Parent process of randomfile.exe: **2022-11-6 9:46:53 AM** , explorer.exe opens randomfile.exe. The explorer.exe doing this has pid:4668


```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=3 | bin _time span=1h | stats count as NetworkConnections by _time, Image | streamstats time_window=24h avg(NetworkConnections) as avg stdev(NetworkConnections) as stdev by Image | eval isOutlier=if(NetworkConnections > (avg + (0.5*stdev)), 1, 0) | search isOutlier=1
```
- We are looking at the normal network connections, and the events that fall outside the normal. 