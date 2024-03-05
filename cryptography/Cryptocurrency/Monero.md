
Source: https://www.getmonero.org/library/Zero-to-Monero-2-0-0.pdf

What kind of cryptocurrency is Monero: 
**Standard one-dimensional distributed acyclic graph** cryptocurrency blockchain where transactions are based on **elliptic curve cryptography using curve Ed25519**, transaction inputs are signed with **Schnorr-style multilayered linkable spontaneous anonymous** **group signatures**, and output amounts are concealed with **Pedersen commitments** and proven in a legitimate range with **Bulletproofs.** 

So what the freak do that mean? Lets find out.


Background:

Digital currency to exit we need:
- Users to believe its supply is strictly limited.
- Money recipient must trust that the money send to them is not counterfeit coins. 
- Since we do not want a third party central authority we need: the currency supply and complete transaction history to be publicly verifiable. 

These needs can introduce problems such as:
- Lack of privacy, anyone can look at the ledger(records) and found out **when**, **who** and  **how** much money was send to **whom**. With Bitcoin, one could obfuscate transactions using temporary intermediate addresses, but this can apparently be worked around.  Monero attemt to work around this problem by storing only single-use addresses for receipt of funds in the blockchain, and authenticating transactions with **ring signatures.**

Mathematics:
Elliptic curves: 
Given a pair (a,b), is the elliptic curve defined as the set of all points with coordinates (x,y) satisfuing a Wierstrass equation: 

y^2 = x^3 + ax + b where a, b, x, y ∈ Fq

Monero used a spescial curve from a category called Twisted Edwards curves:
ax^2 + y^2 = 1 + dx^2y^2 where a, d, x, y ∈ Fq

