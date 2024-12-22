
----------------------------------------------------------------------
Tags:
- #Cryptography 
- #TTM4135
-----------------------------------------------------------------------

#### 2021 Spring Exam:
![[Pasted image 20240425084134.png]]

a) For the one time pad, we require the key to be truly random, and the message to be as long as the message encrypted. The message is then XOR'ed with the message, bit by bit. The same key can be used for decrypting as well. If the key is only used once, we say that the one time pad provides perfect secrecy: An attacker cannot derive any information about the plaintext from the ciphertext. 
AES-128 is considers currently generally considered secure, however with ECB there exists weaknesses. This is because **ECB each block is encrypted with the same key independently**, which result in identical plaintext-blocks would be encrypted into the same ciphertext. Because of this we say ECB lack "diffusion", the property where one bit of plaintext affects many bits in the ciphertext, and hence its vulnerable to **ciphertext only attacks.**  
**Conclusion:** One time pad would provide better security in this situation. 

b) As mentioned in a) the one time pad is by design set to only use each key once. When we use the same key for the second message we **lose the perfect secrecy trait**. An attacker can for instance get a hold of the two ciphertexts, and try XORing them with each other in hopes of getting information about the plaintexts. Its also possible to apply statistical attacks such as frequency analysis to try getting an understanding of the statistical properties of the plaintext. 
For the AES-128-ECB the security would also be reduced by reusing the key. Since we encrypt each block independently, patterns can be identified if an attacker has access to multiple ciphertexts that has been encrypted with the same key.     
 **Conclusion:** While reusing the key for multiple messages with AES-ECB would reduce its security property, it would not break the encryption scheme in the same way it does for the one time pad.


**Worksheet 4.**
![[Pasted image 20240429110607.png]]
**Answer:** 
Brute force AES128 key requires roughly 2^128 computations. Due to the birthday paradox, we need around 2^128 computations to find a collision for a SHA256 hash. 



![[Pasted image 20240505123114.png]]

a): 
Chosen plaintext: The attacker can find the corresponding ciphertext from some chosen plaintext the attacker decides. (Adversary can encrypt chosen messages)
Chosen ciphertext: The attacker have collected some ciphertext and have managed to find their corresponding plaintext.  (Adversary can encrypt and decrypt chosen messages)

b):
The key matrix is of size 2x2, and each C, P is a vector of size 2. So we can combine two ciphertexts , plaintext pairs, and solve for K in the equation: C1C2 = K P1P2, where we consider them a 2x2 matrix. This gives us: C1C2 (P1P2)^-1 = K. So we only need 2 chosen plaintext pairs to obtain the key.

c): Chosen ciphertext attack will also work. Choose any C, and find corresponding P, then we find K = C P ^-1 


![[Pasted image 20240505125839.png]]

a): 
The IV (initialization vector) is a nonce, e.i. number only used once, and is not supposed to be used multiple times. The reason we only use it once is because we want equal plaintext to be encrypted to different ciphertext. When we use the IV for every message, it loses this property. An attacker can for instance obverse patterns in the ciphertext, which is a security violation. It is also vulnerable to dictionary attacks. 

b):
If an attacker knows what the IV is, he can for himself choose a plaintext with P1 as the first block, and find its corresponding ciphertext C. Since the IV is the same each time, he can compare his calculated C's C1, against the captured ciphertext C*'s C* 1.  if C1 = C* 1 , then the attacker knows the first block A sent to B is equal to P1. 

![[Pasted image 20240505132120.png]]

b):
Whole security of RSA lies in the fact that n is hard to factorize, and hence an attacker cannot find phi n and correspondingly not able to find the private key. Since they have shared modulo n, both A,B knows phi(n). So A can find dB by finding the inverse of eB mod phi(n). eB * dB = 1 (mod phi n)
Q: How to actually factorize n? Solution says we can use Millers Algorithm? 

![[Pasted image 20240505140140.png]]
a):
TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 :=
Elliptic curve D.H exchange ephemeral, elliptic curve digital signature algo, using AES128 symmetric encryption with galois counter mode and SHA256 hash.

TLS_RSA_WITH_3DES_EDE_CBC_SHA :=
RSA for key exchange and digital signature(auth) with tipple DES(Data encryption standard) symmetric encryption EDE(encrypt decrypt encrypt), and Cipher block chaining mode using basic SHA, (SHA-1).

First one provide forward secrecy since its using key exchange with ephemeral, e.i. we are using keys only within one session. 

b) Confidentiality is gained by the symmetric encryption used in the ciphersuits, hence we are comparing AES128 GCM and 3DES CBC.

AES128 GCM: 
128 bit key strength 
GCM provide data integrity  
Data integrity from SHA 256 -> Key derivation . 
Having forward security from EC DH ephemeral provide better confidentiality  

3DES CBC:
3 * 56bit key length = 168 bits -> because of MITM attacks, the effective key strength is not 168 bits, the attack require 2^112 steps, equivalent to a key with strength 112 bits 
Slower algorithm 
3DES CBC has no build in way to provide data integrity  
Key derivation from SHA1 hash -> Not as robust as SHA 256, SHA1 has been broken and should not be used  
Not forward secure when using RSA for key exchange, compromise the key once, and all past communication can be decrypted. 
