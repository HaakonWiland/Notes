
**Little-endian-write:**
```
Goal:
Using the earlier mentioned info, perform the following:
  Set [rdi] = 0xdeadbeef00001337
  Set [rsi] = 0xc0ffee0000

Program run 1:
We will now set the following in preparation for your code:
  [0x4040a8] = 0xffffffffffffffff
  [0x404d28] = 0xffffffffffffffff
  rdi = 0x4040a8
  rsi = 0x404d28

Program run 2:
We will now set the following in preparation for your code:
  [0x404768] = 0xffffffffffffffff
  [0x404bd8] = 0xffffffffffffffff
  rdi = 0x404768
  rsi = 0x404bd8
```
- Program change the memory address each run, but not the value
- Program does not change the registers, but changes the value 
- The register values are equal to the memory address 
Solution: 
```
.intel_syntax noprefix
.section .text
.global _start

_start:
        xor rax, rax
        mov rax, 0xdeadbeef00001337
        mov [rdi], rax

        xor rax, rax
        mov rax, 0xc0ffee0000
        mov [rsi], rax

```
- get the desired value into a register, then assign this value to the memory address stored in rdi and rsi. 


**Memory-sum:**
```
Perform the following:
  Load two consecutive quad words from the address stored in rdi
  Calculate the sum of the previous steps quad words.
  Store the sum at the address in rsi

We will now set the following in preparation for your code:
  [0x4041b8] = 0x6aa3
  [0x4041c0] = 0xf2f72
  rdi = 0x4041b8
  rsi = 0x4046b0
```

solution:
```
_start:
        xor rax, rax
        xor rbx, rbx
        mov rax, [rdi]
        mov rbx, [rdi+8]

        add rax, rbx
        mov [rsi], rax
```
- We need to store two variables in two registers.
- The first variable is located in the first 8 bytes of rdi, the second is in the second 8 bytes of rdi 
- Then its just a matter of summing them, and assigning this value to the value rsi holds. 

**Stack-subtraction:**
```
Using these instructions, take the top value of the stack, subtract `rdi` from it, then put it back.
```

Solution:
```
_start:
        xor rax, rax
        pop rax
        sub rax, rdi
        push rax
```
- Pop the top value of the stack into rax 
- Then subtract rdi from rax, and push this value onto the stack 

**Swap-stack-values:**
```
Only using push and pop:
Swap values in `rdi` and `rsi`.
```

Solution:
```
_start:
        push rsi
        push rdi
        pop rsi
        pop rdi
```
- Just get both values onto the stack, then pop them in the correct order. 

**Average-stack-values:**
```
Without using `pop`, please calculate the average of 4 consecutive quad words stored on the stack. Push the average on the stack.
```



