#hackthebox #smb #Windows #lab 

- **135/tcp open  msrpc  Microsoft Windows RPC:** Remote Procedure Call, essential for communication between windows applications - needs to be running. 
- **139/tcp open  netbios-ssn   Microsoft Windows netbios-ssn:** Network Basic Input/Output System, also a essensial service on a windows 



SMB Administrator user without password, we can list the SMB shares:
```shell
smbclient --list=<target> --user=ADMINISTRATOR 
```
- Note since we have access to all the files, we can just navigate to the folder where the flag is. 

Using psexec.py to get shell via SMB:
```shell
python3 psexec.py WORKGROUP/ADMINISTRATOR@10.129.24.22 --no-pass
```
- Using this repo: https://github.com/fortra/impacket/tree/master (Python tools for working with different network protocols)
- This is a very aggressive attack and i likely to get detected in a real world engagement by defender or similar - but we get a SYSTEM shell which is very powerful. 