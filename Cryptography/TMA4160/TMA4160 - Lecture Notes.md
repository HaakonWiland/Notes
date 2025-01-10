Teacher: Kristian Gjørseen 
#Cryptography #TMA4160 


### Terminology:
- **Secure Key exchange:** A key exchange protocol is secure if someone who sees the protocol messages but does not actually participate in the protocol, cannot learn the agreed-upon shared secret.






#### 19.08.24 
#Diffie-Hellman

Amount of data being sent can reveal the message, but cryptography do not solve that problem, this in an engineering problem. 

![[Pasted image 20240819124931.png]]


Consider Diffie-Hellman, using cyclic group G, with generator g and a random secret a. Then we have:

**Theorem:** There exit an algorithm that can compute g^a in G using **at most 2log(a)** group operations.

![[Pasted image 20240819125451.png]]

(This is much faster then doing a group operation a times on g.)

