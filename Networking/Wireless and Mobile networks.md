#TTM4100 


Main challenges:
- Wireless: Communication over wireless link 
- Mobility: Handling the mobile user who changes point of attachment to network

Note: Wireless does not always mean mobility. 

**Elements of a wireless network:**
- **Wireless host:** Mobile, latop etc. 
- **Base station:** Typically connected to wired network, replay - responsible for sending packets between wired network and wireless hosts in its area
- **Wireless link:** Used to connect mobiles to base stations, multiple access protocol coordinates link access

- **Infrastructure mode:** Base station connects mobiles into wired networks. 
- **Ad hoc mode:** No base stations, nodes can only transmit to over nodes within link coverage. Nodes route among themselves. 


#### Characteristics of selected wireless links:
![[Pasted image 20250320082921.png]]

Note: Network taxonomy not impotent for exam 

**SNR: Signal to noise ratio:**
- Large SNR - easier to extract signal from noise (GOOD)
- When SNR increase: Receiver can more accurately interpret the transmitted bits, so BER(Bit error rate) reduces, leading to more reliable data transmission. 

**Modulation scheme:**
- A method used to encode information onto a carrier signal for transmission. It determines how digital/analog data is mapped onto changes in the amplitude, frequency or phase of signal. 

Trivia:
- **For a given modulation scheme; a lower SNR => higher BER**. (Lower SNR means signals are more affected by noise, increasing likelihood of bit errors)
- **For given SNR, a modulation scheme with a higher bit transmission rate has higher BER.** ( Transmitting more bits per symbol, make it more susceptible to noise and leading to higher BER)



**Hidden terminal problem:**
![[Pasted image 20250320085035.png]]
- Can cause a collision at B, since A and C are unaware of what is happening at B -> Both A and C might happen to transmit at the same time, causing a collision. 


**RTS/CTS:**
- RTS/CTS frames is used in WiFi (802.11)
- Used to reduce collisions and deal with the hidden terminal problem 
1. **Sender** sends an **RTS** frame to the intended receiver.
2. **Receiver** responds with a **CTS** frame if the medium is clear.
3. **All nearby nodes** that hear the CTS know that they **must stay silent** for the upcoming transmission.
4. **Sender** then sends its actual data.



**Signal attenuation:**
![[Pasted image 20250320085210.png]]

#### Differences from wired link:
- **Decreased signal strength**: radio signal attenuates as it propagates through matter 
- **Interference from other sources:** Wireless network frequencies shared by many devices: interference 
- **Multipath propagation:** Radio signal reflects off objects ground, arriving at destination at slightly different times. 


#### WiFi: 802.11 Wireless LAN:
![[Pasted image 20250320085300.png]]

Tips: If home wifi is slow, consider checking out the frequency, and changing it to a less populated freq. 

**Elements of WiFi:**
- Wireless hosts
- Access point(AP): same as base stations 


**Channels and association:**
- AP admin chooses frequency for the access point. It is possible to have channels chosen at same frequency by neighboring access point, which could lead to problems.
- Arriving host must associate with an AP: 
1. Scans for channels, listening for a beacon frame with APs name(SSID) and MAC address.
2. Host selets the AP to associate with 
3. May preform authentication
4. Run DHCP to get IP address in AP subnet. 

**Passive and Active scanning:**
![[Pasted image 20250320091248.png]]


##### 802.11 MAC Protocol: CSMA/CA:
Goal: avoid collisions.

![[Pasted image 20250320101748.png]]


**Alternative way to avoid collisions:**
Idea: Sender "reserves" channel use for data frames using small reservation packets 
1. Sender first transmits request to send(RTS) packet to BS using CSMA(RTSs may collide but they are short)
2. BS broadcasts clear to send (CTS) in response to RTS
3. CTS heard by all nodes 

![[Pasted image 20250320102315.png]]


**802.11 frame: addressing:**
![[Pasted image 20250320092655.png]]

**Rate adaptation:**
- Base stations dunamically change transmission rate(physical layer technique) as mobile moves.
- This is to balance SNR and BER. 

**Power management:**
- Node-to-ap: "i am going to sleep until next beacon frame", saves power not listening for new beacons frames 
- Beacon frame: Contains list of mobiles with AP-to-mobile frames waiting to be sent. 

#### Cellular networks: 4G and 5G:

![[Pasted image 20250320093900.png]]

**Elements of 4G LTE architecture:**
- **Mobile device:** Smartphone, tablet, laptop, IoT etc. 
- **Base stations:** Similar to AP in WIFI, but also adds functionalities for mobility 
- **Home Subscriber Service:** Stores info about mobile devices for which the HSS network is their "home network"
- **Serving Gateway(SGW), PDN Gateway (PGW):** Lie on the path from mobile to/from internet. 
- **Mobility Management Entity:** Device authentication coordinated with mobile home network HSS. Tracking/paging device location 

![[Pasted image 20250320103549.png]]


##### LTE data plane protocol stack:

**First hop:**

**Packet core:**

**Associating with a Base station(BS):**

##### Global cellular network: a network of IP networks:


##### 5G:
Goal:
- 10x increase peak bitrate
- 10x decrease in latency 
- 100x increase in traffic capacity over 4G 
- 5G has higher frequency bands, then those used by 4G.

