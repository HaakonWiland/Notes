Also known as: Deferred Acceptance algorithm.

**Properties:**
- Stability: No pair of elements prefer each other over their assigned matches, preventing "blocking pairs", which is guaranteed by the Algorithm! 
- Optimality: **Can** be optimal for one side of the matching parties. In **GS where men propose**, each man receives best valid partner! 
- Fairness: Aims to create a fair matching based on the preferences provided. 
- **Time complexity:** O(n^2)
- A stable match must always lie in between a man-optimal solution and women optimal solution, hence if a solution is both man-and-women optimal, it is the ONLY valid solution. 

**Definitions:**
- Valid partners: W is a valid partner of m if there exist some stable matching where m and w are paired. 

**Applications:**
Basically all problems that can be formulated as a Stable matching type problem.
- Collage Admission: Matching students and collages 
- Job Market Matching: Matching job seekers to employers 
- etc... 

**Common problems:** 
- Solves the Stable Matching Problem -> solves using arrays and lists  

#### Stable Matching Problem: 

Goal: Everyone is matched monogamously, with no unstable pairs.  Note: For a given instance of the stable matching problem, there may exist **multiple stable matchings.** 
Input: A preference lists of n men and n women. 

GS-algo used on the problem: 
```
Repeat {
- Each single man proposes to his "next" preferable woman
- Each woman gets engaged with the most preferable man among her current proposals.
} until everyone is engaged.
```

Example: 
![[Pasted image 20240723101728.png]]
```
Gale-Shapley

a1 : b1 
(a2 : b3) 
(a3 : b2) 
a4 : b2 
a3 : b3
a2 : b4
```

