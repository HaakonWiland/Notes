#hackthebox #lab 


#### Enumeration:
```
sudo nmap -sC -Pn -v -oA nmap_bashed <ip>
```

- port 80 is open.
- [https://github.com/Arrexel/phpbash](https://github.com/Arrexel/phpbash) 
- Supported Methods: POST OPTIONS GET HEAD


**Fuzzing for subdirectories:**
```
ffuf -w raft-medium-directories.txt -u http://10.129.41.124/FUZZ
```



```
uploads                 [Status: 301, Size: 316, Words: 20, Lines: 10, Duration: 102ms]
dev                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 101ms]
php                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 102ms]
fonts                   [Status: 301, Size: 314, Words: 20, Lines: 10, Duration: 24ms]
css                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 3786ms]
images                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 3787ms]
js                      [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 3860ms]
server-status           [Status: 403, Size: 301, Words: 22, Lines: 12, Duration: 23ms]

```
- Looking at all of these, we find out that /dev/phpbash.php is just a authenticated webshell, which gives us the user flag. 

