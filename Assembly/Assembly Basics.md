
#### Lessons I learned:
```
1. In x86-64 we are not allowed to 'mov' a 64 bit immediate directly into memory. So we have to 'mov' the immediate into a register, then move the content of the register into memory. 


```


#### Syntax:
```
<instruction_mnemonic>  <destination>, <source>



Moving values: (rax = rax + 10)
mov rbx, rax     // make a temp for x
add rbx, 10      // add 10 to that temp
mov rax, rbx     // move the temp back into x

```



#### Registers:

![[Pasted image 20240905160016.png]]
- **RSP:** A pointer to the top of the stack, changes when data is pushed and popped to the stack. 
- **RBP:** A pointer to the BASE of the stack. Usually do not change throughout the functions execution. When working with function, RBP works as a reference point for the functions stack frame. 


#### Division:
64-128 bit division:
- RAX and RDX always act as the **dividend**, where RDX is the 64 highest bits, and RAX the lower 64 bits. 
- We always have to prepare both these registers before we do a division. 
- We can use any register or memory address as **divisor.**
- We can also specify if we only want do to 64bit / 64bit division, by explicitly defining the dividend: `div rax, rcx`

Ex:
```
xor rax, rax,
xor rdx, rdx

mov rax, 100
mov rcx, 2

div rcx  <----- 100/2 
---
RAX = 50 (Quotient)
RDX = 0 (Remainder)
```


#### Jumping:
![[Pasted image 20250506212719.png]]


#### Functions:
In x86-64 assembly, defining a function involves setting up a label for the function, managing the stack frame, and adhering to the calling convention. The System V AMD64 ABI specifies that the first argument is passed in the `rdi` register, and the return value should be placed in the `rax` register.

Calling convention: https://en.wikipedia.org/wiki/X86_calling_conventions#System_V_AMD64_ABI

Basic structure of a function: 
```
my_function:
    ; Prologue
    push rbp            ; Save the base pointer
    mov rbp, rsp        ; Establish a new base pointer, now rbp and rsp                          point to the same address! 

    ; Function logic
    ; ... (your code here)

    ; Epilogue
    mov rsp, rbp        ; Restore the stack pointer
    pop rbp             ; Restore the base pointer
    ret                 ; Return to the caller

```

`call: Pushes the memory address of the next instruction onto the stack, then jumps to the value stored in the first argument.`

`ret: Pops the top value of the stack and jumps to it.`

- When a function i called, a new stack frame is created. It stores used local variables, function arguments, and return addresses 
- 