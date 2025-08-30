#algorithms #programming 
A class of Algorithms. 

**Generally:**  An algrorithm that solves a problem by choosing the optimal choice at each step, without worrying about what is the best choice in the future steps.  
**Properties that lets us use Greedy algorithm:** 
- A global optimal solution can be reached by choosing the optimal choice at each step. 
- A optimal solution to the entire problem contains the optimal solutions to the sub-problem. 


Example Algorithms:
- Prims Algorithm 
- Kruskal's Algorithm 
- Huffman / Huffman Codes 


Common problems which can be solved with Greedy Algorithms:
- Different Interval Scheduling problem.
- Shortest Path Problem -> Solved by Dijkstra's algorithm 
- Minimum Spanning Tree Problem.


#### Interval scheduling problems:
- **Description:** Given a set of intervals, each with a start and end time, the goal is to select the max number of non-overlapping intervals. 
- **Greedy solution:** Selecte the interval that finishes the earliest, hence making most room for the remaining intervals. 
- **Time complexity:** O(n log n)
- **NOTE:** Greedy method do not work well if intervals contain weight/value -> Consider using dynamic programming. 

![[Pasted image 20240801140002.png]]

Example: 
![[Pasted image 20240801140136.png]]

#### Optimal Caching problem: 

#### The shortest path problem: 
**Description:** Goal is t find the shortest path between two nodes in a graph. 
- Each edge has a non-negative weight w(u,v) representing the cost to travel from u to v. 
- Using **Dijkstras algorithm** we can find the shortest path between the starting node and all of the other nodes in the graph. (SINGLE SOURCE PROBLEM)
- **Dijkstras** CANNOT be used in graphs with NEGATIVE WEIGHTS.
**Time complexity:** Can vary based on implementation and data structure, but generally **O(E + log(V))** or **O(E+V) * log(V))** or **O(E log(V))** with heap prio-queue

#### Minimum spanning Tree problem:
**MSP def:** A spanning tree with the minimum total edge weight.
**Description:** Find the MSP of a given graph.
**Uniqueness:** If all edge weights are distinct, then the MST is unique. If there are equal edge weights, there may be multiple MSPs.
**Solutions:** Kruskals Algorithm , Prims Algorithm. 

**Kruskals Algorithm:**
![[Pasted image 20240801144816.png]]

**Time complexity:**  O(ElogE+Eα(V))
- O(E log E) sorts the edges
- The union-find data operation take O(Eα(V)), where α is hte ackermann function. 


**Prims Algorithm:**
![[Pasted image 20240801153200.png]]

**Time Complexity:** O(E log V) for a binary heap priority queue. 


#### Huffman coding:
**Usecases:** Lossless data compression, used to reduce the average length of encoded data by assigning shorter codes to more frequently occurring characters and longer codes to less frequent characters. 

![[Pasted image 20240801162130.png]]







