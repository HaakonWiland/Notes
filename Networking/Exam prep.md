Topics to read up on:
- Packet switching and circuit switching 
- P2P architecture - model for sharing data  
#### V23 exam:
- Q1.1.1: a), **c)** -> circuit switching CAN guarantee broadband! 
- Q1.1.2: b) -> Question is on the bottleneck on flow (gjennomstrømming), which is the slowest of link: min(R1, R2)
- Q1.1.3: d) siden 100 kbps = 100 000 bits per sec, and we want to transfer 1 000 000 bytes = 8 000 000 bits 
- Q1.1.4: c)  transmission rate 10 Mbps = 10 000 000 bits per sec. packet size 1000 bytes = 8000 bits-> Transmission per packet = 8000/10 000 000 = 0.0008 bits per sec. Since we have chaining effect when doing packet switching, with pipelining, the total time of the sending the 1000 packets is: T = `0.0008 * 3 + 999*0.0008 = 0.0024 + 0.7992 = 0.8016` But for some reason the textbook do not count the last link, so the answer 0.8008. 
- Q1.1.5: c) 

- Q1.2.1: b), c), e) -> DNS, SMTP and FTP all on application layer. 
- Q1.2.2: b)
- Q1.2.3: c) 125 Mb; upload 100Mbps => 1,25 sec to upload. distribute to 100 peers, each peer has download 10 Mbps => each peer use 12,5 sec to download file.  Least distribute time: 12,5 sec. -> No d) = 100 sec is correct. 
- Q1.2.4: e) no alternative is correct. seq number should be 248+48+64=360, but no option has this. -> bad formulation in the question: answer is (297,502,80), note seq number: 248 + 1 + 48 = 297 (Remember to count the ACK byte). 
- Q1.2.5: a), b), c), d) -> In non persistent connections, we only allow 1 http request per tcp connection. 

- Q1.3.1:  c), 8 + 16 + 64 + 128 = 216
- Q1.3.2: b), c): `214.97.254/23` => 512 size subnet (510), range from: `214.97.254.000 - 214.97.255.255`, `Subnet with 250 addresses: 214.97.255/24 and 214.97.254/24`
- Q1.3.3: a), b), d) 
- Q1.3.4: a), b), d), e) -> IPv6 does not have checksum field, and BOTH IPv4 and IPv6 are connectionless.  
- Q1.3.5: c)  - arrives at t=2, leaves when t=5, delay of 2. (t=3, t=4)

- Q1.4.1: a) 
- Q1.4.2: d), h) 
- Q1.4.3: a), c) -> Given a SNR, a modulation technique witch HIGHER bit transfer has HIGHER BER. Also, adaptive modulation and coding is uses in: Wifi, 4G, and 5G. 
- Q1.4.4:  a), c), d), e) -> 802.11(WIFI) uses CSMA/CA, ACK, RTS/CTS, DIFS.  802.3(Ethernet) uses CSMA/CD
- Q1.4.5: a), c) 

- Q1.5.1: a), b), c), d) 
- Q1.5.2: c) 
- Q1.5.3: b), c), d) 
- Q1.5.4: d) -> We are interested if there is a period where the client renders the video faster then he receives them, and hence gets lag. In this case he will get lag on block 7 since, he receives this block at t1 + 9delta, but is trying to view it at t1+7delta.  
- Q1.5.5: d) -> Main reason we prefer video streaming over HTTP over UDP, is because a lot of firewalls block UDP traffic, but not http.   

