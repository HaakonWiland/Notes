#TOR #darkweb #osint

### Darkweb workshop hackcon 2026

**Missing tools that might be useful:**
- TamoSoft Essential Net Tools -> collection of lots of network tools + email verifier 

#### Tools:
- Checking phone numbers: https://www.freecarrierlookup.com/ 
- Checking ip addresses with more: https://browserleaks.com/
- Checking pgp fingerprints: https://keyserver.ubuntu.com 
- pgp public blocks(can find usernames, emails associated with the key): https://cirw.in/gpg-decoder/ 
- Checking out emails: https://epieos.com/ 
- Checkout usernames and pgp stuff: https://keybase.io/ 
- Checkout usernames: https://whatsmyname.app/ 
- Checkout domain: https://mxtoolbox.com/ 
#### I2P
On most systems, I2P can be started with:
/home/kali/Binaries/l2p/i2prouter start

If I2P does not start, please try:
/home/kali/Binaries/l2p/runplain.sh
#### Darknets
- tor
- i2p: Have to configure you outproxy yourself 
- freenet: Lagrange browser etc 
- zeronet? - 8chan?
- lokinet - currently down. 
- IPFS: Just for file sharing, can be used via an extension in Brave
#### Random 
- Blockchain domains, web3, never have to pay rent on you domain. 
- Tor sites should be hosted with site relay, note you can check shodan.io for .onion and you can find sites that are misconfigured.  
- Can browse tor via the tor executable, and proxy a browser via it. -> check.torproject.org to verify if it works (browser config + proxybridge + tor)
- Can install extensions to read and translate Russian in other browsers then the onion browser, hence can be useful to be able to route tor via another browser 
- Telegram secret chat - cannot take screenshot, but with can with phone emulator. 

#### Find stuff 
- tor.taxi
- https://browserleaks.com/ 
- torch
- deepdotweb
- darknetstore12
- tor.watch
- darkeye
- darknettrust: http://dntrustmuq5ccf3lygrnhsprpdliakq7r2ljsspczmdsslj5wl4teeid.onion 
- keyserver to check fingerprints: https://keyserver.ubuntu.com/
- fresh onions 
- ahmia
- Russion darkweb sites: http://godnotabawurdtje5hemj2i2latgl2bp6lcycqad4edikacgggjvluid.onion/sites/runion/ 
- Telegram channels 
#### Cryptocurrency:
- UTX0: BTC
- ERC20: eth, smartcontracts 

- Bitcoin - never in a wallet, only associated with the wallet

Ways to hide with BTC: 
- mixing/tumbling -> chip mixer 
- chainhopping/morphtoken 
- Some sites sells wallets with BTC -> Importent to check out the amount in the wallet 

- Bad guys cash out on "normal" crypto marketplaces such as binance. 
Quick btc address: https://www.bitaddress.org
- Can lookup wallets at arkm
- Can use tracker.app to check out traffic between wallets , or breadcrumbs.app 
- For monero: xmrchain.net, can checkout transaction hash, wallets 

- Monero users, usually only share sub-wallets to recive funds.

Working with pgp keys: https://cirw.in/gpg-decoder/
- Possible get metadata from the pgp key: email, date of creation etc. 
- Can use lolarchiver.com to check if the email is valid, it can be related to a real person. 

Monero:
- Not sold many places, often have to swap 
- signing transaction, always signed by 15 other decoy users 

**Investegating sites:**
- Check out their crypto addresses -> They usually have a donation address. 
- Can lookup the email domains via mxtoolbox.com
- pgp stuff: https://keybase.io/ 

#### Yggdrasil
https://publicpeers.neilalexander.dev/ 
- Peer can see you ipv4

Running on linux:
```shell
sudo yggdrasil -useconffile /etc/yggdrasil/yggdrasil.conf
```

#### Telegram exploit:
- Stunmon -> Unpatchable, can also work for signal and session. 
- Can get ip of any user which they videocall with. 



