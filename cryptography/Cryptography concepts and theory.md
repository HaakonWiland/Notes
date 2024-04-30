
---
tags:
- #Cryptography 
- #TTM4135

--- 
Definitions: 
- **Confidentiality:** Prevent unauthorised disclosure of information.
- **Integrity:** Prevent unauthorised modification of informaton.
- **Availability:** Ensuring resources are accessibe when required by an authorised user. 
- **Entity Authentication:** Process by which one entity is assured of the identity of a secound entity.
- **Non-repudiation:** Proof of the origin, authenticity and integrity of the data. 
- **Group generator:** An element of a group that through repeated application of the group operation, can generate the entire group.  
- **Finite field:** A group with addition and multiplication, and with prop. *Finite cadinality*, and *Characteristic*.
- **symmetric and asymmetric ciphers:** Symmetric ciphers uses the same key for encryption and decryption, while asymmetric used two keys, one for each operation.  
- **Ciphertext only attack:** Attacker have access to only a set of ciphertexts, that is, no information about the plaintext generating the cipherstexts. 
- **Known plaintext attack:** Attacker has info about ciphertext and the plaintext generating them. 
- **Chosen plaintext attack:** Attacker knows some the ciphertext of his own chosen plaintexts. 
- **Chosen ciphertext attack:** Attacker can take arbitrary ciphertexts, and find the corresponding plaintext. 
- **Kerckhoffs principle:** Security of the cryptosystem must lie in the choice of its keys only, everything else should be considered public knowledge. 
- **Passive attacks:** Attack that attempts to learn/make use of, the information from a system. Examples: **Release of message**, **Traffic analysis**.
- **Active attacks:** Attack that attempts to alter system resources / affect their operation. Examples: **Replay, Data modification, Masquerade, Denial of service**
- **Confusion:** Substitution to make the relationship between the key and ciphertext as complex as possible. 
- **Diffusion:** Transformations that dissipate the statistical properties of the plaintext across the ciphertext. 
- **Differental cryptoanalysis:** Attack based on difference between two input plaintexts that can be correlated to differences in their ciphertext. 
- **Collision resistance:** Hard to find h(x) = h(y), x != y
- **Second preimage resistance:** Given x, its hard to find y st. h(y) = h(x)
- **One-wayness:** Function that is easy one way, but hard the other way. ex: Factorization of big numbers. 
- **Birthday paradox:** Surprisingly high chance for two people in a set to have the same birthday. Relatable to hash collisions. 
- **HMAC:** Construction for creating a MAC by using a hash function and a secret key.  
- **GCM mode:** Symmetric encryption mode of operation which provides confidentiality and data integrity. 
- **Big O Notation:** Upper bound for time/space complexity. 
- **Fermat test:** a^(p-1) = 1 mod p => if p satisfy for all 1 < a < p, p is probably prime else its composite.  
- **Miller-Rabin test:** More reliable but slower test of prim then fermat test. 
- **Factorization:** Decomposing number into prime factors is hard. 
- **Discrete log. problems:** Find exponent that satisfy a equation with a base, moduli and a number. 
- **Trapdoor oneway function:** A one way function, that when given some key information is easy to invert. (else its hard)
- **RSA padding:** Extra bits (redundance and random), which results in encrypting the same plaintext twice results in different ciphertexts. This prevents dict-attacks and Håstand attacks.
- **Prime number theorem:** Primes are rarer as the numbers grow larger.  
- **Square and multiply algo.:** Algo. for faster decryption / encryption in RSA. 
- **Håstads attack:** Can be done if SAME massage m is encrypted with the SAME public modulus but different ciphertext moduli (assumed to be relative prime, else we can just use Euclidean algo). Can then use CTR, see EX. 5.5.  Same exponent e also make it easier. 
- **Millers theorem:** Find discrete logarithm is as hard as to factor. 

### **Basic encryption:**
- **Caesar cipher:**
- **Random Substitution cipher:**
- **Polyalphabetic substitution:** Sub. cipher using multiple mappings, hence smooth the frequency distribution. 
![[Pasted image 20240205140251.png]]

- Vigenere cipher: Type of periodic substitution cipher. Choosing a key of length d, for each letter in the key take the plaintext letter sum it with the key letter mod n(where n is the length of alphabet). 
![[Pasted image 20240205140653.png]]

### **Hill cipher:**

- TYPE: Polygraphic cipher
- Weakness: Linear -> plaintext attacks easy.
- Breaking: Knowing bits of the plaintext and d, lets us solve equation for k and k-inverse. 

![[Pasted image 20240205141307.png]]

Splitting up out plaintext into bits of d-characters, that is, write each plaintextblock as column vectors in the dxd matrix. If there is insufficient letters to fill a block we use padding, preferably with uncommon letters. 


### **Steam cipher:**

- TYPE: Symmetric key cipher, 
- Requirements: Keystream need good randomness properties 
- Different versions: Synchronous S.C , Self-Synchronizing S.C

Starting of the algorithm converts the plaintext to digits. Then generates a pseudo-random key stream, generated by an internal secret key. Then successively, preforming a bitwise operation(XOR, OR) of each keystream-digit and plaintext digit.   

The stream cipher operate on continuous stream of data(unlike the block cipher which work on fixed size blocks).

![[Pasted image 20240205144412.png]]


### **One-time-pad:**
- Attributes: PERFECT SECRECY 
- Requirements: Key is >= length of message, Key is onetime use and truly random
- Weakness: Message only as long as the key, can only use each letter once in the message, key-transportation(length of key >= length of message).

![[Pasted image 20240205144550.png]]


Can also use binary one time pad:
![[Pasted image 20240205145142.png]]

### **Block cipher:**

- TYPE: Symmetric key cipher 

Plaintext split up in blocks of fixed size, and each block is encrypted with the same key. Block size is typically 64 bits or 256 bits.   

**Techniques used within block ciphers:**

**Product cipher:** Cryptosystem in which the encryption function is formed by applying several sub-encryption functions. 

**Iterated ciphers:** 

Encryption process is divided into r similar rounds, and all sub-encryption functions are all the same function, g,  called the round function. Then from a master key, K, we derive the round keys ,Ki. (We find the round keys using key schedule). 

Encrypting with iterated ciphers:
![[Pasted image 20240205150941.png]]

Decrypting with iterated ciphers: 
![[Pasted image 20240205151022.png]]


We categorize iterated ciphers into two types:

**Feistel Ciphers(Feistel Network):** Example DES
- Attributes: Works for any round function(since we never invert it) and any number of rounds. 
- Requirements: Round function is the only non-linear part of system, so need to be picked correctly. 


In the Feistel cipher, the round function swaps the two halves of the block and forms a new right hand half.  

Video for explanation: https://www.youtube.com/watch?v=FGhj3CGxl8I

![[Pasted image 20240205152921.png]]

To decrypt, we reverse the the order of traverse the "feistel ladder", applying the last key first.   

**Substitution-Permutation Networks (SPN):** Example AES
- Requirements: Block length n must allow each block to be split into m sub-blocks of length l: n = l * m. 


Key scheduling: Masterkey (K) is split into multiple roundkeys (Ki)

1. The plaintext is XOR'ed with the first roundkey K0. 
2. Then we split the result into a number of S-boxes(bits we send it is **substituted** with another set of bits, often based on a lookup table). This possess introduces non-linearity to the system.  
3. Then the result of step 2 is send as input into a P-box, which rearrange the order of bits. This permutation of the bits is fixed for each round of the SPN. 
4. The result of step 3 is then XOR'ed with the next key round K1
5. Then we repeat step 1-4 again a number of times, the final result we call the ciphertext.  
![[Pasted image 20240213125718.png]]

Video explanation: https://www.youtube.com/watch?v=DLjzI5dX8jc


#### Data Encryption Standard (DES):
- TYPE: 16-round Feistel cipher.
- Key length: 56 Bits
- Data block length: 64 Bits. 

Step by step Encryption:

1. The 64 bit length data block is permutated  by an initial fixed permutation IP  
2. The permutated data block then undergo a 16 round Feistel operation.
		2.1. The RIGHT HAND 32bit datablock is expaned into 48 bits(based of a predefined table), then XOR'ed with a 48 bit roundkey. We derive the 48 bit roundkey from the 56 bit masterkey. This is done buy splitting 56 bits into 2 x 28 bit halvs, which then is shifted and compressed to make a 48 bit roundkey.   (NOTE. that each roundkey is different).
		2.2: The result of 2.1 is split into 8 s-boxes(6 bits into each box) S1-S8, and substitute them (based on predefined table) for 4 new bits that each box send out. 
		2.3 The 8 boxes with 4 bits each is then sent into a permutation box P, which rearrange the bits (Fixed permutation).
		2.4 The result of 2.3 is then XOR'ed with the 32 bit LEFT HAND original datablock.
		2.5 The original RIGHT HAND side of the of the datablock becomes the left hand side of the next round, and our result 2.4 is the new right hand side. 
		THIS IS REPEATED 16 TIMES.  
		![[Pasted image 20240213133450.png]]

		
3. After the Feistel operation we apply a final permutation IP^-1. This effectively "undoes" the initial permutation. This makes it into a format that is easy to decrypt given the correct key. 

We often refer the process of going through the S - and P-boxes in step 2.2 and 2.3 as the encryption function f. 

Versions: 
- Double:
- Triple: 

Possible attacks:
- Requirements: 
- Brute force: 

#### Advance Encryption Standard (AES):
- TYPE: symmetric key block cipher, BYTE based design
- Data block length: 128 bits
- Key length: 128 , 192 , 256 bits. More bits -> More rounds (10), (12) ,(14)-> More secure. 
- Data block size: 16 bytes (16 * 8 = 128). Think of it as a 4x4 grid, where each entry is a byte (8 bits).
![[Pasted image 20240213175858.png]]




Step by step Encryption:
1. Plaintext of 128 bits XOR'ed with the first round key K1. 
2. SUB BYTES: Each byte in the data block is substituted with another byte from a s-box.
3. SHIFT ROWS: Each byte is shifted cyclically to the left: first row not shifted, second round shifted one to left, and so on. 
4. MIX COLUMNS: For each column in the matrix, mix up the entries within the columns. Done by taking each the columns and "multiplied" with a fixed matrix. Lookup GL(2^8) for the details.  
5. ADD ROUND KEY: After step 4, we take that state XOR'ed with the round key. 

The steps 1-5 is repeated round_number amount of times. Note the last round we do not mix the columns, this is just to simplify decryption.  

Decryption:
Doing the same operations just backwards, staring with the last round key etc. (Fill in more).

Video explanation: https://www.youtube.com/watch?v=O4xNJsjtN6E

Check up cryptools visualization. 

### Modes of Operation:
If we would like to apply a cipher to a larger amount of data, say, AES to some data that is >128bits, then we would have to split up the data, and apply AES to each part. The techniques used to split the data, apply the cipher to each part, then combining it to derive the final cipher is what we call **Modes of operation.** 

We need these modes of operation because this process can be insecure.

Terminology: 
- Initialization vector (IV): starts the encryption process before encrypting the first plaintext block. This will result in a plaintext being encrypted with the same key, will result in a different cipher when the IV is different for each time we encrypt. 
- Keystream: sequence of pseudorandom bits/bytes generated by a steam cipher. We use the keystream with (XOR) the plaintext to make the cipher. 


Different purpose:
- Provide confidentiality -> Often done by introducing randomization.
- Integrity -> often by Authentication 

Mods can also have different features for practical reasons:
- Parallel processing: multiple plaintexts etc.
- Error propagation: Bit errors result in multiple bit errors -> Easier to detect modifications 
- Padding: To complete blocks, is added before encryption, and removed when decryption. 

**Some standard modes from NIST:** 
![[Pasted image 20240214112111.png]]

**Electronic Code book(ECB):**
![[Pasted image 20240214112650.png]]

![[Pasted image 20240214112717.png]]

![[Pasted image 20240214112749.png]]


**Cipher block chaining (CBC):**
![[Pasted image 20240214113652.png]]
![[Pasted image 20240214113703.png]]
![[Pasted image 20240214113809.png]]'


**Counter (CTR) mode:** Mode of operation that turns a block cipher into a stream cipher. 

TYPE: Synchronous stream cipher.
![[Pasted image 20240214115618.png]]
![[Pasted image 20240214115629.png]]
![[Pasted image 20240214115642.png]]
![[Pasted image 20240214115736.png]]

### Random Numbers:

Hard to define what a random string of bits is, we more often think of THE WAY a bit of string is GENERATED as being random or not.

True random number generator (TRNG): Physical process which outputs each valid string independently with equal probability.

Pseudo random number generator (PRNG):  Deterministic algorithm which approximates a TRNG. 

Terminology: 
- Deterministic: Gives the same output each time, given that we use the same key/IV/nonce etc.

Often we use a TRNG as seed for a PRNG. 

Entropy Sources for random bit generation: https://csrc.nist.gov/pubs/sp/800/90/b/final
-> Give examples where we can find physical sources for true randomness, and how to validate them. 

**Deterministic Random Bit Generators (DRBG)**: (Fill inn later)

Functions:
- Instantiate:
- Generate:
- Reseed:
- Test:
- Uninstantiate: 

Properties of secure DRBG:
- Backtrack risistance:
- Forward prediction resistance: 

Examples:

**CTR_DRBG:**


**Dual_EC_DRBG:**


More:
Specific DRBG algorithms: https://csrc.nist.gov/publications/detail/sp/800-90a/rev-1/final 

### Hash functions:

**Uses of hash functions:**
Note:
- Hash != encryption 
- Hash does not depend on a key
- Not possible to go backwards to find the input. 

General: 
- Helping authentication: 
-> Authenticate the hash of a message to authenticate message 
-> Building block for message authentication codes(HMAC)
-> Building block for signatures. 

Terminology:
- Compression function: Takes two n-bit input strings x1 and x2 and produces an n-bit output string y. 

Definition: Public function with properties: 
1. It is simple and fast to compute
2. It takes in a message of arbitrary length, and returns a result of fixed length.

![[Pasted image 20240214124446.png]]

**Birthday paradox:**
General: if we choose around √M values from a set of size M, the probability of getting two values the same is around 0.5. -> This gives us a general rule of thumb for how many different elements in the images of a given hash function that should exist. We base this of how many hashed a computer is able of guess.

**Iterated Hash functions:** As with the different modes of operation used to split data into multiple parts, that then is used as input to a cipher, we can do the same with hashes. 

-> Iterated hashes split the input into blocks of fixed size and operate on each block sequentially using a function. 

**Merkle-Damgård construction:** 
1. Break message m into n bit-blocks m1 , m2 , .... ml.
2. Add padding and encoding of the length of m (may or may not add one block)
3. Input each block into compression function h along with chained output(Use an IV to start). Note the compression function is a function that takes in two k-bit strings and sends out one k-bit string.  

![[Pasted image 20240214131621.png]]

Note: If compression function h is collision-resistant, then hash function h is collision-resistant. (CHECK UP PROOF) #TODO

Possible weaknesses:
- Length extension attack: Once you have one collision -> easy to find more. e.i. we would have two inputs, that when send into the hash function would yield the same output: H(M1) = H(M2). <=>  two different messages with the same hash. 
- Second-preimage attacks: Generally not as hard as they should be. 

Hash functions using M-D Construction: MD5, SHA-1, SHA-2-family. 

MDx: Not secure in 2024
SHA-0 , SHA-1: 
- 160 bit output
- SHA-0 broken 2004
- SHA-1 First collison in 2017

SHA-2 family:
![[Pasted image 20240214132558.png]]

Sometimes we write hash function as taking in keys, but note that it must be hard to find a collision for a randomly generated key s and the keys is not kept secret. 
![[Pasted image 20240214133446.png]]


**Storing passwords for login:**
- Usually store the hash with some salt, and not the password: h = H(password, salt), and store (salt,h) in the database. 
- Its then easy to check the password that a user types, and compare it against the hash and salt. 
- Hard for an attacker for to recover password from the hash, since it is preimage resistant. 
- Also, attacker needs to store different salts for each dictionary attack. 

### MAC and authenticated encryption:

Mechanism used for message integrity and authentication. 

Concept:
Both sender and receiver have the secret key K(Symmetric key), and access to a MAC algorithm which outputs a tag of fixed length. The sender sends the pair (M,T), a message M of arbitrary length, with the tag T. Then the recipient needs to recompute the tag based on M, and check the he gets the same Tag output T. 

Formula: MAC(M,K) = T 

MAC PROPERTIES:
- Unforgeability: Impossible to find tag T without knowing key K.
- 

While hiding messages is important, we also want to develop integrity in the communication. e.i. Ensuring that the message came from the correct person and that the message is not modified. 

Keyed-hash Message Authentication Code (HMAC):
TYPE: Iterated cryptographic hash function 
USES: Standardized in applications such as TLS and IPsec 

Construction:
![[Pasted image 20240226113844.png]]

Properties: 
- Secure if function H is collision resistant or if H is psudorandom. 
- Designed to resist length extension attacks 


***Combining encryption and message authentication:**
3 possible ways of doing this:

M: Message
K = key
K1 , K2: K split into two parts. 

- Encrypt and MAC: Encrypt M and apply MAC to M, e.i. the MAC take is M as an input NOT C. Then we send C,T to the receiver. 
- MAC then encrypt: Apply MAC to M to get tag T, then encrypt M || T => C. Then send C to the receiver. 
- (Safest approach) Encrypt then MAC: C = enc(M, K1) , T = MAC(C, K2) => Then we send C and T to the receiver. 

Since the MAC does not guarantee anything about secrecy (only integrity), there may be possible to find information about M based on the MAC(M,K). Hence encrypting before applying the MAC is considered safest. 

Also MAC then Encrypt might lead to decryption errors since we pad M with the tag T: Padding may be incorrect or tag may not verify. An attacker may also intercept the message, and distinguish between the failures and exploit this. 


**Authenticated encryption with associated data(AEAD):** #MORE?
TYPE: Symmetric key cryptosystem 
![[Pasted image 20240226123534.png]]


Galois Counter Mode(GCM):
TYPE: Block cipher mode providing AEAD
USED: In web-based TLS 
INVOLVES: CTR mode on block cipher,  with a keyed hash  function called GHASH.

Article: https://csrc.nist.gov/pubs/sp/800/38/d/final

Exploitation of GCM and GHASH: #TODO

More about storing passwords:
- Storing the password hash with the salt good, but will not slow down per-password attacks. Possible solution is PBKDF2 = Password based key derivation function 2.

PBKDF2:
Explain how it works #TODO 


### Number Theory for public key cryptography:

Related theorems:
- Chinese remainder 
- Fermat 
- Euler 

Testing for primiality:
- Brute force / trial division is slow, and is not practical
- More practical is Fermat Test and Miller-Rabin Test

**Fermat primiality test:**

Concept:
We know: If a number p is prime then a^(p−1) (mod p) = 1 for all a with gcd(a, p) = 1.
We can use this to examine a number n and check that a^(n-1) (mod n) != 1 => we know n is not prime. Else we assume its a prime. 

This method can fail with some probability, hence to work around this, we repeat the test with different base values a.

Example, for some numbers, the algorithm will always output probable prime for every a with gcd(a,n) = 1, such numbers we call *Carmichael numers*. Examples of this is 561, 1105,...

Algorithm:
![[Pasted image 20240226140908.png]]

Miller-Rabin test:
USES: Check if a number is prime. Generate large primes.
Most widely used test for generating large prime numbers. 

Concept:
![[Pasted image 20240226141407.png]]

Algorithm:
![[Pasted image 20240226141622.png]]

#TODO Explain in own words, and why it works. 

Notes on complexity theory:
![[Pasted image 20240226143151.png]]


Can categorize: 
- O(m^t), for some int t: Polynomial time function
- O(b^m), for some number b>1: Exponential time function. In cryptography we view a problem of this type as hard. Example: Brute force key search is exponential (keys is a bit string of m elements, then there is 2^m possible keys).

Problem classification:
We classify a problem according to the MINIMUM time and space needed to solve the HARDEST instance of the problem on a deterministic computer.

Some important problems:
1. Integer factorization: Given an int, find its prime factors. Best solution is using *Number field sieve*. 
2. Discrete logarithm problem(DLP): Given base g, prime p and integer 0< y < p, find x such that: y = g^x mod p 

Solutions to such problems on conventional computers are sub-exponential, slower then polynomial but faster then any exponential. 

We can compare other problems in crypto, for example brute force key search, with the int-factorization problem or DLP. This gives some context to how difficult something would be. Example: 
![[Pasted image 20240226145803.png]]


So brute force search of 128-bit key(Example AES key), would take roughly same computational effort as factorization of 3072 bit number(two factors) or finding discrete logs in Z*p with p of length 3072.  

### Public key cryptography(PKC) = asymmetric cryptography:
PKC provides some features which cannot be achieved with symmetric key cryptography, and is widely applied for key management in protocols such as TLS and IPSec. 

Advantages compared to symmetric cryptography:
1. Key management: no need to transported confidentially. 
2. Digital signatures can be obtained.


Characterized by:
- Encryption and decryption keys are different
- ENCRYPTION KEY is public, DECRYPTION KEY is private 
- Finding the private key from knowledge of public key must be a hard computational problem.


Prerequisite:
- One way functions: a function that is easy to compute f(x) for a given x, but is computationally hard to compute f^-1 (y) = x, given a y. HOT TOPIC if such function actually exist. Believed one-way functions: multiplication of large primes, Exponentiation(invest is discrete logarithms). 
- Trapdoor one way functions: A one way function, that when given additional information(trapdoor) then the inverse is easy to compute. 

Concept:
A stores her public key PKa in a public directory, anyone can find HER key, and encrypt a message that one SHE can decrypt. We have cipher C = Enc(M, PKa) and recovery of message M = Dec(C, SKa).  

PKC is more computationally expensive then SKC, so a typical use case is combining them, we call this *Hybrid encryption*:
![[Pasted image 20240226153610.png]]


### RSA (Example of PKC):
TYPE: Public key cryptography 
USES: 
- Message encryption
- Digital signatures
- Distributing keys in SKC(ref hybrid encryption)
- User authentication by providing knowledge of private key

Concept:
![[Pasted image 20240226170601.png]]

![[Pasted image 20240226170622.png]]

#TODO Proof of RSA correctness, refresh this such that you can explain it easily. 

Implementation issues:
- Key generating: Choice of e, generating large primes 
- Encryption and decryption algorithms: 
- Formatting data(padding)

**Generating p and q:**
- Recommended at least 1024 bits 
![[Pasted image 20240226171850.png]]

**Choice of e:**
- Optimally: chosen at random 
- For practical reasons e should be small(efficiency).
- 65537 is a common choice of e 

**Fast exponentiation (encryption/decryption):**
- Square and multiply modular exponentiation algorithm. https://en.wikipedia.org/wiki/Exponentiation_by_squaring
- Can also use Chinese remainder theorem: #TODO  How and why, fill in.

**Formatting data:**
- Using RSA on a messages directly, where the message is just encoded as numbers is a weak cryptosystem: Vulnerable to dictionary-known-plaintext-attacks, Guessing plaintext and checking if it encrypts to the correct cipher, Håstands attack. 
- Hence, we need to use padding on the message before we encrypt. 
- Two ways we add padding: PKCS #1, Optimal asymmetric encryption padding OAEP.

PKCS number 1: 
![[Pasted image 20240226173805.png]]


OAEP: #TODO powerpoint very simply explain this, do own reading or do not prioritize.  


Håstands attack:
![[Pasted image 20240226173437.png]]

Side channel attacks: Attacks that do not directly attack the mathematics behind RSA, but rather unintended leakage of information from physical implementations of the algorithm. 
- Timing attacks: Exploit time taken to execute cryptographic operations 
- Power analysis: Exploit power usage of private key operations to gain information about private key.
- Fault analysis: Measure effect of interfering with the private key operations.  

End notes:
- Security of RSA depend on n being hard to split into p,q. Hence, breaking RSA is not harder then the factorization problem.  
- Finding the private key from the public key is as hard as factorizing the modulus, and its not possible to find the private key WITHOUT factorizing the modulus. (See Miller, and Millers algorithm).
- Quantum computers can use Shors algo to factorize in polynomial time. 
- Poor random number generation can and have caused problems using RSA. 



### PKC based on Discrete Logarithms:

Main version PKC, these versions are often more efficient then RSA.

**Diffie-Hellman key exchange:**
GOAL: A,B wants to share a secret using only public communications.
Public knowledge: There is a generator g of a multi group G of order t

step 1: A,B each select random value a,b respectively where 0 < a,b < t.
step 2: A sends g^a to B, and B sends g^b to A. (over insecure channel)
step 3: They both find the secret key Z = g^ab
=> They now have a common secret key. 

Common choice today for G is an elliptic curve group.  

Example with Z*p:
![[Pasted image 20240229143525.png]]

Attack on Diffie-Hellman:
-> Intercept the value of g^a, and take the discrete log to obtain a. (Can do the same for b). 

Note: This setup of D-H protocol does not use authentication, hence A,B does not know who the secret Z is shared with. This can enable man-in-the-middle attack, where an adversary sets up two keys, one with A and one with B, then he could relay messages between the two. 

**Static Diffie-Helleman:**
Keys are made to longtime use. 

step 1: A chooses long-term private key XA with corresponding public key YA = g^XA, B does the same.
step 2: A,B find a shared secret S  = g^XAXB by looking up each other public keys. #CHECK What stops attacker from doing the same?

**Elgamal cryptosystem:** Turns D-H protocol into cryptosystem. 

Setup example:
![[Pasted image 20240229145221.png]]
![[Pasted image 20240229145452.png]]

#TODO Why it works. 

Breaking the cryptosystem is equivelent to solving the DLP: 
![[Pasted image 20240229145834.png]]

In general, we think of solving DLP as difficult as the factoring n problem, where n is the product of two primes. Hence, we think of both methods of having the same level of security. 


**Elliptic curves:**
Basic explanation:
![[Pasted image 20240229150205.png]]
![[Pasted image 20240229150351.png]]


We can generate new elliptic curves at any time, but in general we use standard curves that have proven to have certain properties.

Check out: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-186.pdf

Generally: Cryptosystems based on descrete logarithms can be constructed by elliptic curves as well as Z*p. 

Note: Post quantum most PKC would not be considered safe. Mainly due to Shors algo for factorization being speed up, which can be used both on factorization problem or DLP.

Read more about PQC: https://csrc.nist.gov/Projects/post-quantum-cryptography/post-quantum-cryptography-standardization

### Digital Signatures:

PKC make obtaining digital signatures possible, and is widely deployed in authentication protocols. 

Remember: MACs allow only an entity with the shared secret to generate a valid MAC tag, provides data integrity and data authentication. 

Digital signatures: uses PKC to provide the properties of MAC and more. 
![[Pasted image 20240229152332.png]]

Schemes of digital signatures:
- key generation: Generates two keys, one for signing and one for verifying. 
- signature generation
- signature verification 

Note: There is should be computationally infeasible for anyone without signing key to construct a signature that gets verified. (unforgeability)


Possible attacks: #MORE 
- Key recovery:
- Selective forgery:
- Existential forgery:

We consider a digital signature secure only if they can resist existential forgery under a chosen message attack.   

**RSA signatures:** #MORE  Explain how and why it works.

Steps - generating keys:
1. The modulus, n = pq is defined, p and q being large primes.
2. Two exponents e,d are generated, where ed mod phi(n) = 1
3. Private signing key sk = (d,p,q), Public verification key pk = (e,n). 

signature generation:
input message m and modulus n, and private exponent d, and apply hash function h: ![[Pasted image 20240304111354.png]]
Signature verification:
input message m, and claimed signature sigma and the public key (e,n):
![[Pasted image 20240304111524.png]]


**Discrete logarithm signatures:** #TODO Explain and understand all of these.


**Elgamal signatures:**
![[Pasted image 20240304111932.png]]
![[Pasted image 20240304111951.png]]



**Schnorr signatures:**

**DSA - standard by NIST:**

**ECDSA - (DSA with elliptic curves):**

### Key Establishment:
Setting up keys in a cryptographic protocol, to protect a subsequent communication session. 

We break up key management into 4 phases:
1. Key generation: Ideally keys are random
2. Key distribution: keys must be distributed in a secure fashion
3. Key storage: keys must be accessible for use but not to unauthorized users
4. Key destruction: removing a key from memory is not always easy 

Type of keys:
- **Long term keys:** Intended to be used for a long period. Can ether be symmetric or asymmetric keys depending on how they are used.  
- **Ephemeral keys:** Single use, then deleted. Example Diffie-Hellman. Both Ephemeral - and Long term keys are used in establishment of session keys.  
- **Session keys:** Intended for one communication session. Ususally symmetric keys used to protect communications in a session with ciphers. Also, keys for different sessions should be independent and have no effect on other sessions. 

Security goals:
- Authentication: A and B have a session key Kab, then Kab should not be shared with a different party.
- Confidentiality: Adversary is unable to obtain the session key accepted by a particular party. 

Note that both parties can achieve authentication, or only on side achieve this(unilateral). 


**Categories of key establishment protocols:**

**Key pre-distribution: Keys set in advance.****
HOW: Trusted authority (TA) generates and distributes long term keys to all users when joining a system. TA only need to be "online" in the pre-distribution phase. 

EXAMPLE: Simplest version assigns a secret key to each pair of users(poor scalability).

**Key transport: One party chooses the key and distributes it.** #MORE Get a better understanding of this.

HOW: TA share long-term shared key with each user. TA generates and sends session keys to users when requested and protected by the long term keys. 

WEAKNESS: Scalability can be a problem, and TA is a single point of attack and needs to be trusted. 

EXAMPLE: Kerberos.  
https://datatracker.ietf.org/doc/html/rfc4120

VERSIONS:
Asymmetric version: One user chooses key material and sends it encrypted with the other party's public key. Then each party can include a random nonce value to ensure that the key is new. A key derivation function (KDF, often a HMAC) binds the secret key material with other protocol elements to prevent certain attacks. 

note: not forward secure. 
![[Pasted image 20240305180600.png]]



**Key agreement: Two or more parties contribute the session key.** 
HOW: Two parties each provide input to the keying material. Then usually add authentication with public keys, for example by signing the exchanged messages. 

EXAMPLE: Diffie-Hellman protocol(Included in TLS).

Signed Diffie-Hellman example: #TODO 
Note: Forward secure. 
![[Pasted image 20240305182516.png]]

**Needham-Schroeder protocol:**
- Basis from many protocols, example Kerberos. 
- Vulnerable to replay attacks. 

Key exchange protocol for secure communication over an insecure network, achieved by establishing a session key between two parties wanting to communicate. #MORE Explain and understand, include discussion about replay attacks. 

**Kerberos:** #TODO 


**(Perfect) Forward secrecy:**
Property of protocols that ensures that past communication sessions remain secure even if long term secret keys used in the protocol is compromised in the future.  

**Post compromise security (PCS):**
If long term key "evolve" over time, an attacker cannot take advantage of capturing the long term key unless his is fast. 

EXAMPLE: Send a new Diffie-Hellman share every message and change the session key also after each message. (ref Signal).






### Digital Certificates (DC) & Public key infrastructure (PKI):
While similar to digital signatures, Digital CERTIFICATES serves as a tool to verify the identity of users and establish trust. Remember Digital SIGNATURES is used to ensure the authenticity and integrity of digital documents/messages. 

DCs normally contain a users Public key and the owners identity. Then the certificate is digitally signed by a trusted party, refereed to as certification authority(CA). 

**Public key infrastructure:**
*A framework that established in issue, maintain, and revoke public-key certificates.*

Role of the CA: 
- Create, issues and revokes certificates for users and other CAs.

X.509:
A standard format for public key certificates and key management in a PKI, defining structure of DCs, rules of issuance, distribution and validation. 

Using a certificate:
- Validating a certificate: Checking that the CA signatures is valid and that any conditions set in the certificate are correct. This is done with the public key of the CA. 

### TLS (Transport Layer Security):
Most widely used security protocol, used to secure communications with banks, online shops, email providers etc.

Main versions: TLS 1.2 and TLS 1.3

Designed to provide: secure reliable end to end services over TCP. 

TLS protocol stack:
![[Pasted image 20240306145016.png]]

TLS handshake:
- Client and server agree on encryption parameters, and general setup of the session.

TLS change cipher spec: 
- Normally used after the handshake protocol to indicate commencement of secure traffic. e.i. saying that the data being send from this point on is encrypted.

TLS Alert:
- Handles connection by sending an "alert" message of various degrees of severity 

TLS cipersuites: (within TLS handshake protocol)
- Sets cryptographic algorithms and parameters used during TLS handshake protocol, and the symmetric algorithms used in the record protocol.
- Many different ciphersuits exist.

Record protocol:
- Message confidentiality: Ensure that the message contents cannot be read in transit 
- Message integrity: Ensure that the receiver can detect if a message is modified in transit. 
- These services can be provided by a symmetric encryption algorithm and a MAC. 
- Format:
![[Pasted image 20240306152016.png]]

### IPsec:

### Email security and secure messaging: 