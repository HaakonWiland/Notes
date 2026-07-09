#lessons #hackthebox #enumeration #privesc 
#### Enumeration:
**05.07.26**
- Look for multiple layers of directories, the file / directory we are looking for could be nested.
- Never assume we cannot access a file within a directory just because we cannot access the directory itself. 

#### Privsec:
**05.07.26**
- Look for files you have access to, both user and group 
- Look for suid files 
- When we get initial access, look at the source code and config. Can use commands like, to seach for keywords. 
```
cat * | grep -i passw*
```
- Be aware of executables being called with filename and not calling the full path of the executable -> This is a potential vector. (PATH hijacking)

#### Web hacking:
- If we find a standardize application, like jenkins, redis, CMS stuff etc. look for CVEs and fuzz for common credentials.