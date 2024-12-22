

Useful commands:
``` 
WIN+r:
- lusrmgr.msc: Give info over lical users and groups 
- MSconfig(system configuration): Advanced troubleshooting, startup diagnose, Computer management(compmgmt), scedule tasks, event logger, performance monitor(perfmon), system info(msinfo32), Windows Registry(regedit)  


```


CMD commands:
```
- tasklist: Lists running processes.
	Can also be used with seach function, example: tasklist /FI "imagename eq notepad.exe" = search for process with name equal to notepad.exe

- taskkill: terminale a process. Example: taskkill /PID 4567

```


PowerShell:
```

Usefull cmdlets:
- Get-help: Get more info about another cmdlet
- Get-Command: Lets other cmdlets, use with filtering to find the one you need.
- Get-Alias: Get alias for commands example ls insted of Get-Childitem




Examples:

- Get-Service | Where-Object DisplayName -like "*merry life*": Get the service which displayname property has "merry life" somewhere inside it.

- Get-ChildItem | Where-Object -Property Length -gt 100

(Same as: Get-ChildItem | Where-Object Length -gt 100): Lists the elements in the current folder that are "longer" then 100. 
```


Connect to windows machine via RDP on linux:
```
xfreerdp <ip>
```

