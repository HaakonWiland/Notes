#bluetooth #RedTeaming #wireless


Article: https://insinuator.net/2025/12/bluetooth-headphone-jacking-full-disclosure-of-airoha-race-vulnerabilities/

Repo: https://github.com/auracast-research/race-toolkit?tab=readme-ov-file


### Bluetooth basics:

- Protocol on the 2.4 GHz ISM band 
- Bluetooth BR / EDR - **Bluetooth classic**
- **Bluetooth Low Energy** - **BLE**


#### Pairing:
- Key sharing for authentication and encryption 
- **B.classic:** Link manager protocol **LMP** -> outputs a key on the host, and provider to the controller. 
- **BLE:** Secure Manager Protocol (**SMP**) -> Operates over fixed L2CAP channel, paring happens on the host part of the stack. (contra controller in b.classic)


#### Bluetooth Addressing:
- Bluetooth devices are identified at the **link layer**, by a Bluetooth Device Address (BD_ADDR -> Kind of like a MAC address.)
- BD_ADDR is 6 bytes long, for both BLE and classic. 

**BLE VS Classic**
- **Classic**: Globally unique address (should be) - 24 first bits manufacturer identifier and last 24 bits device specific. 
- Can be privacy problematic since id is static 

- **BLE:** various types of addresses, can be random or static like the classic version. 
- Random addresses can be private, and regenerated periodically 
- Resolvable Private Addresses (RPA): Appear to be random, but is derived from the devices identity resolving key (IRK)
- Non resolvable Private Addresses (NRPA): Actually random, and cannot be resolved by any party, used for anonymous advertising(?)

- Many devices support both B.classic and BLE, but the addresses are independent, hence a device can have 2 BD_ADDRs 

#### RFCOMM:
- Byte-stream transport over b.classic
- Operated on top of L2CAP 
- Multiple data channels multiplexed over a single Bluetooth connection 
- Channels identified via a UUID


#### GATT:
- Generic attribute profile (GATT)
- Operated on top of Attribute protocol (ATT), provides read, write and notify over L2CAP channel.
- 

