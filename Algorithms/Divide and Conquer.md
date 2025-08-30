#algorithms #programming 
**(MS)Time complexity:** O(n log n) 
**(MS)Space Complexity:** O(n)
**Application:** Sorting 

**Generally:** A class of algorithmic techniques, which break the input into several parts. Then solves each part recursively, and combine them to find the overall solution. 

Most relevant example algorithm:  
- **Mergesort:**

**Common problems we solve with Divide and Conquer:**
- Counting Inversions problem 
- Finding the Closest pair of points problem 
- Integer Multiplication problem 

#### Merge Sort:
1. Divide array of elements into two halvs 
2. Recursively sort each half 
3. Merge the two halvs to make sorted whole. 

Merge sort recurrence: 
![[Pasted image 20240731141638.png]]


#### Counting Inversion problem:
Description: Comparing two arrays of elements,  where the goal is to count how "sorted" an array is (can also compare two or more arrays arrays).

Note: Brute forcing such problem would run at time complexity O(n^2)

![[Pasted image 20240731154549.png]]

![[Pasted image 20240731154704.png]]
![[Pasted image 20240731161941.png]]


#### Closest Pair of points problem:

**Description:** Given n points in the plain, find a pair with the smallest Euclidian distance between them. 

**Brute force:** O(n^2)

![[Pasted image 20240731162816.png]]


#### Integer multiplication problem: 

**Description**: Given two n-digit integers a,b, compute a x b. 
**Brute force**: O(n^2)
**Karatsuba:** O(n^1.585)





