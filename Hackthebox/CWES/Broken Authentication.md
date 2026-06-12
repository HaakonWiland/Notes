#CWES #AuthnenticationBypass 

#### Lessons:
- Even though we have not completed the 2fa check for a user, we still might be able to find authorized endpoints. Ref. gladys profile page. 


![[Pasted image 20260529044859.png]]

#### Authentication methods:
**Knowledge:**
- User have to provide something the user knows, ex. password, passphrases, PINs,etc. 

**Ownership:**
- User have to provide something the user owns, ex. physical object, id-card, token or smartphone with authentication app. 

**Inherent:**
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

Before attacking with a bruteforce attack, it can be smart to test for default credentials first. Can google after them or use spesiffic wordlist for default creds (seclist)


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

#### Brute forcing 2FA codes
Similar idea to the last one, we can brute force all combinations of a simple otp if they do not limit our requests.

```shell
ffuf -w ./tokens.txt -u http://bf_2fa.htb/2fa.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -b "PHPSESSID=fpfcm5b8dh1ibfa7idg0he7l93" -d "otp=FUZZ" -fr "Invalid 2FA Code"
```
- Note: we need to include the session token from the successful login. 

#### Counter against bruteforce:
- Rate limiting 
- CAPTCHAs 

#### Attacking password reset functionalities
Some sites have a password reset functionality that only require us to know the username and answer a security password, this can be exploited if the user has a easy to guess answer. ex. what city were you born can be OSINTed or bruteforced. 

```shell
ffuf -w ./city_wordlist.txt -u http://154.57.164.73:32364/security_question.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -b "PHPSESSID=u3q5h1f41pvsdukae7o1aq3bse" -d "security_response=FUZZ" -fr "Incorrect response."
```

#### Direct access 
- Some resources does not check for authentication, even though they should have.
- This mean we sometimes can access hidden resources if we just ask for them.
- Thus could also manifest itself in parameters, ex. id parameters with no authentication check (IDOR)

#### Attacking tokens:
- If the authentication tokens use weak entropy, we might be able to manipulate the session tokens to gain authenticated access.
- Examples could be: tokens based on hex or base64 encoding, weak algorithms, or bad JWT practices. 


#### Other attacks:
- Sessions fixation: Attacker forces a user to use a specific sessions token via sending a malicious link. Attacker can later access this user.
- Sessions token timeout: If the token does not expire, it can be used to authenticated as the user later if it at any point gets leaked. Best practice for session timeouts can range from minutes to hours. 


#### Assignment:
Password does not meet our password policy:

- Contains at least one digit
- Contains at least one lower-case character
- Contains at least one upper-case character
- Contains NO special characters
- Is exactly 12 characters long
```shell
grep '[[:upper:]]' rockyou.txt | grep '[[:lower:]]' | grep '[[:digit:]]' | grep -E '^[[:alnum:]]{12}$' > custom_wordlist.txt
```



haakon
ZszQ55MUpXHv

User does not exist
Unknown username or password.

User does exist 
Invalid credentials:

gladys
Gladys
dWinaldasD13

timeout from opt check? Can only send 3 requests before i need to relogin.