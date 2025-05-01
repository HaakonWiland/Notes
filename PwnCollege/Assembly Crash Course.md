
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

```
_start:
        xor rax, rax
        xor rdx, rdx
        xor rcx, rcx

        add rax, [rsp]
        add rax, [rsp+8]
        add rax, [rsp+16]
        add rax, [rsp+24]

        mov rcx, 0x04
        div rcx

        push rax
```



**Absolute-jump:**
```
In this level, we will ask you to do an absolute jump. Perform the following: Jump to the absolute address `0x403000`.
```
Solution:
```
_start:
        mov rax, 0x403000
        jmp rax
```

**Relative-jump:**
```
- Make the first instruction in your code a `jmp`.
- Make that `jmp` a relative jump to 0x51 bytes from the current position.
- At the code location where the relative jump will redirect control flow, set `rax` to 0x1.
```

Solution:
```
_start:
        jmp $ + 0x53
        .rept 81
        nop
        .endr

        mov rax, 0x1
```
- This was a weird one 
- We jump from our current position `$` and 0x53 bytes forward. 
- Then we have to fill in the gap between our jump instruction, and the address we land in.
- To know how many bytes we have to fill in, we need to know how "big" the jump instruction is. From the disassembler, we can see that it is 2 bytes. 
- So we need to add 0x53-2 = 81 = 0x51 nop-bytes 
- I was confused, since the problem told us to jump 0x51 bytes.
- Lesson: Read the output, check the memory addresses and change code from there. 

**Jump-trampoline:**
```
Create a two jump trampoline:

- Make the first instruction in your code a `jmp`.
- Make that `jmp` a relative jump to 0x51 bytes from its current position.
- At 0x51, write the following code:
    - Place the top value on the stack into register `rdi`.
    - `jmp` to the absolute address 0x403000.
```

Solution:
```
_start:
        jmp $ + 0x53
        .rept 81
        nop
        .endr

        pop rdi
        xor rax, rax
        mov rax, 0x403000
        jmp rax
```
- The same temple as the prev problem
- To do the last jump, we have to put the memory address we want to jump to in a specific register first 
- since `jmp 0x403000` will translate to: "jump to the instruction at instruction pointer + 0x403000", which not what we want. 


**Conditional-jump:**
```
Using the above knowledge, implement the following:


if [x] is 0x7f454c46:
    y = [x+4] + [x+8] + [x+12]
else if [x] is 0x00005A4D:
    y = [x+4] - [x+8] - [x+12]
else:
    y = [x+4] * [x+8] * [x+12]


Where:

- `x = rdi`, `y = rax`.

Assume each dereferenced value is a signed dword. This means the values can start as a negative value at each memory position.

A valid solution will use the following at least once:

- `jmp` (any variant), `cmp`
```

