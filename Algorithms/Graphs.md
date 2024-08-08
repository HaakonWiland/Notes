A class of data structure.

Different types of graph:
- **Undirected graph:** We can go both ways in an edge connecting two nodes. 
- **Directed graph:** An edge goes FROM node u TO node v. 
- **Tree:** An undirected graph is a tree if it is connected and does not contain a cycle. 
- **Binary Tree:** A tree where each node has AT MOST two children. 
- **Directed Acyclic graphs (DAG)**: Directed graphs with no cycles. 
- **Connected graphs**: Every pair of nodes in an undirected graph, there is a path between them. 
- **Spanning tree:** a sub-graph that is a tree which includes all of the nodes of the "parent-graph". 

Related terminology:
- **Path**: In a undirected graph there is a sequence of nodes with the property that each consecutive pair is joined by an edge.
- **Simple Path**: A path is simple if all nodes are distinct. 
- **Cycle:** A path where all nodes are distinct. 
- **Bipartite:** An undirected graph where every edge has one "red" and one "blue" node. NOTE: a graph is Bipartite  IFF it contain no odd length cycle.
- **Topological ordering**: In a directed graph G, an ordering of its nodes as v1 , v2, ... ,vn such that for every edge (vi, vj) we have i < j. 
- **Depth of binary tree:** Total number of edges from a root node to a target node. 

Common problems we solve with algorithms:
- Connectivity between two nodes problem
- Shortest path problem
- Directed reachability: Given node v, find all nodes reachable from s. 



#### Topological sort algorithm 
**Time complexity:** Finds a topological ordering in **O(V+E)**
**Uniqueness:** Topological orderings does not need to be unique! 
**When does Top-Ordering exist:** Only in a DAG graph.

Generall structure of the algorithm:
![[Pasted image 20240802113627.png]]




**Example of topological ordering**: Think of the nodes as dependencies
![[Pasted image 20240731120908.png]]



Related concepts:

- Implementation of graphs 

