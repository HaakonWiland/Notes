#CWES #fuzz

**Fuzzing:** Give unexpected input to an application and see how it responds, goal is to find hints at vulnerabilities.  

**Relevant tools:**
- FFUF: Enumerating directories, file, and parameters on a webpage. Can also brute force.

Example off FFUF for a file within a directory, here we look for different file extensions. 
``` shell
ffuf -w common.txt -u http://94.237.49.88:58798/webfuzzing_hidden_path/flag/FUZZ -e .txt,.html,.bak,.php,.js
```

- Gobuster: Enumerating directories, files, virtual hosts, DNS subdomains, wordpress stuff. 
- FeroxBuster: Directory discovery, specifically hidden directories such as recursive or unlinked ones. 
- wenum: Enumerating directories, file, and parameters on a webpage.

**What we look after when we fuzz:**
- Sensitive data
- Outdated content
- Development resources
- Hidden functionalities 

Even small hidden stuff could be useful, and help a pentester see the bigger picture.


**Common fuzz wordlists:**
![[Pasted image 20260110150432.png]]

#### Directive fuzzing:
**Recursive directory fuzzing:**
Example:
```shell
ffuf -w DirBuster-2007_directory-list-2.3-medium.txt -ic -u http://94.237.49.88:45052/recursive_fuzz/level1/level2/FUZZ -e .html -recursion
```
- A complex web application can have many layers of directories and files, enumerating them manually can be time consuming
- FUFF has a `-recursive` option that lets use fuzz "into" a directory, allowing us to automate more of the fuzzing process. 
- NB: Can be useful to use the `-rate` and `-recursion-depth` option to prevent causing problems on the application. 

#### Parameter fuzzing:
- Can automate interactions with the web application 
- By fuzzing parameters we can quickly get a feel for how the application operates.

Can use **wfuzz** or **wenum** as tools to fuzz parameters, but both seem pretty buggy if you ask me. 
Example fuzzing get parameter:
```shell
wfuzz -w common.txt --hc 404 "http://94.237.49.88:30318/get.php?x=FUZZ"
```
- But the idea is that they can fuzz a parameter, and see of a specific value returns a interesting result 
- TBH: I would probably used Burp intruder or OWASP ZAP to fuzz such parameters. 

Example fuzzing post parameter:
```shell
ffuf -u http://94.237.49.88:30318/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w common.txt -mc 200 -v
```
- Change the Content-Type and wordlist on need. 

Can also be useful to verify findings in parameters by sending curl request manually:
- Example:
```shell
curl http://94.237.120.74:43318/post.php -X POST -d "y=SUNWmc"
```


#### Virtual hosting and subdomain fuzzing 
- See [[Information Gathering]] 
- Can use gobuster to fuzz and discover subdomains and virtual hosts 

Examples of gobuster subdomain fuzzing:
```shell
gobuster dns --domain inlanefreight.com -w subdomains-top1million-5000.txt
```

Examples of gobuster vhost fuzzing:
```shell
gobuster vhost -w common.txt -u http://inlanefreight.htb:47521 --append-domain
```

Gobuster tips:
- Filtering output
![[Pasted image 20260111142606.png]]

FFUF tips:
- filter output
![[Pasted image 20260111142926.png]]


#### Fuzzing APIs:
**Discovery:**
- Check documentation! (But there can still be hidden apis not documented)
- Network traffic analysis; check what apis are used by the application when we interact with it.  
- Parameter name fuzzing 

**Different types of APIs:**
- REST
- SOAP (xml bullshit)
- GraphQL: Usually 1 endpoint, has its own query language. 

**Ways to fuzz apis:**
- **Parameter fuzzing:** Try different API endpoints, headers, request bodies 
- **Data format fuzzing:** Try different formatting and encoding of the data sent to the apis, can reveal vuls related to parsing, buffer overflows, improper handling of special characters. 
- **Sequence fuzing:** Looking at api response from sequences of requests. 

**Tools for API fuzzing:**
- https://github.com/PandaSt0rm/webfuzz_api.git

```shell
python3 api_fuzzer.py http://94.237.122.188:38651 --wordlist ~/wordlists/seclists/Discovery/Web-Content/common.txt
```
- Can be used to find hidden apis.
- Can be used to fuzz parameters in the api?


#### Skill assessment:
- Have /admin dir, but give access denied. Else no interesting leve1-directories. 
- We find this interesting level2-directory: /admin/panel.php, which tells us "Invalid parameter, please ensure accessID is set correctly"
- We fuzz that parameter, and a value "getaccess" returns a site with a hint to fuzz a specific vhost: fuzzing_fun.htb

```shell
  curl http://94.237.55.124:43442 -H "Host: fuzzing_fun.htb"    
Welcome to fuzzing_fun.htb!
Your next starting point is in the godeep folder - but it might be on this vhost, it might not, who knows... 
  ```
- Fuzzing for vhosts, we find: hidden.fuzzing_fun.htb:43442/godeep/
- Then we do a recursion-ffuf on hidden.fuzzing_fun.htb:43442/godeep/ , looking for directories -> which finds the site with the flag. 