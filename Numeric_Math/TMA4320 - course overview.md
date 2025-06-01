 
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
- Note: What is 'e' and how do we find it? -> This is just some theoretical point, instead of find this value, we just **bound** the error to the max of `n+1-derivative of f(x)`.

**Chebyshev Interpolation:**
![[Pasted image 20250508145457.png]]
- Can reduce interpolation error
- Distributed the nodes in the approximation differently; More nodes at the endpoints where the error is assumed to be bigger.  
- Often we have to translate the output nodes back to our original interval. 


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
- DEF: Newton-Cotes is a "class" of quadrature rules, where we have n equidistributed nodes, and have degree of exactness equal to n. 
![[Pasted image 20250502143724.png]]

- Trapezoidal rule and Simpsons rule are examples of **closed** Newton-Cotes 
- The methods **Degree of exactness** referee to highest degree polynomial that the rule integrates exactly over the interval 
- As we increase the degree of exactness(number of nodes), some weight must be negative to satisfy exactness 
- The above is due to the **Runge phenomenon: high degree polynomial interpolation over equally spaces points becomes unstable.** 
- The result is: Negative weights which amplify errors, and unstable solution. 
- Generally: we use Newton-Cotes with degree of exactness <= 8. 

#### Constructing quadrature rules:
![[Pasted image 20250424133710.png]]

#### Composite quadrature rules:
- Motivation: Improve accuracy. 
**General:**
![[Pasted image 20250424134641.png]]


**Composite trapezoidal rule:**
![[Pasted image 20250424134654.png]]
- Note: For the Comp-Trap-rule we have that a doubling of the number of subintervals, decrease the error by a forth! 

**Composite Simpsons rule:**
![[Pasted image 20250508125245.png]]
![[Pasted image 20250508125252.png]]
- Always start with x_0 = a, and end with x_m=b. 
- Compute h and h_half 
- Then just remember: "1/6,0,4,2,0"
**Error for the Comp-Trap-rule:**
![[Pasted image 20250424135006.png]]

**Error for Composite Simpsons rule:**
![[Pasted image 20250424135433.png]]
TODO - Assignment 2. 


### Numerical solution of ordinary differential equations:
NOTE: In this course we are only working with initial value problems.

- TODO: Butcher table? 

#### Methods:
- Higher order ODEs: Can be split up into a system of first order equations, then use a fitting method to solve each of them.  
- One step methods: ![[Pasted image 20250429113627.png]]
- We differ between **explicit one step methods and implicit**, where in explicit methods the increment function does NOT depend on y_(k+1).  
- Euler and Heun are examples of one step methods



##### (Explicit) Eulers method:
- IDEA: Approximate a continuous but unknown function, by a discreate function. We approx. the next point with a tangent.
- Fast and works for some functions, but is inaccurate if the functions is curvy 
- Based on a quadrature rule, with degree of exactness equal to 0
![[Pasted image 20250429110158.png]]
![[Pasted image 20250429110645.png]]

- How to choose tau? -> Easiest tau = (T-t_0) / N_max i.e (N_max + 1) evenly distributed points.  
When computing by hand, we can express a step in **Eulers method** as:
![[Pasted image 20250508161721.png]]



**Error study:**
- Smaller step size in Euler => numerical solution is closer to the exact solution 
- Consistency order: TODO
- Lipschitz condition: TODO
- Convergence of one step methods theorem:  ![[Pasted image 20250429121153.png]]
- **A method is convergent**: When step size goes to 0, our approx approaches the exact solution
- **Local truncation error**: Error made in a single step assuming perfect knowledge of the previous step. 
- **Global error:** Accumulates over many steps and is generally one lower then LT error. 
- Ex: Say Global error is of order 2( O(h^2) ), then a  halving in stepsize would reduce the error by a factor of 4; `(h/2)^2 = h^2/4`. 
- **EOC: Experimental Order of Convergence**, numerical estimate of the convergence rate of the method, based on observed errors rather than theoretical. Example: A order 2 method could really behave as a order 1 method due to conditions as: Rounding errors, bad implementation, step size choice etc. 


##### Heuns method:
- More accurate then Eulers method 
- Heuns method cost more to run, then Eulers (Heun has higher runtime)
- Heuns method is convergent of order 2 (Global error behaves like O(stepsize^2)). 
- Also go by the name improved Eulers 
![[Pasted image 20250429112835.png]]

When computing by hand, we can express a step with **Heuns method** as:
![[Pasted image 20250508161847.png]]
##### Runge-Kutta:
- A class of methods 
- IDEA: Each timestep approximate the derivative at multiple places, these are the `k_i` values. The `k_(i+1)` values are computed also, based on the previous k values. 
- The `a_ij` values are used to compute the `k_i` values
- Both Euler and Heun are examples of Runge-Kutta methods 
![[Pasted image 20250429115944.png]]
![[Pasted image 20250514161909.png]]



**Adaptive time steps:** (Not important for exam )
- Dynamically adjust step size during the numerical integration process 
- GOAL: Balance accuracy and computational efficiency 
- Increase when: Solution varies slowly
- Decrease when: Solution changes rapidly 
![[Pasted image 20250430145957.png]]


### Discrete Fourier Transform and it applications:
- Transforming periodic functions, into series of sin and cos
![[Pasted image 20250430151048.png]]

**Discrete Fourier transform:**
![[Pasted image 20250512124123.png]]
![[Pasted image 20250512125258.png]]

![[Pasted image 20250512124248.png]]
![[Pasted image 20250512125207.png]]


**Inner products:**
![[Pasted image 20250512124456.png]]


**Trigonometric interpolation: (Maybe look at later)**
- Given (x,y) data, we can interpolate the points, and approximate the underlying function using trig. 
- 1. We need to find the DFT coefficients satisfying the matrix:
- The **f** is the vector with the datapoints
![[Pasted image 20250513170121.png]]

- 2. we can find the N'th term approximation(polynomial) for the underlying function using the formula below:

![[Pasted image 20250513164825.png]]
- Here L is the period of the underlying function. 
- Note that the order of the approximation is limited by the number of sample points 

**Implementing discrete Fourier transform:**
- When implementing, it is to slow to construct a FT as a matrix -> O(n^2)
- So, we have the Fast Fourier transform (FFT) to implement the method -> `O(N*log(N))`.
- FFT IDEA: Implement the FT with N (number of data points) being a power off 2 

**Numerical Differentiation and Spectral derivatives:**
Context: 
- We have a cont. function f, periodic on `(0,L)`
- Then get N equi-distributed sampling points, then define N output values of f evaluated in these N points, `f(l), l=0...N-1`.
- From here we have 4 ways of approximating the derivative:
![[Pasted image 20250512132756.png]]

- Forward and backward difference have a **first order convergence rate** 
- Central difference has **second order of convergence** 
- Spectral derivative has higher convergence rate that the finite difference operators? -> Spectral not finite? 


**Exercises:**
![[Pasted image 20250512140402.png]]

