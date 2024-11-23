

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
-  **Injection**
-  **Insecure Design**
-  **Security Misconfiguration**
-  **Vulnerable and Outdated Components**
-  **Identification and Authentication Failures**
-  **Software and Data Integrity Failures**
-  **Security Logging & Monitoring Failures**
-  **Server-Side Request Forgery (SSRF)**
