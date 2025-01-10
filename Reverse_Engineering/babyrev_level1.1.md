
file output:
```
`babyrev_level1.1: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=82776275b56fb0947991a9b14e7a6eba983e45ea, for GNU/Linux 3.2.0, stripped`
```

- setuid: When executed, the program runs with the permissions of the file owner. 
- ELF: Executable and Linkable Format - used on Linux 
- 64-bit LSB shared object: 64 bit with Little-Endian architecture (x86-64)
- dynamically linked: Linking done at runtime 
- interpreter /lib64/ld-linux-x86-64.so.2: Type of linker?
- BuildID sha1 =82776275b56fb0947991a9b14e7a6eba983e45ea: Unique identifier of the built binary. 
- GNU/Linux 3.2.0: Minimum version of linux kernal required.
- stripped: Binary has been stripped of symbol information.


checksec output:
```
[*] '/challenge/babyrev_level1.1'
    Arch:     amd64-64-little
    RELRO:    Full RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      PIE enabled
```

List of security measures, look into them later.  