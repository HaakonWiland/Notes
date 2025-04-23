```
ssh -i SSHpwncollege hacker@dojo.pwn.college
```


HOT TIPS: If you want to quickly ssh to a server.
```
Host gluteus  # f.eks. dingseboms
    HostName # f.eks. dingseboms.hackerspace-ntnu.no
    Port 2222 # f.eks. 2222
    User hackerspace # f.eks. hackerspace
    AddKeysToAgent yes # slippe å skrive nøkkel passord hver gang
    UseKeychain yes # bare for macOS
    IdentityFile # f.eks. ~/.ssh/id_hackerspace
```