#Debugging #pwn #ReverseEngineering

Tips on configuration:
- ~/.gdbinit file will contain information gdb uses each time it starts. 
- include a `set disassembly-flavor intel` in this file, this gives us intel syntax. 
- Can also add: `source /opt/gef/gef.py` (in pwn college at least)



Base GDB - Basic commands:
```
- r <program>: run a program
- gdb -x my_script.gdb <program>: Run a program with a script interacting with it. 
NOTE: .gdb scripts use the same syntax as the normal gdb commands. 
NOTE: We do prefere using pwn lib in python when doing pwn CTF. 

- print $<register>: Prints the current value of a register

- b *<memory address>: Sets a breakpoint at a memory address. 
- b *<function name> + <number>: Allow us set a break point 'within' a function. 
- d break <number>: Deletes breakpoint with number. 

- info register: Prints the current values of all registers
- info break: Displays all breakpoints

- Examine intruction, x/i $<register>: 
Example: x/i $rsp: Shows us the next instruction that is going to be executed 
Can also: x/10i $rsp: To show the next 10 instrcutions. 

General form: x/<n><u><f> <address>
<n>: number of elements: 
<u>: Unit size: (b) 1 bytes, (h) 2 bytes, (w) 4 bytes, and (g) 8 bytes
<f>: Format: decimal, hex, string, and instruction



- Disassemble <function name>: Dumps all assemble code for a function. 
TIP: Notice the "=>", this arrow shows us where our rip is.  

- si: Step instruction, goes into another function if one where to be called
- ni: Next instruction, does not go into another function 

- finish: step all commands in the function, returns to where the function as originally called. 
- continue: Run the program untill it hits a breakpoint

- set <variable> = <value>: Set a variable, memory value, or register to a spesific value. 
NOTE: We can indeed actually move the rip to another memory address, this is, without executing other commands. 
```
