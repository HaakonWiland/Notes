
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

![[Pasted image 20240429113433.png]]
