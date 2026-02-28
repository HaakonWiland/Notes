#hardware #RedTeaming 

#### IDEA:
USB plug-in
- device claims “I am a keyboard”
- OS auto-loads HID driver
- keystrokes accepted without prompt
- benign command executed

#### General theory:

**USB identity:**
1. Device is powered via USB
2. Device responds with a set of **USB descriptors**
3. The OS reads those descriptors and says:  
    “OK, you claim to be X. I will treat you as X.”
    
That’s it. There is no cryptographic verification and no human prompt for most classes.

**USB descriptor**
small data structures stored in firmware that describe:
- Vendor ID / Product ID
- Device class (very important)
- Subclass / protocol
- Endpoints
- Optional: HID report descriptors

### ChipWhisperer Nano (important bits)
- MCU: **STM32F042 / STM32F0 series**
- USB: **Full-speed device only**
- Flash/RAM: limited
- Debug: **SWD**, not USB
- USB connector is wired directly to the MCU

Key implication:
**Once you change USB firmware, you lose USB-based debugging.**