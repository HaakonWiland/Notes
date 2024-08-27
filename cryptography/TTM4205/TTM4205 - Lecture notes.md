#Cryptography 

### Randomness 1:
#Entropy #Randomness

#### Lingo:
- **Randomness:** Absence of predictable pattern 
- **Entropy:** Quantifying the amount of randomness / unpredictability, often measured in bits. 
- **Random Oracle:** Theoretical model used to describe in idealized function that produces truly random output for every unique input. (But it is deterministic). In practice, there are not truly random oracles, the concept is mainly used to prove security of cryptosystems.


Where do we use Randomness:
- Parameter and key generation
- Probabilistic encryption and signatures
- Modeling of hash functions
- Analyzing attacks and security 
- Protecting implementations 

Generating **real** randomness we need a source of entropy:
- Temperature measurements
- Acoustic noise
- Air Turbulence
- Electromagnetic radiation 

What modern computers does to create randomness:
- Keyboard timings
- Mouse movements
- Disk and network activity
- Lava lamps


PRNG: [[Cryptography concepts and theory]]

Some PRNGs that might be predictable given some samples or statistical testing:
- Mersenne Twister (Python, PHP,.. )
- Linear Congruential Generator (JAVA, Python,..)
- rand and drand48 (libc) , rand and mt_rand(PHP)

#### Schnorr Signatures
![[Pasted image 20240820110805.png]]
![[Pasted image 20240820110813.png]]


#### ElGamal
![[Pasted image 20240820110914.png]]

#### RSA: [[Cryptography concepts and theory]]
![[Pasted image 20240820110959.png]]


### Randomness 2: Randomization 

#### **How to check if a number if prime: (if the number is really big > 2^128)** [[Cryptography concepts and theory]]
- Sieving methods -> Not going to work for a really big number 
- Wilsons Theorem -> Still exponential work, no good. #TODO fill in theorem.
- Monte Carlo algorithm -> Algo might be correct, with a given probabiltiy 
- **The miller-rabin method** -> Need log2(p) operations, which is pretty good. Can also be used with a pre-computed prime numbers, to run the algorithm less times.  
- **Soloway-Strassen**
- **Fermat testing (NB: Carmichael number)**
#TODO Fermats theorem 


**An attacker can convince you that a composit is prime:**
- Can split up an algebraic group, into multiple smaller groups, then use CRT
- In RSA, say n is product of composit, it is easy to compute the factorication of n.
- Zp, where p is not prime. We can have a generator which is less then the order of the group. Which lets us work with a smaller group in practice. 

#### **How factorize a large bi-prime:**
- Cap the options with the sqrt(n), and then use the check if prime number methods. 
- **Number field Sieve** -> Generally the most efficient algorithm. 
- Fun fact: Larget number factoriced in public: 829 bits 

#### Failure of RSA in practise:
- Same seed when sampling primes 
- low entropy RNG / PRNG
- Related primes from know algorithm 

#### Qualities of a RNG that we need to ensure:
#TODO 
