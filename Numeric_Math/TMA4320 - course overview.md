
Main topics:
- Polynomial Interpolation
- Numerical Integration - Interpolatory quadrature rules 
- Numerical methods for ODE
- Fourier Transformations 


### Polynomial interpolation 
**Use cases:**
- We want to approximate functions over some bounded interval. Say we are given some points, and we want to know a value in between each points, we can use interpolation to estimate the value in this point. 

**Properties and factors to consider:**
- Method: How to compute the polynomials?
- Existence and uniqueness of results: **For Lagrange interpolation, yes to both!** 
- Error Analysis: How good is our approximation?
- Improvements 


**Definitions:**
![[Pasted image 20250424115214.png]]
![[Pasted image 20250424115645.png]]


#### Methods:
**Direct approach:**
![[Pasted image 20250424115849.png]]


**Lagrange interpolation:**
![[Pasted image 20250424120030.png]]
![[Pasted image 20250424120128.png]]
![[Pasted image 20250424120253.png]]

![[Pasted image 20250424120322.png]]


#### Python implementation:
Should make functions:
- Cardinal(): For generating the cardinal functions 
- Lagrange(): For constructing the polynomial based on the cardinal functions  

#### Properties:
**Existence and uniqueness:**
![[Pasted image 20250424121057.png]]
- Key factor to check when asked if there exist a unique solution: ARE THERE DISTINCT X VALUES? 


##### Error theory:
The relevant question to ask:
![[Pasted image 20250424121401.png]]

We have a formula for finding the error between the polynomial and the function:
![[Pasted image 20250424122255.png]]
- Note: What is 'e' and how do we find it?

**Chebyshev Interpolation:**
- TODO 

Error depend on:
- The number of points
- Distribution of points:![[Pasted image 20250424123702.png]]
- Smoothness of the function we are approximating. 

Other things:
- Uniformly spaced nodes **can** lead to large interpolation errors near the boundaries (Runge’s phenomenon).


### Numerical Integration - Interpolatory Quadrature Rules 
Problem: How do we solve definite integrals that are really complicated? It is hopeless with calculus. 

Solution: Numerical quadrature or quadrature rules:
![[Pasted image 20250424125128.png]]

**Examples of quadrature rules:**
- **Left and right endpoint rule:**
![[Pasted image 20250424125622.png]]

- **Trapezoidal rule:** 
![[Pasted image 20250424125905.png]]
- **The midpoint rule:** 
![[Pasted image 20250424125703.png]]

- **The Simpsons rule:**
![[Pasted image 20250424130051.png]]

- **GauB-Legendre quadrature:**




**Visual examples:**
![[Pasted image 20250424125446.png]]

Q: Strengths and weaknesses of the methods? 

#### Quadrature rules based on polynomial interpolation:
Idea: Choose some distinct points from the f on a given interval. Approximate the function with a polynomial based on the points. Now we have polynomial which is easier to integrate. (We tend to use the Lagrange interpolation).

![[Pasted image 20250424130538.png]]

NOTE: We can construct Lagrange-versions of the known-classic quadratic rules.
-> Q: Why would do we do this? What improves? 

#### Properties of the method:
**Exactness:**
![[Pasted image 20250424131522.png]]
![[Pasted image 20250424131554.png]]

**Estimates for the quadrature error:**
Q: What is the difference between exactness and error in this context?

General formula for estimating error:
![[Pasted image 20250424132028.png]]

Specific formula for estimating error on concrete methods:
**Trapezoidal rule:**
![[Pasted image 20250424132246.png]]

**Simpsons rule:**
![[Pasted image 20250424132311.png]]

**Ways to increase the accuracy of quadrature rules:**
- Increase the order of the interpolation polynomial used to construct the quadrature rule
- Subdivide the interval we are working with into smaller subintervals and apply a quadrature rule on each of the subintervals -> **Composite quadrature rules.**



#### Newton-Cotes formulas 
- Discuss Newton-Cotes formulas, including the issues with negative weights and their practical implications.
TODO 



#### Constructing quadrature rules:
![[Pasted image 20250424133710.png]]

#### Composite quadrature rules:
- Motivation: Improve accuracy. 
**General:**
![[Pasted image 20250424134641.png]]


**Composite trapezoidal rule:**
![[Pasted image 20250424134654.png]]
- Note: For the Comp-Trap-rule we have that a doubling of the number of subintervals, decrease the error by a forth! 

Error for the Comp-Trap-rule:
![[Pasted image 20250424135006.png]]

**Composite Simpsons rule:**
![[Pasted image 20250424135433.png]]
TODO - Assignment 2. 
