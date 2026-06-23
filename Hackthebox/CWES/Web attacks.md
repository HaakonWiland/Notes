#CWES #XXE #IDOR #verbtampering

#### Lessons:
- It matters if scripts are run with sh or bash! You wan to use bash when you can, so add the `#!/usr/bin/env bash` shabang at the top of the script. 

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
- XML external entity injection
- Exploits the way the web app parses the XML data 
- When looking for which element of a XML input to inject our payload, look for elements that gets displayed in the response. 
- Some pages looks like they only respond to JSON data, but they might not have disabeled XML. We can try to change the Content-Type to application/xml and convert the json data to xml with such tool: https://www.convertjson.com/json-to-xml.htm 
- XML has a strict syntax on which characters to use, example space, |, <> {} may break it, so consider finding workaround for these characters. 