
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

solution:
```
_start:
        xor rbx, rbx
        mov ebx, [rdi]

        cmp ebx, 0x7f454c46
        je option1

        # Fall through
        cmp ebx, 0x00005A4D
        je option2

        # Fall through
        xor rax, rax
        mov eax, 1
        imul eax, [rdi+4]
        imul eax, [rdi+8]
        imul eax, [rdi+12]
        jmp done


option1:
        xor rax, rax
        add eax, [rdi+4]
        add eax, [rdi+8]
        add eax, [rdi+12]
        jmp done


option2:
        xor rax, rax
        add eax, [rdi+4]
        sub eax, [rdi+8]
        sub eax, [rdi+12]
        jmp done

done:
```
- This was a hard one
- The general flow is easy, but we have to be careful about the registers we use, and the way we finish our labels.
- All values we dereferenced, are said to be dword = 32 bit.  So we have to do the computations in a register of this size, so instead of using rax, we use eax and so on. 
- Also, we have to finish each label or "branch" by jumping to another place in the code. Or else it will execute the code below. So we just jump to an empty label "done".

**Indirect-jump:**
```
Using the above knowledge, implement the following logic:


if rdi is 0:
  jmp 0x40301e
else if rdi is 1:
  jmp 0x4030da
else if rdi is 2:
  jmp 0x4031d5
else if rdi is 3:
  jmp 0x403268
else:
  jmp 0x40332c


Please do the above with the following constraints:

- Assume `rdi` will NOT be negative.
- Use no more than 1 `cmp` instruction.
- Use no more than 3 jumps (of any variant).
- We will provide you with the number to 'switch' on in `rdi`.
- We will provide you with a jump table base address in `rsi`.
```

Solution:
```
_start:

        xor rbx, rbx
        mov rbx, rdi

        cmp rbx, 3
        ja default_case

        jmp [rsi + rbx * 8]


default_case:
        jmp [rsi + 4 * 8]
```
- Assembly does not have a switch statement keyword 
- The addresses we are supposed to jump to change each run, but are located in rsi with 8 bit spacing? 
- The default case is located at `[rsi + 32]`
- Since we want to consider all cases where rbx is bigger then 3, we have to jump to a label, and spesify that we want to jump to rsi + 32. Since `jmp [rsi + rbx * 8]`, could be different depending how big rbx is.

**Average-loop**
```
Please compute the average of `n` consecutive quad words, where:

- `rdi` = memory address of the 1st quad word
- `rsi` = `n` (amount to loop for)
- `rax` = average computed


We will now set the following in preparation for your code:
  [0x4042c8:0x4044b0] = {n qwords]}
  rdi = 0x4042c8
  rsi = 61

NOTE:
0x4044b0 - 0x4042c8 = 488 = 61 * 8
```

solution:
```
_start:
        xor rbx, rbx
        xor rax, rax
        xor rdx, rdx

loop:
        xor r8, r8 #Dont really need this xor. 
        mov r8, [rdi+rbx * 8]

        add rax, r8
        inc rbx
        cmp rbx, rsi
        jb loop
        jmp end

end: #Do not really need this label, we can just let if fall through 
        div rsi
```

- Assume all the quad words are "after each other" in rsi
- We are working with q words, so why is the spacing 8? -> A qword is 8 byte. When we do memory addressing in assembly we think in bytes not bits; `[rdi+rbx * 8]`
- NOTE: We do not need to do the `jmp end` thing. 

**Count-non-zero:**
```
Count the consecutive non-zero bytes in a contiguous region of memory, where:

- `rdi` = memory address of the 1st byte
- `rax` = number of consecutive non-zero bytes

Additionally, if `rdi = 0`, then set `rax = 0` (we will check)!

An example test-case, let:

- `rdi = 0x1000`
- `[0x1000] = 0x41`
- `[0x1001] = 0x42`
- `[0x1002] = 0x43`
- `[0x1003] = 0x00`

Then: `rax = 3` should be set.
```

```
_start:
        xor rax, rax
        xor rbx, rbx

while_loop:
        cmp rdi, 0
        jz done

        mov bl, [rdi + rax]
        cmp bl, 0
        jz done

        inc rax
        jmp while_loop

done:
```
- We have to check if rdi is 0 first, if it is, the operation `[rdi ..]` becomes an invalid operation. 
- Since we are only counting bytes here, we can use `bl` instead of `rbx`

**sting-lower:**
```
Please implement the following logic:


str_lower(src_addr):
  i = 0
  if src_addr != 0:
    while [src_addr] != 0x00:
      if [src_addr] <= 0x5a:
        [src_addr] = foo([src_addr])
        i += 1
      src_addr += 1
  return i

```

- Then accessing memory, and only want to access a specific part of the register (Byte, word, etc),  we need to say the size explicitly. Example: `cmp byte ptr [rdi], 0x00`. Note that this is different from `cmp [dil], 0x00`, since `dil` is just the lower 8 bits of `rdi`, but not a memory address itself, thus we can not dereference it. 