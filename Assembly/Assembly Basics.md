
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
- **RBP:** A pointer to the BASE of the stack (last value of stack?), usually do not change throughout the functions execution. 