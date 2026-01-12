#CWES #javascript #BlueTeaming #obfuscate #ReverseEngineering #base64 #hex 

- Code may be obfuscated to make it less understandable, deobfuscation can be useful if we need to understand malicious code etc.  

Can be useful to run js code in a standbox: https://jsconsole.com/  - checking that it does the same after deobfuscation. 


#### Tools to obfuscate:
- Minify: https://www.toptal.com/developers/javascript-minifier
- BeautifyTools: https://beautifytools.com/javascript-obfuscator.php 
- Obfuscator:  https://obfuscator.io/legacy-playground 

#### Tools to deobfuscate:
- Prettier.io: https://prettier.io/playground/
- beautify: https://beautifier.io/ 
- unpacker: https://matthewfl.com/unPacker.html

Can also be useful to know how to decode basic encodings:

**Base64**
```shell
echo aHR0cHM6Ly93d3cuaGFja3RoZWJveC5ldS8K | base64 -d
```

**Hex:**
```shell
echo https://www.hackthebox.eu/ | xxd -p

68747470733a2f2f7777772e6861636b746865626f782e65752f0a
```

```shell
echo 68747470733a2f2f7777772e6861636b746865626f782e65752f0a | xxd -p -r

https://www.hackthebox.eu/
```

Can use: https://www.boxentriq.com/code-breaking/cipher-identifier to identify encoding. 