**Different definitions of an efficient algorithm:**


**Time complexity - asymptotic bounds:** 
Measurement of the amount of time an algorithm takes to complete as a function of the input size. 
- O: Worst-case scenario. 
- Omega: Best-case scenario.
- Theta: Tight bound scenario, algorithms running time is bounded both above and below. 

**Space Complexity:**
Measurement of the amount of memory an algorithm needs to run as a function of the input size. 

**Related concepts:** 
- Priority queue: -> Heaps implementation: 
- Proof of Correctness. 

#### How to analyze an algorithm:
1. **Understand the algorithm:**
- What is the input and output 
- Understand each step, logic and flow 
2. **Identify the Basic Operation:**
- Example: Comparisons, Arithmetic, data movements etc...
3. **Determine Time Complexity:**
- Loop Analysis: How many times do we loop in terms of n?
- Recursive Calls: Setup Recursive relation (If relevant), consider the Master Theorem if possible 
- Conditional Statements: Consider conditional branches 
- Worst case, best case and average case.
4. **Space complexity:**
5. **Check for optimization:**
6. **Verify Correctness:** 
- Algorithm sorts correctly for various test cases. 