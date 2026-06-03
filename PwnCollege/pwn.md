#ctf #pwn 


#### your-first-overflow(easy):
- Stack pointer: 0x7ffc54e9a7a0
- input buffer: 0x7ffc54e9a7f0, 18 bytes long. 
- Win variable: 0x7ffc54e9a7e4 
- Base pointer: 0x7ffc54e9a7f0 

Win con: 
- Set the win variable to non zero. 

Solution:
- Win variables is 20 bytes after the start of the input buffer
- The input buffer does not enforce 18 bytes 
- We send an input of non-zero bytes which is 21 bytes long. 

#### your-first-overflow(hard):

- We dont know how many bytes to write. 


Win con:
- Same idea as before; send in some bytes and set the win variable to non zero. 