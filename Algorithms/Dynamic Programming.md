A class of Algorithms with similar idea as divide and conquer, and is basically the opposite as greedy algorithms.

**Idea:** Break up a problem into a series of overlapping sub-problems and build up solutions to larger and larger sub-problems. Hence we only need to solve each subproblem once. 
**When and how to use DP:** 
- Optimal substructure: An optimal solution to the whole problem can be fond from  its sub-problems. 
- Overlapping subproblems: The same subproblems are solved multiple times, hence we can take advantage of only each subproblem just once. 
- Storing of data: Use a data structure that lets up easely access the previous solutions, example a "Table" / "Memorization - Store results of sub-problems in a cache"


#### Dynamic programming tables:
**Description:** A multi-dimensional array where each entry represents the solution to a subproblem. 

**Knapsack problem:** You have a knapsack with that can carry a certain weight, then you have some items with weights and value, the goal is to maximize the value you hold in the knapsack. 

Can be used to solve the Knapsack problem:
![[Pasted image 20240802123510.png]]

**Time complexity:** Psudo-polynominal - NP complete, but there exists a polynominal algorithm that produces a feasible solution that thas value within 0,01% of optimum. 


#### Sequence Alignment problem:
**Description:** Given two sequences, we want to check how similar they are. 


#### Shortest path with DP: (All-Pairs Shortest Path (APSP) problems)
**Description:** Given n-vertex directed weighted graph, find a shortest path from vertex i to j for each of the n^2 vertex pairs. 
**With Dijkstras:** Dijkstras finds the shortest path from a given vertex to all of the other vertexes. Hence to solve the problem we would need to run the algorithm n times. This will have a time complexity of O(n^3) and only works when edges has cost > 0.
**With DP (Floyds shortest path):** 
- **Time complexity:** O(n^3)
- Works as long as there is no cycle whose length is < 0. 
- Ideal for graphs no larget that a copule of hundred nodes, and when a graph is dense. 

**Floyds shortest path:**
- IDEA: Check all paths from one node to another?
- Data structure: Represent the graph as a 2d adjecency matrix, "From to for each of the nodes"


**Bellman-Ford Algorithm:**
- Works on single-source-all-destination problems, **AND works with negative-cost edges.** BUT will not manage to solve for graphs with NEGATIVE WEIGHT CYCLES. 
- **Time compexity:** O(n^3) when adjecency matrix is used, **O(V x E) when adjecency lists are used.** Hence, Bellman-Ford can be faster for dense graphs
![[Pasted image 20240802134815.png]]





Common problems:
- The Knapsack Problem 
- Sequence Alignment 
- Shortest path in a graph 

Related concepts:
- Dynamic programming tables 
- Asymptotic running time for DP-algorithms. 

