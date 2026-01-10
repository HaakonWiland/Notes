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