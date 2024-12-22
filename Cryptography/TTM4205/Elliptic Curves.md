
![[Pasted image 20240909123220.png]]

- Group of points on a elliptic curve forms an abelian group.
- {**O**}: Refers to "point of infinity" or "ideal point", which acts a the identity element of the group of points on the elliptic curve. 

Using the elliptic curve we can define a problem similar to the discrete log problem:
#### ECDLP:
![[Pasted image 20240909123926.png]]


We can use this to make a signature scheme: 
#### ECDSA (signing)
![[Pasted image 20240909124246.png]]

 - G is a "generator point" on the elliptic curve 
 - n is the order of G; nG = **O** = id 
 - k is picked random, and need to be kept secret and be unique for each signature. 
 - Pair (r,s) is the key. 
 
#### ECDSA (Verification)
![[Pasted image 20240909124323.png]]


#### Breaking ECDSA:

If we **reused the random value k** on two signatures:
![[Pasted image 20240909130759.png]]

What if we cannot exploit this weakness: -> Break using Lattices:

![[Pasted image 20240909131108.png]]

![[Pasted image 20240909131237.png]]

