#pwncollege 

### SETUID
- s replaces the x for the execution, example:
- `-rwsr-xr-x `
- Means that: Any user executing the program, will run it with the file owners privileges 

#### cp with s-bit
`cp --no-preserve=mode,ownership /flag ~/flag1`

- We can run cp with root permissions, to copy a file owned by root (not readable by us). 
- The we also overwrite the mode of the file, such that we have access to read the copied flag file. 

#### Authenticating into another group
- Say you want access to a file, which is only available by a unix group.
- If we have the password for this group we can temporarily get access to it by: `newgrp <groupname>`

#### Find user within a group
- Say you was access to a file, where a group in linux has access to it. 
- You want to figure out what accounts are in this group
- Then we can run: `getent group <groupname>`
- Then we can ex `su` to this user if we have their password 


