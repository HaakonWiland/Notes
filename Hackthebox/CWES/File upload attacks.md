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