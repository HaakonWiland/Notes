#Privacy
Commands using GPG for PGP:

```
Generate key:
gpg --gen-key 

Check collected keys:
gpg --list-keys

Upload your key to keyserver (example):
gpg --export olanordm@stud.ntnu.no | curl -T - https://keys.openpgp.org

Signing messages:
gpg --output message.sig --sign message

Note: Can use --clearsign or --detach-sign also.

Verifying message:
gpg --verify message.sig

Encrypt and sign message:
gpg --output message.gpg --sign --encrypt --recipient \ olanordm@stud.ntnu.no message

Decrypt and verify encrypted message:
gpg --output message --decrypt message.gpg

Note: To decrypt messages, you need to have the private key corresponding to the public key being used to encrypt the message. 

Generate revoking certificate (used to declare that our keypair is no longer in use. Should be done if our private key gets leeked):
gpg --output revoke.asc --gen-revoke oladnorm@stud.ntnu.no

Applying the revoking cert:
gpg --import revoke.asc

Declare that other that the keypair is no longer valid:
gpg --send-key --keyserver keys.openpgp.org \
<your_key_ID>
```

For more practical application, one could use extensions of email clients, ref Enigmail. 

Note: PGP does not protect metadata.

- login to ubuntu server:  
```
ssh ubuntu@<ip-address> -i haakwil.pem
```

