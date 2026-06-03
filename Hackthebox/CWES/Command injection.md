#CWES #injection 

Payloads: https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection 

#### Lessons:
- Enumerate all stuff on the page before you start exploiting - ENUMERATE
- Injections attacks are way harder to find if the command we are exploiting does not return things to the page - ex. advanced search function did not show anything even if we had valid commands. 
- Command injections: Deduce the characters and commands that we blacklisted. Once we find a way to chain commands via ; && || etc -> Try to get decoded base64 commands executed since its OP.  
- Hypothesis: to suspect command injection, we should see some response on the page which correspond to the normal output of some command. no output or costume error messages  may indicate there is no command injection. 


#### Command injection types:
![[Pasted image 20260317155252.png]]


**OS command injection:**
- userinput results in a system command, usually via a web query. 

##### Useful signs for command injection
![[Pasted image 20260317153456.png]]
- The "or" sign can be useful, if we intentially break the first command or cannot make it return without an error. 

NOTE: If our input is sanitized by the application, it is interesting to check it it is only the frontend sanitizing or if the backend aswell. Of only frontend sanitization, we can exploit this by sending request directly to the backend. 

PRO TIP: `If the error message displayed a different page, with information like our IP and our request, this may indicate that it was denied by a WAF`

#### Bypassing filters
- Try 1 character at a time, and see which one causes error / invalid input

**Space bypass**
- Instead of space, try tab (%09) or `${IFS}` if you are on linux 
- Can do bash brace expantion: 
```
la -la = {ls,-la}
```

- More ways to bypass space: https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-without-space 

**Slash bypassing**
- Can use env variables, and slice them to get certain characters 
```
${PATH:0:1} -> /
${LS_COLORS:10:1} -> ;

Windows:
echo %HOMEPATH:~6,-11% -> \

```
- Can also ASCII shift:
```
$(tr '!-}' '"-~'<<<[) -> \

Change [ with the ascii char which is before the one you want.
```

**Command blacklists**
- Some spesiffic commands may be blacklisted
- We are run these commands with some bypasses 
```
Linux: 
w'h'o'a'mi = whoami
w"h"o"a"mi = whoami -> OBS: need to match and be even number of them
w\ho\am\i
who$@ami


Windows: can also use ' ' and ""
who^ami
```

**Advanced blacklist bypass:**
- There are multiple operations we can combine which when combined lets us bypass the filter 
```

LINUX:
$(tr "[A-Z]" "[a-z]"<<<"WhOaMi") -> whoami (transform upper letters to lower)

$(rev<<<"imaohw") -> whoami

Base64:

echo -n "whoami" | base64 -> "d2hvYW1p"

base64 -d <<< "d2hvYW1p" -> whoami (first base64 encode the command, then inject the base64 decoded version)

When sending it as a payload we need to redirect the command to bash, ex:
bash<<<$(base64%09-d<<<d2hvYW1p)

Hex:
Same logic as base64 injection


WINDOWS: (ps and cmd is not case sensitive, so we can:)
WhOaMi -> whoami

Can also base64 and hex encode, but are more messy commands on windows. 

```

**Other ways to obfuscate our command payloads:**
- https://github.com/Bashfuscator/Bashfuscator (LINUX)
- https://github.com/danielbohannon/Invoke-DOSfuscation (WINDOWS)

#### Command injection prevention:
- **Avoid having system commands in your application being influenced by user input**. If it uses user input, it should use a more fitting function to achieve its goal.
- **Input validation** on both front and backend
- Use standardized functions in languages/frameworks/libs to validate userinput
- **Input Sanitization**, whitelisting characters, instead of blacklisting them -> Lookup you languages recommended way of sanitizing input. 
- **Server should be configures with good practice**, in case of a compromise: WAF, PoLP when running the webserver,  disable certain functionalities, limit the scope of accessible by the web application, reject double-encoded requests and non ascii characters in URLs, update and avoid outdated things

