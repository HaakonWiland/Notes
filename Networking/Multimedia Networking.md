#TTM4100 

#### Audio 
- Need to transform something analog to digital(digitization)
- Analog auto signal is sampled at constant rate(Ex. CD 44100 samples/sec) ![[Pasted image 20250403085231.png]]
- Example: 8000 samples/sec, 256 quantized values(Range of possible digital values) => 64 000 bits per sec  

#### Video:
- Video as a sequence of digital images 
- Digital image: Array of pixels, each pixel represented by bits 

Coding within video is used to decrease number of bits used to encode images. Different ways of coding video:
- **Spatial coding (Within image):** "instead of sending N values of same color (all purple), send only two values: color value (purple) and number of repeated values (N)"
- **Temporal coding example (From one image to the next):** "instead of sending complete frame at i+1, send only differences from frame i"

Sending video:
- CBR(Constant bit rate): Video encoding rate fixed 
- VBR(Variable bit rate): Video encoding rate changes as amount of spatial,temporal coding changes 

Different ways to deliver video:
**Streaming, stored audio, video:** 
- Can start watching before whole file is downloaded with streaming 
- Can transmit faster then auto/video will render(A buffer will be stored at the client)
- Challenges if clients pause, fast forward, rewind, jump the video 
- Packets may be lost when transmitted(streaming most likely over UDP)
- In general we want the buffer to have a size such that it does not "run empty". Since, usually transmitting rate is faster then rendering rate, thus eventually the buffer empties 

**Conversational -  Voice/video over IP:**
- Facetime, skype etc.
- Limited by delay tolerance 

**Streaming live, audio, video:**
- Streaming of live sport event 

#### Voice over IP(VoIP)
Exam relevant type of questions: Why is X sensitive for delay, but not loss

- Network loss: IP datagram lost due to network congestion 
- Delay loss: IP datagram arrives too late for payout at receiver 

VoIP is more sensitive to delay then loss because of the conversational aspect (taking turns talking). Still, a high loss rate >10% will become a problem 


**Adaptive playout delay:**
Goal: Low playout delay, low late loss rate 
- Adjust playout delay at beginning of each talk spurt 
- Silent periods compressed and elongated 

**Recovery from packet loss:**
Challenge: recover from packet loss given small tolerable delay between original transmission and playout.

Forward error Correlation (FEC):
Possible solution: Send enough bits to allow recovery without transmission. 

#### RTP(Real time protocol):
- Transport layer protocol 
- Extends functionalities of UDP 
- With RTP audio can be be divided in chunks and added RTP headers, before we encapsulated it in a UDP segment    
- RTP headers indicates: Type of audio encoding in each packet, sequence number and timestamps 
- RTP encapsulation only seen at end systems (not by intermediate routers)

RTP headers:
![[Pasted image 20250403110647.png]]


RTP does NOT:
- Provide mechanism to ensure timely data delivery or other QoS(quality of service) guarantees.
