
```

nmap -sC -sV 10.129.80.148

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-25 17:30 EDT
Nmap scan report for 10.129.80.148
Host is up (0.040s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
8080/tcp open  http    Jetty 9.4.39.v20210325
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
|_http-server-header: Jetty(9.4.39.v20210325)
```


Can view the service on 8080 in the browser.  NB: Need to be over http, not https, and spesify the port since it is not on port 80 which the browser assumes. 
```
http://<ip><port>
```

Trying to gobuster it, have to use the: -b 403, flag go ignore the 403 error:
```
gobuster dir -u http://10.129.80.148:8080 -w /usr/share/wordlists/dirb/common.txt -b 403

```

We find a signup site, which display the Jenkins version. 
[Jenkins 2.289.1](https://jenkins.io/)

Can do Groovy scripting? 

