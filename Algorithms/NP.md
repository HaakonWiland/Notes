#algorithms #programming 
A class of problems, where the problem cannot be solved efficiently. 

Lingo and definitions:
- **Tractable**: Problems that can be solved in polynomial time.
- **Intractable problems:** Problems not solvable in polynomial time, often in the category of NP-hard or NP-Complete. 
- **Reduction of a problem:** Transform one problem into another problem in such a way that a solution to the new problem corresponds to a solution to the original problem. Hence, if we can solve the new problem efficiently, then we can also solve the original problem efficiently using the transformation. 

Key concepts:
- Difference between: P, NP, NP-Complete and NP-Hard -> Prove such properties

**Complexity Classes:**
- **P:** Problems solvable in polynomial time.
- **NP(Nondeterministic Polynomial time):** Problems for which a given solution can be **verified** in polynomial time.  
- **NP-Complete:** Subset of NP problems, that are at least as hard as any other problem in NP. Think of them as the hardest NP problems, so if any of the NP-Complete problems can be solved in polynominal time, then ALL NP problems can be solved in polynominal time. Given a problem L, we say that L is NP complete if:
1. L is in NP.
2. Every problem in NP can be reduced to L in Poly-Time. 

- **NP-Hard:** Problems that are at least as hard as the hardest problems in NP, but NOT necessarily in NP. 


#### Comparing problems:
![[Pasted image 20240808110122.png]]


#### 2 Example problems which is X =p Y: 
![[Pasted image 20240808110459.png]]

![[Pasted image 20240808110645.png]]

#### Proving a problem is NP-Complete:
1. Show that a given solution to a problem, can be verified in poly-time. (prove its NP)
2. Show that any problem in NP can be **reduced** to the problem we are looking at in polynominal time. 

#### Common problems we can compare with:

**(NP) Hamiltion Cycle problem:** Given a graph, does there exist a cycle that visits each vertex exactly once and returns to the starting vertex?  

**(NP-Complete) 3-SAT problem:** A problem where we have a boolean statements, or multiple boolean statements, and we want to find a compination of values of the variables which makes the whole problem true. 




#### Is a given problem NP or Tractable:
1. Understand the problem: Is it a yes/no problem or an optimization problem. Can we try a input and constitute a solution?
2. Check for tracability: how does it relate to common problems that have polynominal solutions: Sorting, seaching, shortest path in a graph. Can we reduce the problem to a well know p-problem?
3. Check for NP: Given a candidat solution, can we verify the solution in polynominal time? Does the problem resemble a well-know NP-complete problem: SAT, Traveling salesman, knapsack, graph coloring, it likely NP-Complete. 



Common problems: 
- Traveling Salesman Problem
- Hamiltonian Cycle Problem 
- Satisfiability Problem

Related concepts: 
- Polynomial-Time Reduction
- Extending the limits of Tractability 
- Approximation Algorithms: Appox. solutions to NP type problems. 