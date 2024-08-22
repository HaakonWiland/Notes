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

