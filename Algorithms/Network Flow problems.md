#algorithms #programming 
A class of problems, where we solve problems related to a directed graph with edges having capacities and receiving flow from other edges. 

Example algorithms:
- Ford-Fulkerson Algorithm

Common problems:
- Maximum-Flow Problem 
- Minimum Cut 
- Bipartite Matching problem 

Related concepts:
- Identify problems that can be solved using network flow. 


#### Terminology:
- **Residual Graph**: A graph that identify all nodes that are reachable from the source s unsing BFS / DFS. These nodes from one part of the partitions of the graph? 
- **Edge-disjoint:** Two paths are edge-disjoint if they have no edge in common. 
![[Pasted image 20240803125619.png]]
- **Network connectivity:**  Given a directed graph, and two nodes s,t, find the min number of edges whoseremoval disconnects t from s: 
![[Pasted image 20240803125918.png]]

![[Pasted image 20240803130039.png]]





**Max-Flow Min-Cut Theorem:**
![[Pasted image 20240802171648.png]]

#### Ford-Fulkersen: (MAX FLOW)
**Solves the problem:** With an inf input source, how much "flow" can we push through the network given that each edge has a certain capacity.
**Time complexity:** If implemented the right way **O(E x F)** , where E is number of edges IN THE GRAPH and F is the maximum flow value.  


#### Minimum Cut problem:
**Description:** Finding the smallest set of edges that, if removed, would disconnect the source node s from the sink node t.


#### Bipartite matching problem: (Undirected & unweighed)
**Description:** Find maximum matchings (Pairs ) in a bipartite graph. Think of a matching as a node only appearing in at most ONE edge. To make the pairs, we need to group the nodes in the graph in two different groups, which is possible with a bipartite graph. 
**Solution:** The maximum machtings = value of the max flow in the graph. 

Example:
![[Pasted image 20240803125205.png]]

