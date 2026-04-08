#CWES 

#### Cause of file upload attacks:
- Weak file validation and verification - bad logic in code or outdated libraries 

#### Typical attack:
- We can upload a web shell and get RCE on the server 

NOTE: A web shell has to be written in the same programming language that runs the web server, as it runs platform specific functions and commands. -> SO **1.STEP** IS TO IDENTIFY WHAT LANGUAGE RUNS THE WEB APPLICATION.

This should be simple: use **Wappalyzer** or:
"One easy method to determine what language runs the web application is to visit the `/index.ext` page, where we would swap out `ext` with various common web extensions, like `php`, `asp`, `aspx`, among others, to see whether any of them exist."

**2.STEP:** See what files we can upload to the server and if there is any restrictions. 

**3.STEP:** Send a malicious file to the server -> Typically web shells, check out SecLists which have loads of different web shells. 

It can also be reverse shells, can be found on SecLusts or https://github.com/pentestmonkey/php-reverse-shell or msfvenom.

We always prefere a reverse shell over a web shell, but sometimes we may not be able to get a reverse shell: firewalls on the back-end network that prevents outgoing connection or web server disables necessary functions to initiate a connection. 


#### File validation bypass:

**Only validating on the frontend:** If the site only validates on the client side, then it can be bypassed via sending requests directly to the api endpoint or disabling the client validation (since it is code which run i our browser we can just change it.)

Example of a file upload api:
```
POST /upload.php HTTP/1.1
Host: 154.57.164.74:32023
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
X-Requested-With: XMLHttpRequest
Content-Type: multipart/form-data; boundary=----geckoformboundaryfaa0f7b264525ad835555a5b95d739
Content-Length: 244
Origin: http://154.57.164.74:32023
Connection: keep-alive
Referer: http://154.57.164.74:32023/
Priority: u=0

------geckoformboundaryfaa0f7b264525ad835555a5b95d739
Content-Disposition: form-data; name="uploadFile"; filename="shell.php"
Content-Type: image/png

<?php system($_GET["cmd"]); ?>
------geckoformboundaryfaa0f7b264525ad835555a5b95d739--
```


**Simple blacklisting of extensions:** If the only form of validation is blacklisting some extensions we can try to write our payload with another extensions and check it it bypasses AND is allowed to execute. 

Example look at this list of alternative extensions that might execute: 
- https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt
- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst (PHP alternatives)

We can fuzz what extensions are valid via the intruder in burp, and afterwards check if any of them also is allowed to execute code. NOTE: IMPORTENT TO DISABLE URL ENCODING


**Whitelist validation:** Generally harder to bypass then blacklisting, but if done poorly, it can still get exploited. Some whitelisting is done via a regex, and if there are errors in the regex, it can be bypassed. 

We might be able to do a **"double extension" or "reverse double extension"**, ex: shell.php.jpg or shell.jpg.php

We also might be able do some character injection into the extension 
- `%20`
- `%0a`
- `%00`
- `%0d0a`
- `/`
- `.\`
- `.`
- `…`
- `:`

Which can be mixed with the extension to bypass. Consider building a wordlist with such characters and known extensions. 
