#cwes #methodology 
### 1. Enumeration:

**DNS, subdomans and vhosts:** [[Information Gathering]] [[Web Fuzzing]]
**Directory and file fuzzing:** [[Web Fuzzing]]
- For each found directory, we can do a recursive fuzzing search.
**Parameter fuzzing:** [[Web Fuzzing]]
- Both GET and POST requests, use FFUF. 


If we find a service which needs authentication, consider running a login bruteforce attack, its quick and can give us some initial access. 

**Login Brute forcing:** [[Login bruteforce]]
- If we know the password policy (feks. by trying to create a new account, and see what the password enforces), we can modify the base-wordlist we use. ex:
```
grep -E '^.{8,}$' darkweb2017_top-10000.txt > darkweb2017-minlength.txt
```
- Remember we can brute force logins over many different protocols with Hydra, not only web apps.
- If we know the actual name of a account we want to brute force, consider using `username-anarchy` or  `cupp` to generate possible usernames for that user. 



### 2. Exploitation:

#### XSS:
**Discovery**: [[Cross-site-scripting (XSS)]]
- Quick manual testing: https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md 
- Fuzzing: xsstrike
- NOTE: XSS can also exit in HTTP headers (User-agents, cookies,etc) - consider fuzzing with burp pro

#### SQLi: [[SQLi]] [[SQLmap]]
- Course heavily lead to union-based SQLi
- 1. Find inputs that somehow interact with a database 
- 2. Test it for the common input escapes: `' " # ; ) `. Try to get two different states (true and false), and checking that the responses differ. (this is the POC)
- 2.5. Run SQL-MAP to fuzz the different sus-inputs. 
- 3. If we can do union-based sqli, we have to figure out the number of columns. Then check which if them gets returned visually to us. 
- 4. Exploit: Check write privileges and leak the data we are interested in.  Can we get code exec? 

**SQLMAP:**
- 1. find inputs that somehow interact with a database
- 2. If its a POST request, copy the request into a file (`sqlmap -r req.txt`) and if its a GET request spesify the parameter in the url argument. 

Ways to modify your sql-command to increase success:
- Add `--random-agent`
- Adding `--prefix` or `--suffix`
- Adding `--level=<1-5>` and `--risk=<1-3>`
- CSRF bypass: `--csrf-token` 
- If the requests needs randomization in its parameters we can use: `--randomize=<parametername>`
- Add `--chunked` to split the post request body into smaller chucks, can help prevent detection.
- Can also do evaluations:  ex. hash of id value
```
sqlmap -u "http://www.example.com/?id=1&h=c4ca4238a0b923820dcc509a6f75849b" --eval="import hashlib; h=hashlib.md5(id).hexdigest()" --batch -v 5 | grep URI
```
- Can proxy the request via another IP such a tor, or if our ip is blacklisted.
- If all else fails, look up tamper scripts 

For debugging:
- Add `-t <file.txt>`: saves the actual requests being send from sqlmap
- Increase verbose
- Proxy to burp 

Exploiting:
- Find database version and names, schemas, tables, columns etc.
- Searching for specific data: `--search -T user` = search for table with name like user 


#### Command injection: [[Command injection]]
1. Find a place where you think a underlying command is running, ex. network stuff, creating files, and other stuff we could do from a terminal.
2. Test the normal use-cases of this function, check for outputs and try to understand what command is running.
3. Check which characters and commands get filtered out, and find ways to bypass these restrictions.
4. Craft a payload to do malicious things: web shells, reverse shells, reading credential files, etc.

Tools like bashfuscator can be really useful to craft payloads that bypass the filter. 


#### File upload attacks: [[File upload attacks]]
1. If we find a file upload functionality on a site, we first need to test how it gets sanitized and validated. Some times the validations only happens in the frontend.

Validation methods:
- File type enforced to be some specific types: ex. jpeg and png, and ban php.
- Content-Type or File-Content header restricted to specific types: ex. image/jpeg
- MIME type validation (magic byte): 


Testing validation methods:
- Fuzz with intruder in burp, using a file extension wordlist (https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt). Can also try character injection to bypass the extension filter.
- Fuzz the Content-Type header with burp (https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-all-content-types.txt) HOT TIPS: Look at the error message, and we might be able to remove a lot of these guesses. 
ex:
```shell
cat web-all-content-types.txt | grep 'image/' > image-content-types.txt
```
- Test different MIME types: https://en.wikipedia.org/wiki/List_of_file_signatures 

NOTE: In SVG images, we can embed XML, which could lead to XXE attacks. 

**Be very systematical when we test the different validation methods.**

2. Upload a malicious file: Web or reverse shell usually. 

#### Server side attacks: [[Server-side attacks]]
4 attacks in this module:
- SSRF
- SSTI
- Server-side includes (SSI) injection
- eXtensible Stylesheet Language Transformations (XSLT) Server-side injection

**SSRF:**
1.identification: Look for arguments and parameters that specify URLs, we can maybe change these. Try to make them fetch our ip or 127.0.0.1.

2.Once confirmed, try different url-schemes, different IPs, enumerate ports and directories. 


**SSTI:**
1.For input fields, inject the test string: 
```
${{<%[%'"}}%\.
```
- Compare the response with the response normal expected input. If the test string gives server error or similar, we might have a vulnerability.

2.Identity the template engine, with the "engine-map"

3.Once we know the engine, look up different payloads. Here we can possible get LFI or RCE. https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md 

(Can also try SSTImap)

**SSI injection:**

1.Look for pages with file extensions `.shtml`, `shtm`, `.stm`
2.try some of these:

`<!--#exec cmd="id" -->`
`<!--#printenv -->`
`<!--#exec cmd="whoami" -->`

**XSLT injection:**
1.Look for cases where our input gets saved in a XML document and displayed on the page.
2.We can try to inject characters like `<` to provoke an error in the xml document and hence a error on the page. 

Then we we can try to exploit:
```
<xsl:value-of select="unparsed-text('/etc/passwd', 'utf-8')" />

<xsl:value-of select="php:function('file_get_contents','/etc/passwd')" />

(if it runs php)
<xsl:value-of select="php:function('system','id')" />
```

#### Broken Authentication:
- Can use verbose error messages to enumerate valid usernames. Use a wordlist with common usernames.
- If we find usernames which are valid, consider doing a password brute force on these 
- It is also possible to bruteforce reset-password-tokens, that is; the site you get sent when you want to reset you password. You need to know the structure of the "reset-url".
- Same with OTP - if it is setup poorly. 
- If we find a "common service", consider looking up the default creds, google or https://cirt.net/passwords/. 
- If we know information about the user we want to hack, we could try reseting their password and answering a security question. Some sec questions have a finite amount of answers. 
- Some services protect their resources very poorly, and gives access to their admin page via IDOR or authentication attempts which are uncomplete (only 1 factor complete).
- Session tokens are also a attractive place to attack, try to understand the structure of them. If it is a simple short pattern, a hash some known data, base64 or hex encoding. 


#### Web attacks:
