#CWES #hackthebox #web #proxy #burp


- Web proxies: we use them to capture requests and traffic passing between applications and back-end servers, and manipulate these types of requests. 
- Can setup proxy between applications, scripts, cli tools and browser, then we can view them in burp. 
- Bott burp and zap has extensions which we can add onto the tool. We can find useful stuff such as wordlists etc. 

**Brup pro vs free:**
- Intruder speed throttled to 1 request per sec, while pro is unlimited. 


TOOLS:
- Burp and ZAP
- proxychains (to forward cli tools via the proxy)
- msfconsole (set PROXIES)
 
LESSON:
- Some input fields in web applications validate their inputfield, ex: must input integer. And we are not allowed to enter  `;whoami`.
- But if we intercept the request with a web proxy, we can change the post parameter in the http request. This is allowed in some cases. 
- **CAUSE: Only frontend validation, no serverside validation.** 



#### Configurating burp 
- If we go to proxy > proxy settings > match and replace, then we can edit the behavior of the http requests and responses that we are intercepting.  

#### Burp Intruder (Zap Fuzzer)
- IDEA: Have a set of payloads, and spam them at a / (some) components on the site.  
- Zap fuzzer is much faster then burp intruder 

- payloads: https://portswigger.net/burp/documentation/desktop/tools/intruder/configure-attack/payload-types 


LESSON:
- When fuzzing stuff and preforming processing(encoding etc), it can be useful to try different ways of the same thing 
- Ex: encode with cli tool > list.txt, encode with python script write to file, or burp payload processor. 
- There can be hard to spot differences in the way they handles things, which can make a difference. 


### ZAP
- Can fuzz the same way as burp intruder, but is much faster 
- Can also do processing of the payloads, ex. hashing username-cookies. 

#### ZAP spider:
- Passive and ajax scan to enumerate all pages on the site, will only look at the request/response to look for vulnerabilities.
- Then there is active scan, which tries to preform various types of attacks against the pages identified earlier. 

