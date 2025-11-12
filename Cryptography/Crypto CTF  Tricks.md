#cryptography #ctf


#### Conversion tricks:
**str to byte-str:**
`byte_str = bytes(string, 'utf-8')`

**Hex-str to int:**
`number = int(hex_str, 16)`
- NOTE: We can use the XOR-operator(^) on int.

**Int to byte-str:**
```
length = max(1, (flag_int.bit_length() + 7) // 8)
flag = flag_int.to_bytes(length, "big")
```

**hex-str to byte-str + xor of byte-str:**
```
key = bytes.fromhex(string1)
secret = bytes.fromhex(string2)

flag = bytes(a ^ b for a,b in zip(key,secret))
```




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

mod

Sagemath tricks:
```
Generate finite field: F = GF(p)
Finding generator of finite field: F.primitive_element()
Modulo with exponent: power_mod(g,a,p)
Compute x such that g^x = y: log(F(y), F(g))  
CRT: crt([ci ... ], [n1 ....])

```

Useful cryptodome function:
```
Finding modular inverse: numbers.inverse(x, N) 
```


25.09.24

Inverse of padding in RSA: 
The message m is padded with 57 nullbytes, padding with a nullbyte is the same as multiplying the numerical representation of m by 2^8.  We can represent the ciphertext with this equation.

![[Pasted image 20240925162413.png]]

Then we just have to calculate the inverse of p^e, then we find a value for m^e. 

![[Pasted image 20240925162143.png]]

