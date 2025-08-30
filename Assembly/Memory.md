#assembly 
#### Case study from the stack: 
Consider these memory addresses with their values:

```
0x7ffdc7f30f80: 0x0000000000000002      0x00007ffdc7f310c8

0x7ffdc7f30f90: 0x00007ffdc7f310b8      0x00000001ebc75d10
```

Here: `0x7ffdc7f30f80` is the first memory address. 

It contain two 8 byte values:
`0x0000000000000002   0x00007ffdc7f310c8`

Q: Why are these 8 byte:
- Each hex represent 4 bits, and there are 16 hex digets
- Each value is `4*16=64 bit = 8 byte`, that makes 16 in total.

The second memory address is `0x7ffdc7f30f90`. 
- Notice: ` 0x7ffdc7f30f90 - 0x7ffdc7f30f80 = 0x10 = 16`
- The addresses differ with 16 bytes. 

