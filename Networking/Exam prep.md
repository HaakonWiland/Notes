Next time: 
- Response time HTTP, presistent, non-persistent, multiple tcp etc. 
- ferdig kortsvar.

Transmission problems:
![[Pasted image 20250527120443.png]]
- Understand when the client receives the video blocks, and when he plays them.
- For each t, keep track of the above, then you are able to answer question about it. 


CRC:
- Line up D(with zeros) and G under
- XOR column wise 
- Always skip the first bit computed with XOR
- After each XOR, append the next bit to the part the gets XOR-ed
- If the leading bit is 1, XOR normally with G
- If the leading bit is 0, XOR with only zeros. 


Topics to read up on:
- Packet switching and circuit switching 
- P2P architecture - model for sharing data  
- Congestion control
- More on subnetting, have AI generate problems?
- Assignment 3, task 4, type of problems 
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

**V23 Part 2:**
**Q2: Flow control vs congestion control:**
1)
2)
See congestion control and flow control 

**Q3: Wireshark:**
1)
IP of host: 145.254.160.237
IP of server: 65.208.228.223

2)
IP header length: 20 bytes 
IP payload length: 48 - 20 = 28 bytes 

3)
Seq number of TCP syn segment: 0
Syn identifier: From the flags, which is set to 0x002

Port used for the TCP connection(server): 80
Port used for the TCP connection(host): 3372

4)
Seq number of TCP syn ack response from the server: 0
Note: The reason why both Syn ack from the server and the syn from the Host both have seq number = 0, is because the number is relative to its "owner"(Both have different seq to keep track of)

5)
How many bytes of data is carried by the TCP segment in packet 6:
?

**Q4: Forwarding and routing:**
1) interface 0
2) interface 1
3) interface 4
4) interface 2
5) interface 4

6)
- Forwarding happens inside the router/switch locally, and its purpose is to find the correct output for a given input. 
- The forwarding is decided based on a forwarding table.
- Implemented in the data plane 
- Fast process, just look in the table 

- Routing happens globally within the network, and its purpose it to find traveling paths for data being sent from source to destination. Often we talk about routing wanting to find the "best" path, with regards to shortest, fastest, or least costly path. 
- Implemented in the control plane 
- Slow process, much more complicated computation. 

**Q5: HTTP performance:**
1)
Lower bound for response time HTTP(non parallel http):
- Need round trip for both the TCP setup and the HTTP get request/response
- For 1 object: 300ms for TCP + 300ms for HTTP request/response 
- Then we need the time it takes to upload the object to the link: 200 000bits / 100 Mbps = 2ms per object 
- Total time for 1 object: 602ms 
- Total time for 6 objects: 3612ms = 3,612 sec. 

2)
lower bound for the reponse, but we can have many TCP connection(no parallell http):
- 1212ms 
- Look this up, hard to understand. 


3)
Lower bound for the reponse, but with persistent HTTP:
- Do only 1 RTT with TCP to open 6 connection 
- Transfer 1 object: 300ms + 2ms = 302ms 
- Transfer 6 object: 1812ms 
- Then add the 1 RTT for opening the TCP connection: 1812ms + 300ms = 2112ms 



**Q6: Firewall:**
1)firewall goals:
- Block unwanted incoming/outgoing traffic. Only authorized should be allowed to pass.  
- Firewalls itself should be immune to penetration
- Differ based on what network we are connected to: home, work, public 

2)stateless vs stateful packet filer:
- Stateless: has a fixed set of rules, which is applied to each packet in isolation. 
- stateful: has rules which can adapt in different situations, taking into account the current state and blocking traffic in the context of this state.  


#### S24 Exam:
Q1.1.1: c),  + a) and e): All are definitions that fit the nuts and bots of the internet. 
Q1.1.2: a) 
Q1.1.3: a) 
Q1.1.4: b) , d)
Q1.1.5: a) -> Packet size / transmission rate. Note Mbps = 1 000 000 bits/sec 
Q1.1.6: C) Encapsulation is: **Taking data from the layer above, adding header fields appropriate for this layer, and then placing the data in the payload field of the 'packet' for that layer.** Happens when sending data 

Decapsulation is: **Receiving a 'packet' from the layer below, extracting the payload field, and after some internal actions possibly delivering that payload to an upper layer protocol.** Happens when receiving data 


Q1.1.7: a) finding max throughput we need to look at the bottleneck, which is 25 Mbps
Q1.1.8: b) 365 microsec, remember a microsec is `1 * 10^(-6) seconds`
Q1.1.9: 200/300 = 0,67 link utilization. 


Q1.2.1: e) -> UDP is only best effort 
Q1.2.2:   b), c), f) -> TCP has Flow control, Congestion control, Loss-free data transfer 
Q1.2.3: a)
Q1.2.4: a) and d)
Q1.2.5: a), d) g) -> c), f), g): HTTP is mostly a client pull protocol(SMTP is not), newline is used to indicate end of request header in HTTP. 
Q1.2.6:  b), d), f), h): Remember udp is connection less, and that a udp server socket can receive datagrams from multiple clients and distinguish them by their source address, and is reliable.
Q1.2.7: a), c), e), g): remember TCP is connection oriented, uses an accept function to create a new socket for this connection 
Q1.2.8: c) Do bit addition, then take the compliment of the sum to find the checksum. Remember to carry the 1, if we get a 17'th bit. 
Q1.2.9: a), f) 


Q.1.3.1: a), c) 
Q.1.3.2: a), b) 
Q.1.3.3:
```
1 - A 
2 - B
3 - C
4 - D
5 - F
6 - G
7 - H 
```

Q.1.3.4: c), d), e) 
```
223.1.3/29, 32 - 29 = 3, so the subnet has 2^3 = 8 ip addresses, range from: 
223.1.3.0 - 223.1.3.7
.0 is the (sub)network address, and .7 is the broadcast. 
```

Q.1.3.5: a), e): IPv6 datagrams has 128bit addresses, and **Flow label field** in its header, which IPv4 does not. 
Q.1.3.6: d), a), b) 
Q.1.3.7: e)
Q.1.3.8: a) In the router, the destination IP address is looked up at the **input port of the router, in the forwarding plane**.  The switching fabric is responsible for moving the datagram to the output port. 
Q.1.3.9: d) 
```
A: 
source: 10.0.0.1,2020 Dest: 128.119.40.18, 80
B:
source: 138.76.29.7, 5051 Dest: 128.119.40.18, 80
C:
source: 128.119.40.18, 80 Dest: 128.119.40.186, 80
D:
**source: 128.119.40.186, 80 Dest: 10.0.0.1, 2020**
```

Q1.4.1: a), c), f), g), e) **Link layer services can benefit from having access to shared physical medium.**
Q1.4.2: a) , d) 

Q1.4.3: 
```
t=0.1, 1.4, 1.8, 3.2, 3.3, 4.1
- 1t to transfer 
- 0.2t in propagation delay
- If collision, move to after t=5

ok m1: 0.1 - 1.1, takes up: [0.1, 1.1 + 0.2=1.3]
ok m2: 1.4 - 2.4, take up: [1.4, 2.6]
m3: conflict with m2 -> Sent to after t=5
ok m4: 3.2 - 4.3
m5 wants to send at 3.3, at this time the channel looks idle(since 0.2 delay, channel looks un-idle at 3.4). m5 transmitts, and ruins it for m4. 
m6 conflict with m4

So: m1 , m2 the only successful. 
```

Q1.4.4: a), c), d), e)  
```
 t=0.1, 0.8, 1.35, 2.6, 3.9, 4.2
 - 1t to transfer 
 - 0.2t in propagation delay 
 - if collision, move to after t=5

ok m1: 0.1+0.2 - 1.1 + 0.2
m2: 0.8-1.8
ok m3: 1.35 - 2.35 + 0.2
ok m4 2.6 - 3.6 + 0.2
ok m5: 3.9 - 4.9 + 0.2
m6: collison with m5 
```

Q1.4.5: a), b), c): **2D-parity can detect both single and two bitflips, but can only correct single bitflips.**
Q1.4.6: c), d): 
Q1.4.7:
```
1) a: 0,5 
2) c: 2, since we have 2*1 message per slot 
3) b: 1, since we have 1 message per slot totally
4) d: 0,25, since we need two messages and two acks before the message arrives at target. 
```

Q1.4.8: b), c), d) 
Q1.4.9: d) 
Q1.4.10: a), b), c), d) 

**Exam V24:**
Q1.1.1: a), c) 
Q1.1.2: b) 
Q1.1.3: d) 
Q1.1.4: e)
Q1.1.5: e) -> Skulle blitt `P*(N+2)*L/R`

Q1.2.1: b) -> SMTP and IMAP is both a applicaton layer protocol. 
Q1.2.2: b), 
Q1.2.3: a) En socket hos per vert, og en socket hos vert D. 
Q1.2.4: d), e) -> SMTP bruker ikke UDP som underliggende protokol 
Q1.2.5: d) -> File is already on the server, pick max of (50sec, 1000sec), hence the answer is 1000.

**Ordinary questions:**
Q2: Flow control
TCP flow control stopper A fra å sende raskere data til B, enn B kan motta. A kan dermed max sende data til B med hastighet 600Mbps. Dette hindrer at sender gjør mottaksbufferen til mottakeren full.

Den faktiske flyten i TCP flow control kan beskrives slik:
- A sender data til B med sin orginal hastighet 1Gbps
- Bufferen til B blir på et tidspunkt full, og B kommuniserer dette til A, ved å si at receiving window er 0. 
- A stopper å sende data, men sender en ACK melding til B som bekrefter at A har fått med seg at receiving window er 0 
- Når A får et TCP segment fra B der receiving window er større enn 0, starter A igså å sende data til B. 

Q3: wireshark
3.1: Klient sendte pakke 112
3.2: server IP: 216.75.194.220, port 443
3.3: neste seq nummer, seq + len = 79 + 204 = 283
3.4: 3 record layers? 
3.5: Inneholder ikke master secret / encrypted master secret. 


Q4: IP addressing
4.1: For å finne et subnet, kan vi gjør bitwise AND mellom IP addressen og subnet masken. 
4.2: 192.168.1.108/30
4.3: 192.168.2.104/29
4.4: 192.168.3.96/28

Q5: Multiple Access Mechanism
5.1: Maximum rate: 2 messages / slot, on for each side, not collision.
5.2: Maximum rate: 1 message / slot, A and C alternate at sending messages
5.3.1: Maximum rate: 2/3 message / slot, Can transmit at the same time, but take a turn to ack the messages 
5.3.2: Maximum rate: 2/3 message / slot, C->D, Ack D-C + A->B, ack B-A

Q6: DNS
6.1: RR in DNS: (name, value, type, ttl) a record keeping track of IP addresses, the domain, type of record, and TTL. The type of record specifies what kind of service we are working with, ex. webserver, mail server, etc. 

6.2: When a client sends a DNS query: hostname, hostname alias, mailserver, Authoritative name server. 




**RAT 1:** (Fasit ikke langt ut enda)
![[Pasted image 20250528121410.png]]

1: 
Socket grensesnittet gir oss en standardisert og praktisk måte å lage applikasjoner som intragere med netverkslaget. Vi slipper å holde kontroll på detaljene som skjer på transportlaget(gjennom TCP eller UDP), selv om vi benytter oss av funksjonalitene deres i programmet vårt. Dette gir oss fleksibilitet til å utvilke kommunikasjonsprotokoller som passer våre behov, og applikasjonen kan sende og motta data via nettverket på en strukturert måte. 

2: Internettstandardene er utvilket av IETF, og standarddokumenter er på formen "RFC ****"

3: De vanligste typene bredbåndstilgang for boliger er Fiberoptikk, eller DSL.  

4: DSL(Digial Subscription Line) bruker samme teknologi som vanlige telefonlinjer for å gi internett tilgang (kobberkabler). Her benyttes en egen frekvens for å sende data, og en annen for å sende tale.  

5: Fiber til hjemmet (FTTH) er en teknologi der fiberoptisk kabel går hele veien fra leverandøren og direkte inn til brukerens bolig, uten å bruke kobberkabler i siste del. Dette gir svært høy hastighet, stabilitet, og lav forsinkelse sammenlignet med DSL eller kabelbasert internett. 

6: Hovedkategoriene for fysiske medier som brukes i datanettverk er: **Guided Media** - Kobberkabler og fiberoptiske kabler, og **Unguided media** - Radiobølger, mikrobølger og intrarødt. 

Kan tenke at Guided oppretter en tilkobling mellom 2 endepunkt, noe som gir mer kontroll og mindre støy.

Unguided sender signaler til alle enheter innen rekkevidde, og fungerer som en slags broadcast-kommunikasjon.  

7: I "Store and forward overføring" mottar en ruter/svitsj en hel datapakke, lagrer den midlertidig i minnet, og videresender den først når det en blitt verifisert(Checksum, etc.) og utgangsleneke er ledig. Dette kan gi noe økt forsinkelse. 

Alternativet er: **Cut-through switching**, en metode som bare leser de første bytene av en pakke før den starter å videresende. Dette gir lavere forsinkelser, men høyere risiko for korrupte pakker. 

8: Ruting i internett kan sammenliknes med å kjør bil i den forstand at, ruting leter etter å levere trafikk fra sender til mottaker på best mulig vis. På samme måte er vi ut etter å finne best mulig rute fra A til B når vi kjører bil, basert på skilter og trafikk. 

9: Linjesvitsjing: 
- Fast kommunikasjonslinke mellom 2 endrepunkter 
- Gir en bredbåndsgaranti 
- Den totale mengden bredbånd fordeles likt mellom flere noder 
- I tillfeller hvor flere forbindelser fra svisjen ikke benyttes, resulterer linjesvjing i at deler av bredbånded ikke blir utnyttet 

pakkesvitsjing:
- Gir ikke bredbåndsgaranti på samme måte som linjesvitsjing 
- Dataen deles inn i pakker som sendes(forwardes) uavhengig hver for seg 
- Hvis det kun skal sendes data til en node, kan pakkesvitsjing utnytte hele bredbånded, noe som gir raske overføringer av data enn ved linjesvitsjing.

10:
Internett-protokollstakken stakken inkluderer: Application, transport, network, link.

note: i TCP/IP modellen har vi 4 lag, og tenker at det fysiske laget er implisitt inkludert i linklaget. 



**RAT 2:**

1:
"Longest prefix matching" ser på destinasjons headeren til en pakke for å finne ut hvordan interface den skal sendes forwardes til. Destinasjonsaddressen sammenliknes med subnett-prefikser i rutingtabellene, og pakken sendes til det interfacet hvor destinasjonsaddresse samsvarer best med prefiksene(flest antall matchende bits fra venstre). Dette er nøyaktig og reduserer forsinkeler i forwarding prosessen. 

2:
For å beskrive "Head of line blocking" kan vi tenke oss en ruter med input-køer og output-køet. Innen en gitt input-kø, skal den fremste pakken sendes til en output-kø som er full, derfor må den vente før den kan til videresendt. La oss nå si at den nest framste pakken i denne input-køen, skal til en annen output kø, som ikke er full, altså den kan videresendes nå. Men siden den første pakken i input-køen må vente, må også den neste pakken vente, da sier vi at pakke 2 er utsatt for head of line blocking. Dette høyere forsinkelser. 

3:
FIFO - First in first out: Den første pakken som kom inn, er den først som skal sendes ut. 

prioritetskø: Vi predefinerer at enkelte pakker skal prioriteres å sendes først, selv om de ikke kom inn først. Prioriteringer kan være gjort på bakgrunn av pakketype, pakkestørrelse etc.

RR - Round Robin: Sorterer pakkene i ulike kategorier, og rullerer mellom disse kategoriene, og sender jevning litt av hver kategori.

WFQ - Weighted Fair Queuing: RR bare med vekting av de ulike kategoriene, noen kategorier kan være av høyere prioritet enn andre, dermed send mer av disse. 

4: Kø mekanismer i rutere
**Drop-tail:** Hvis køen er full, drop den siste pakken. Dette fører til global synkronisering, der flerer sendere reduserer hastigheten samtidig. 

AQM-algo: 
ex: Random Early Detection:  Droppe/merke pakker før køen blir full. Signaliserer overbelasting til senderne tidlig.

**Logikk:** Jo lengere køen blir, jo større sannsynlighet for at en tilfeldig pakke blir droppet. 

**CoDEL:** Droppe pakker basert på hvor lange de har ligget i kø. 

5:
TTL i IPv4: Hvor mange ruter-hopp kan en pakke gjøre før den droppes. Tallet blir reduseres med 1 for hvert hopp, hvis TTL er null, droppes pakken. Når pakken droppes sendes en ICMP feilmelding tilbake til avsenderen. Dette gjør det slik at pakker ikke sendes gjennom nettet i det uendelige. 

"Hop Limit" i IPv6: Samme som TTL i IPv4 

6: DHCP protokol

DISCOVER:
Host ønsker å komme på nettet, sender en melding med MAC addressen sin til DHCP serveren. Denne meldingen må kringkastes til hele netverket, siden hosten ikke kjenenr IP konfigurasjonen på forhand, og da ikke vet hvem som er serveren.

OFFER:
DHCP serveren tilbyr hosten en IP addresse

REQUEST:
Hosten ønsker å takke ja til dette tilbudet, sender respons til server. 

ACK:
Serveren bekrefter at den nye hosten nå har "denne" IP addressen. 

7:
IPv4: 
- src og dest addresser er 32-bit
- kan fragmenteres
- Har mange headere som checksum, internet header length, Fragmentering field 

IPv6: 
- src og dest addresser er 128-bit
- kan ikke fragmenteres hos ruteren(MEN kan fragmenteres hos avsender)
- Har ikke de samme headerne som IPv4, men har eksempelvis **flow label.**

**Fragmentering i IPv6 ble fjernet for å forbedre ytelse og redusere kompleksiteten** hos rutere, og legger ansvaret over på avsender for å bestemme pakkens størrelse.

8:
NAT gjør at vi kan behandle lokale IP addresser, som **EN** offentlig IP addresse. Dermed kan vi ha flere enheter innen et lokalt netverk, men fra utsiden vil alle enhetene ses på som samme ip addresse. 

NAT løser problem med at IPv4 addresser kun størrer `2**32` unike addresser, noe som er for lite hvis man tenker på alle enhetene i verden som trenger IP addresser. 

Kritikk mot NAT er f.eks: 
- Vanskelig for eksterne enheter å initiere forbindelser inn til en enhet bak NAT. 

**RAT 3:**

1:
Kablede kommunikasjonsnettverk er raskere, og mer stabilt enn tråsløst. 

Tråløs kommunikasjon er sårbare for interference(andre enheter og fysiske hindringer), varierende signalstyrke, noe som fører til større sannsynlighet for tap av pakker og feile pakker. Det er også vanskligere å koordinere tilgangen til mediene, fordi signalene er delt mellom flere enhter uten fysisk avgrensning. 

2:
Passiv scanning: Fange opp gjennomgående trafikk, uten å påvirke trafikken. 

Aktiv scanning: Kan sende egen trafikk for å undersøke enhentene på netverket. "Probe requests" sendes til  alle kanaler, og tilgangspunkter svarer med "probe responses."

3: Wifi benytter ikke Kollisjonsdeteksjon på samme måte som Ethernet. Den bruker isteden Collision Avoidance. Grunnen til dette er hovedsaklig:
- En trådløs sender kan ikke lytte mens den sender, så den kan ikke "høre" om det har oppstå kollisjon.
- Pga. variasjon i signalstyrke og rekkevidde kan en enhet ikke alltid oppdage at andre sender. Dette fører til at kollisjonsdeteksjon er upraktisk med trådløse miljøer. 

4: 
RTS: Sender spør mottaker om den har muligheten til å motta trafikk.

CTS: Mottaker svarer sender med en CTS, som stopper andre potensielle sendere fra å sende trafikk samtidig som senderen. 

Dette gir en strukturert måte å sende trafikk i 802.11, og reduserer sjansen for kollisjoner. Sjulte terminaler kan eksempelvis oppstå i følgende situasjoner:

A <-> B <-> C

A, C kommuniserer gjennom B, men hvis begge sender trafikk samtidig vil ikke B kunne videresende trafikken riktig. Med bruk av RTS og CTS, sørger vi for at B kun mottar trafikk fra 1 sender om gangen. 

5:
**Authentication header:** Gir Integritet og authentisering av IP pakken, men ikke konfidensialitet. 

**Encapsulation Security Payload:** Gir integritet, authentisering og konfidentialitet. Dermed brukes dette er ved VPN-er, siden den beskytter dataene fullt ut, og kan brukes i både transport og tunellmodus. 

Note: 
- IPsec transport mode: Payloaden er beskyttet, mens IP-headers forblir synlige. Dette gir dårligere sikkerhet, men bedre ytelse. 
- IPsec tunnel mode: Hele IP pakken er beskyttet. 

6:
Sym: Her bruker vi samme nøkkel for å kryptere som å dekryptere.

Eksempler er: AES og DES

Asym: Her bruker vi 1 nøkkel for å kryptere og 1 nøkkel for å dekryptere. 

Eksemper er: RSA og ElGamal

7: 
Malware: "Malicious software" er programmer som har intersjon om å skape trøbbel, som for eksempel kryptere alle filene dine(Ransomware), eller ta remote kontroll over enheten din(Remote access trojan).  

Tjenestenekt angrep: "DOS", et angrep som har intensjon om å stoppe en tjeneste. Kan eksemelvis gjøres gjennom å sende store mengder data til en server, slik at den ikke klarer å hånte all dataen.

Sniffing: Lytte på trafikk, ofte hvis det er usikker kommunikasjon eller svar kryptografi.  