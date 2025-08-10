#pwncollege #ctf #endian #assembly 

#### Handy resources:
Ascii table: https://www.ascii-code.com/ 

#### C calling convention:
```c
int main(int argc, char *argv[], char *envp[]);

```
- argc: number of arguments
- argv: pointer to array of arguments string 
- envp: pointer to array of environment settings 


the same can look like this in an decompiler
```c 

int32_t main (void) {
    *(var_20h) = edi;  // var_20h = argc
    *(path)     = rsi; // path     = argv
    *(var_40h) = rdx;  // var_40h  = envp
```

#### Big Endian vs Little Endian
![[Pasted image 20250727113320.png]]

#### Writing bytes to a file:
- Some characters cannot be typed from a keyboard, insted we have to write them as raw bytes.

With python:
- Can use "wb" - write bytes 
 ```python
with open("my-file", "wb") as out_file:
    out_file.write(b"HELLO WORLD")
```
- Then the byte representation of "HELLO WORLD" would be written to the out_file 

We can also just write the bytes in hex:
```python
with open("my-file", "wb") as out_file:
  out_file.write(b"HELLO \x57\x4f\x52\x4c\x44\x00")
```

- Can also look at the "struct" packet in python for alternative ways. 

##### More examples: 

(Metadata and Data Python)
```python
null = b"\x00"


with open("a.cimg", "wb") as outfile:

     magic_number = b"\x3c\x3a\x4d\x47" # bytes 0 - 3
     version_number = b"\x01\x00" # bytes 4-5
     width = b"\x32" # byte 6
     height = b"\x0b" # byte 7
     padding = null*7 # 8 - 14
     header = magic_number + version_number + width + height + padding

     data = b"A" * (550)

     print(header)
     print(f"length of header: {len(header)}")

     print(data)
     print(f"length of data: {len(data)}")
     outfile.write(header)
     outfile.write(data)
```
