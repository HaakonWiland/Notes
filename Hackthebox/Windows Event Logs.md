#SOC #hackthebox #eventcode #sysmon #eventviewer 

**Detection mindset**: "One of the key aspects of threat detection is having a good understanding of what is "normal" in our environment. Anomalies that might indicate a threat in one environment could be normal behavior in another. It's crucial to tune our monitoring and alerting systems to our environment to minimize false positives and make real threats easier to spot."


**Tips and Tricks:**
- In event viewer we can look for execution of unmanaged ps code, by filtering for event id 1 (Process creation). 

Where to find stuff:
**Event logs:** Event Viewer or Windows Event Log API 
- Event Viewer can also open saved logs in the format of .entx files. 

**Structure of Windows Event logs:**
```
1. `Log Name`: The name of the event log (e.g., Application, System, Security, etc.).
2. `Source`: The software that logged the event.
3. `Event ID`: A unique identifier for the event.
4. `Task Category`: This often contains a value or name that can help us understand the purpose or use of the event.
5. `Level`: The severity of the event (Information, Warning, Error, Critical, and Verbose).
6. `Keywords`: Keywords are flags that allow us to categorize events in ways beyond the other classification options. These are generally broad categories, such as "Audit Success" or "Audit Failure" in the Security log.
7. `User`: The user account that was logged on when the event occurred.
8. `OpCode`: This field can identify the specific operation that the event reports.
9. `Logged`: The date and time when the event was logged.
10. `Computer`: The name of the computer where the event occurred.
11. `XML Data`: All the above information is also included in an XML format along with additional event data.
```


Example: 
![[Pasted image 20250220141702.png]]

NOTE: You can open .evtx files inside event viewer. 
#### Windows logs types:
**Application:** 
- Logs for software installed 
- Information: Start, stop, general info about event 
- Errors: Issues 
**Security:**
- Security relevant info such as logon and logoff 
**Setup:**
**System:**
- Logs related to the operating system 
**Forwarded Events:**


##### Common System and Security Event logs 

1. **Windows System Logs**
    
    - [Event ID 1074](https://serverfault.com/questions/885601/windows-event-codes-for-startup-shutdown-lock-unlock) `(System Shutdown/Restart)`: This event log indicates when and why the system was shut down or restarted. By monitoring these events, you can determine if there are unexpected shutdowns or restarts, potentially revealing malicious activity such as malware infection or unauthorized user access.
    - [Event ID 6005](https://superuser.com/questions/1137371/how-to-find-out-if-windows-was-running-at-a-given-time) `(The Event log service was started)`: This event log marks the time when the Event Log Service was started. This is an important record, as it can signify a system boot-up, providing a starting point for investigating system performance or potential security incidents around that period. It can also be used to detect unauthorized system reboots.
    - [Event ID 6006](https://learn.microsoft.com/en-us/answers/questions/235563/server-issue) `(The Event log service was stopped)`: This event log signifies the moment when the Event Log Service was stopped. It is typically seen when the system is shutting down. Abnormal or unexpected occurrences of this event could point to intentional service disruption for covering illicit activities.
    - [Event ID 6013](https://serverfault.com/questions/885601/windows-event-codes-for-startup-shutdown-lock-unlock) `(Windows uptime)`: This event occurs once a day and shows the uptime of the system in seconds. A shorter than expected uptime could mean the system has been rebooted, which could signify a potential intrusion or unauthorized activities on the system.
    - [Event ID 7040](https://www.slideshare.net/Hackerhurricane/finding-attacks-with-these-6-events) `(Service status change)`: This event indicates a change in service startup type, which could be from manual to automatic or vice versa. If a crucial service's startup type is changed, it could be a sign of system tampering.
2. **Windows Security Logs**
    
    - [Event ID 1102](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=1102) `(The audit log was cleared)`: Clearing the audit log is often a sign of an attempt to remove evidence of an intrusion or malicious activity.
    - [Event ID 1116](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/troubleshoot-microsoft-defender-antivirus?view=o365-worldwide) `(Antivirus malware detection)`: This event is particularly important because it logs when Defender detects a malware. A surge in these events could indicate a targeted attack or widespread malware infection.
    - [Event ID 1118](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/troubleshoot-microsoft-defender-antivirus?view=o365-worldwide) `(Antivirus remediation activity has started)`: This event signifies that Defender has begun the process of removing or quarantining detected malware. It's important to monitor these events to ensure that remediation activities are successful.
    - [Event ID 1119](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/troubleshoot-microsoft-defender-antivirus?view=o365-worldwide) `(Antivirus remediation activity has succeeded)`: This event signifies that the remediation process for detected malware has been successful. Regular monitoring of these events will help ensure that identified threats are effectively neutralized.
    - [Event ID 1120](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/troubleshoot-microsoft-defender-antivirus?view=o365-worldwide) `(Antivirus remediation activity has failed)`: This event is the counterpart to 1119 and indicates that the remediation process has failed. These events should be closely monitored and addressed immediately to ensure threats are effectively neutralized.
    - [Event ID 4624](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4624) `(Successful Logon)`: This event records successful logon events. This information is vital for establishing normal user behavior. Abnormal behavior, such as logon attempts at odd hours or from different locations, could signify a potential security threat.
    - [Event ID 4625](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4625) `(Failed Logon)`: This event logs failed logon attempts. Multiple failed logon attempts could signify a brute-force attack in progress.
    - [Event ID 4648](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4648) `(A logon was attempted using explicit credentials)`: This event is triggered when a user logs on with explicit credentials to run a program. Anomalies in these logon events could indicate lateral movement within a network, which is a common technique used by attackers.
    - [Event ID 4656](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4656) `(A handle to an object was requested)`: This event is triggered when a handle to an object (like a file, registry key, or process) is requested. This can be a useful event for detecting attempts to access sensitive resources.
    - [Event ID 4672](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4672) `(Special Privileges Assigned to a New Logon)`: This event is logged whenever an account logs on with super user privileges. Tracking these events helps to ensure that super user privileges are not being abused or used maliciously.
    - [Event ID 4698](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4698) `(A scheduled task was created)`: This event is triggered when a scheduled task is created. Monitoring this event can help you detect persistence mechanisms, as attackers often use scheduled tasks to maintain access and run malicious code.
    - [Event ID 4700](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4700) & [Event ID 4701](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4701) `(A scheduled task was enabled/disabled)`: This records the enabling or disabling of a scheduled task. Scheduled tasks are often manipulated by attackers for persistence or to run malicious code, thus these logs can provide valuable insight into suspicious activities.
    - [Event ID 4702](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4702) `(A scheduled task was updated)`: Similar to 4698, this event is triggered when a scheduled task is updated. Monitoring these updates can help detect changes that may signify malicious intent.
    - [Event ID 4719](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4719) `(System audit policy was changed)`: This event records changes to the audit policy on a computer. It could be a sign that someone is trying to cover their tracks by turning off auditing or changing what events get audited.
    - [Event ID 4738](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4738) `(A user account was changed)`: This event records any changes made to user accounts, including changes to privileges, group memberships, and account settings. Unexpected account changes can be a sign of account takeover or insider threats.
    - [Event ID 4771](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4771) `(Kerberos pre-authentication failed)`: This event is similar to 4625 (failed logon) but specifically for Kerberos authentication. An unusual amount of these logs could indicate an attacker attempting to brute force your Kerberos service.
    - [Event ID 4776](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4776) `(The domain controller attempted to validate the credentials for an account)`: This event helps track both successful and failed attempts at credential validation by the domain controller. Multiple failures could suggest a brute-force attack.
    - [Event ID 5001](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/troubleshoot-microsoft-defender-antivirus?view=o365-worldwide) `(Antivirus real-time protection configuration has changed)`: This event indicates that the real-time protection settings of Defender have been modified. Unauthorized changes could indicate an attempt to disable or undermine the functionality of Defender.
    - [Event ID 5140](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=5140) `(A network share object was accessed)`: This event is logged whenever a network share is accessed. This can be critical in identifying unauthorized access to network shares.
    - [Event ID 5142](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=5142) `(A network share object was added)`: This event signifies the creation of a new network share. Unauthorized network shares could be used to exfiltrate data or spread malware across a network.
    - [Event ID 5145](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=5145) `(A network share object was checked to see whether client can be granted desired access)`: This event indicates that someone attempted to access a network share. Frequent checks of this sort might indicate a user or a malware trying to map out the network shares for future exploits.
    - [Event ID 5157](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=5157) `(The Windows Filtering Platform has blocked a connection)`: This is logged when the Windows Filtering Platform blocks a connection attempt. This can be helpful for identifying malicious traffic on your network.
    - [Event ID 7045](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=7045) `(A service was installed in the system)`: A sudden appearance of unknown services might suggest malware installation, as many types of malware install themselves as services.

Windows Doc on sysmon event codes: https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon

#### Other event ids:

**Application and Services:**
- **Event ID 7:** This event is generated whenever a process **loads a module** (DLL, EXE, or other executable file) into its address space. Essentially, it tracks which shared libraries (DLLs) or executable components are loaded into memory by a process.
- **Event code 10 (sysmon):** A process attempts to **access another process** memory. Useful for monitoring unauthorized access to critical processes like LSASS 

#### XML Queries in event viewer:

Example:
```
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">
      *[System[(EventID=4907)]]
      and
      *[EventData[Data[@Name='ObjectName'] = 'C:\Windows\Microsoft.NET\Framework64\v4.0.30319\WPF\wpfgfx_v0400.dll']]
    </Select>
  </Query>
</QueryList>
```

```
<QueryList>
  <Query Id="0" Path="file://C:\Logs\PowershellExec\PowershellExec.evtx">
    <Select Path="file://C:\Logs\PowershellExec\PowershellExec.evtx">
*[System[EventID=1]]
and 
*[EventData[Data[@Name='ProcessGuid'] = '67e39d39-ecd9-6269-0d00-000000000300']]
</Select>
  </Query>
</QueryList>
```



- Checks for security logs, where the event id is 4907(changed audit), and the object is .... wpfgfx_v0400.dll

**Checking for event data that is not signed:**
```
<QueryList>
  <Query Id="0" Path="file://C:\Logs\DLLHijack\DLLHijack.evtx">
    <Select Path="file://C:\Logs\DLLHijack\DLLHijack.evtx">
      *[System[(EventID=7)]] 
      and 
      *[EventData[Data[@Name='SignatureStatus'] != 'Signed']]
    </Select>
  </Query>
</QueryList>
```

- Can be useful for detecting dll hijacking -> Might then also want to filter some common path and stuff. 
### Sysmon:
Tool that monitors and log system activity to the Windows event log. It provides detailed information about process creation, network connections, changes to file creation time, and more.

Basic xml config: https://github.com/SwiftOnSecurity/sysmon-config

Configure sysmon:
```
sysmon.exe -c filename.xml
```


#### Common IOC:
- clr.dll , clrjit.dll load in a process that do not require them. They are used in C# and ran as part of the runtime to execute the bytecode. 
- **Abnormal parent-child relationships** among processes can be indicative of malicious activities. In standard Windows environments, certain processes never call or spawn others. For example, it is highly unlikely to see "calc.exe" spawning "cmd.exe" in a normal Windows environment. MORE: https://x.com/SBousseaden/status/1195373669930983424
- 

**Credential dumping on windows:**
- Mikikatz: Common tool to dump windows credentials
- Main attack LSASS: https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service 
- Example where mimikatz.exe is called from another .exe file. 
![[Pasted image 20250221143804.png]]


**Example of RDP with mounting folders:**
```

xfreerdp /u:Administrator /p:password /v:10.129.36.184 /dynamic-resolution /drive:documents,/home/htb-ac-895510/Downloads


ex: 
xfreerdp /u:Administrator /p:'HTB_@cad3my_lab_W1n10_r00t!@0' /v:10.129.139.67 /dynamic-resolution
```




### Event Tracer for Windows(ETW):
"tracing mechanism for events raised by both user-mode applications and kernel-mode device drivers." ->A more verbose Windows Event Logs


`Providers`: Providers play a pivotal role in generating events and writing them to the designated ETW sessions. Applications have the ability to register ETW providers, enabling them to generate and transmit numerous events.

#### Useful providers:
- `Microsoft-Windows-Kernel-Process`: This ETW provider is instrumental in monitoring process-related activity within the Windows kernel. It can aid in detecting unusual process behaviors such as process injection, process hollowing, and other tactics commonly used by malware and advanced persistent threats (APTs).
- `Microsoft-Windows-Kernel-File`: As the name suggests, this provider focuses on file-related operations. It can be employed for detection scenarios involving unauthorized file access, changes to critical system files, or suspicious file operations indicative of exfiltration or ransomware activity.
- `Microsoft-Windows-Kernel-Network`: This ETW provider offers visibility into network-related activity at the kernel level. It's especially useful in detecting network-based attacks such as data exfiltration, unauthorized network connections, and potential signs of command and control (C2) communication.
- `Microsoft-Windows-SMBClient/SMBServer`: These providers monitor Server Message Block (SMB) client and server activity, providing insights into file sharing and network communication. They can be used to detect unusual SMB traffic patterns, potentially indicating lateral movement or data exfiltration.
- `Microsoft-Windows-DotNETRuntime`: This provider focuses on .NET runtime events, making it ideal for identifying anomalies in .NET application execution, potential exploitation of .NET vulnerabilities, or malicious .NET assembly loading.
- `OpenSSH`: Monitoring the OpenSSH ETW provider can provide important insights into Secure Shell (SSH) connection attempts, successful and failed authentications, and potential brute force attacks.
- `Microsoft-Windows-VPN-Client`: This provider enables tracking of Virtual Private Network (VPN) client events. It can be useful for identifying unauthorized or suspicious VPN connections.
- `Microsoft-Windows-PowerShell`: This ETW provider tracks PowerShell execution and command activity, making it invaluable for detecting suspicious PowerShell usage, script block logging, and potential misuse or exploitation.
- `Microsoft-Windows-Kernel-Registry`: This provider monitors registry operations, making it useful for detection scenarios related to changes in registry keys, often associated with persistence mechanisms, malware installation, or system configuration changes.
- `Microsoft-Windows-CodeIntegrity`: This provider monitors code and driver integrity checks, which can be key in identifying attempts to load unsigned or malicious drivers or code.
- `Microsoft-Antimalware-Service`: This ETW provider can be employed to detect potential issues with the antimalware service, including disabled services, configuration changes, or potential evasion techniques employed by malware.
- `WinRM`: Monitoring the Windows Remote Management (WinRM) provider can reveal unauthorized or suspicious remote management activity, often indicative of lateral movement or remote command execution.
- `Microsoft-Windows-TerminalServices-LocalSessionManager`: This provider tracks local Terminal Services sessions, making it useful for detecting unauthorized or suspicious remote desktop activity.
- `Microsoft-Windows-Security-Mitigations`: This provider keeps tabs on the effectiveness and operations of security mitigations in place. It's essential for identifying potential bypass attempts of these security controls.
- `Microsoft-Windows-DNS-Client`: This ETW provider gives visibility into DNS client activity, which is crucial for detecting DNS-based attacks, including DNS tunneling or unusual DNS requests that may indicate C2 communication.
- `Microsoft-Antimalware-Protection`: This provider monitors the operations of antimalware protection mechanisms. It can be used to detect any issues with these mechanisms, such as disabled protection features, configuration changes, or signs of evasion techniques employed by malicious actors.



**Tools:** 
- Logman: terminal tool for creating, initiating, halting and investigating tracing sessions. 
```
logman.exe query -ets
```

Look at a specific trace: 
```
logman.exe query "EventLog-System" -ets
```

![[Pasted image 20250222155815.png]]



**More logman commands:**
```
logman.exe query providers

Find spesific provider: 
logman.exe query providers | findstr "Winlogon"

Find a details about a spesific provider:
logman query providers "provider name"

EX: 
logman query providers "Microsoft-Windows-DotNETRuntime"

```



- Process Monitor: GUI alternative 
- EtwExplorer: GUI alternative 
- **SilkETW & SilkService** are flexible C# wrappers for ETW, they are meant to abstract away the complexities of ETW and give people a simple interface to perform research and introspection. While both projects have obvious defensive (and offensive) applications they should primarily be considered as research tools. https://github.com/mandiant/SilkETW

**Spoof a process example:**
```
powershell -ep bypass

PS C:\Tools\psgetsystem> Import-Module .\psgetsys.ps1 

[MyProcess]::CreateProcessFromParent([2368],"C:\Windows\System32\cmd.exe","")
```

Example of SilkETW:
```
SilkETW.exe -t user -pn Microsoft-Windows-DotNETRuntime -uk 0x2038 -ot file -p C:\windows\temp\etw.json


---
-t user: Spesify the tracer mode, in this case user-mode
-pn Microsoft-Windows-DotNETRuntime: Spesify the provider name 
-uk 0x2038: keyword mask, spesify the type of event to capture 
-ot file -p C:\windows\temp\etw.json: Output file of the log 
```


Other references:

**Beginner guide ETW:** https://web.archive.org/web/20230222121234/https://bmcder.com/blog/a-begginers-all-inclusive-guide-to-etw 

https://nasbench.medium.com/a-primer-on-event-tracing-for-windows-etw-997725c082bf

**Threat Hunting with ETW:** https://medium.com/threat-hunters-forge/threat-hunting-with-etw-events-and-helk-part-1-installing-silketw-6eb74815e4a0


#### Get-WinEvent
Effective tool to look through lots of logs.

Lots of different ways to filter and query stuff:
https://academy.hackthebox.com/module/216/section/2322

MS Get-WinEvent Doc: https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-7.5




**Basic command to list all available logs:**
```
Get-WinEvent -ListLog * | Select-Object LogName, RecordCount, IsClassicLog, IsEnabled, LogMode, LogType | Format-Table -AutoSize
```

**Retrieving events from .evtx files:**
```
Get-WinEvent -Path 'C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement\*'| Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```

- Change the path variable.


**Retriv events from .evtx files, and filter for a event id=5142**
```
Get-WinEvent -FilterHashtable @{Path='C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement\*'; Id='5142' } | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```



```
Get-WinEvent -FilterHashtable @{Path='C:\Logs\DLLHijack\*'; Id='7'} | Format-List * | Format-Table -AutoSize
```



Once we have identified something interesting, we can query for more information:
```
Get-WinEvent -FilterHashtable @{Path='C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement\*'; Id=5142} | Format-List *
```

![[Pasted image 20250225162920.png]]
- The list goes on, its a lot of data. 
