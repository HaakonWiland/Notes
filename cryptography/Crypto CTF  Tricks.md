
Convert hex-string to number we can do calculations with:
```
c = "5ad94ceba849d..."
c = int(c, 16)
```

Cryptosystems like AES in Cryptodome.Cipher require the parameters to be in bytes rather then Hex, long, etc.. We can use binascii to convert from hex to bytes:

```
import binascii

iv_raw = 'b060263b0723a7173c8aa02cf3b19600'
iv = binascii.unhexlify(iv_raw)

ELLER:
iv = bytes.fromhex(iv_raw)

```

Shared secrets in D.H are usually hashed to derive the key (since keys are usually of standardized length). Be alert to what hash function we are using and how many bits/bytes the key is. 

19.09.24
- When doing the Haastad attack on RSA, sometimes we cannot do it on a arbitrary pairs, since some pairs result in us getting a non-perfect cube for m^3. If we bigger set of ciphertext, moduli, we should try all combinations of 'e' length. Then there is number is 
```
(ciphertext/moduli pairs) C (e) possible combinations to try    
```



Sagemath tricks:
```
Generate finite field: F = GF(p)
Finding generator of finite field: F.primitive_element()
Modulo with exponent: power_mod(g,a,p)
Compute x such that g^x = y: log(F(y), F(g))  

```

Useful cryptodome function:
```
Finding modular inverse: numbers.inverse(x, N) 
```
