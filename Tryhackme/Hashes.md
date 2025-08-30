#tryhackme #hash

Online cracking tools:
- [https://crackstation.net/](https://crackstation.net/)
- [https://hashes.com/en/decrypt/hash](https://hashes.com/en/decrypt/hash) 

Identify hash:
- https://hashes.com/en/tools/hash_identifier
- Hash-identifier python: https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py 
  

Based on the prefix in a hash, we can find out what hash algorithm as used: 
- Check up with: man 5 crypt: https://manpages.debian.org/unstable/libcrypt-dev/crypt.5.en.html 

#### hashcat:

```
hashcat -m <hash_type> -a <attack_mode> hashfile wordlis
```
  
Finding the correct mode for a given hash type:
[https://hashcat.net/wiki/doku.php?id=example_hashes](https://hashcat.net/wiki/doku.php?id=example_hashes)


#### John the ripper:

Basic format: 
```
john --format=<format> --wordlist=<wordlist> <file with hash to crack>
```

Finding the correct format option for a given hash:
```
john --list=formats | grep -iF <the hash type>
```

Unshadowing passwords:
```
unshadow passwd.txt shadow.txt > cracked.hashes
```

NOTE: we need passwd file before shadow file. 

The hard thing is often to get the hash on the right format such that john can handle it. Based on what we are cracking we can use different tools such as:

```
- unshadow: converts the hashes of the linux shadow hashes(note you also need the passwd file). 

Note: when you have unshadowed the hash, you dont need to spesify the format fo john. Something we can just run: 
john <unshadowed_passwd_file>

- zip2john: Converts hash of a zipfile password.
- rar2john: Converts hash of a rarfile password.
- ssh2john: Converts hash of an encrypted ssh private key. 
```


We can also make custom rule: https://www.openwall.com/john/doc/RULES.shtml






