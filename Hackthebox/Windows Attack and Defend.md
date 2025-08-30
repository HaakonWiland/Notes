#ActiveDirectory #SOC #pentesting #Windows #hackthebox

Basic AD elements:
A `domain` is a group of objects that share the same AD database, such as users or devices.

A `tree` is one or more domains grouped. Think of this as the domains `test.local`, `staging.test.local`, and `preprod.test.local`, which will be in the same tree under `test.local`. Multiple trees can exist in this notation.

A `forest` is a group of multiple trees. This is the topmost level, which is composed of all domains.

`Organizational Units` (`OU`) are Active Directory containers containing user groups, Computers, and other OUs.

`Trust` can be defined as access between resources to gain permission/access to resources in another domain.

`Domain Controller` is (generally) the Admin of the Active Directory used to set up the entire Directory. The role of the Domain Controller is to provide Authentication and Authorization to different services and users. In Active Directory, the Domain Controller has the topmost priority and has the most authority/privileges.

`Active Directory Data Store` contains Database files and processes that store and manages directory information for users, services, and applications. Active Directory Data Store contains the file `NTDS.DIT`, the most critical file within an AD environment; domain controllers store it in the `%SystemRoot%\NTDS` folder.


HOT TIPS: 
Connection to smbclient to transfere files between machines on same network: 
```
smbclient \\\\TARGET_IP\\Share -U <machine>/<user>%<password>
```

Also, if you want to connect to the DC machines, you have to first rdp into the target windows machine. Then rdp into the DC machine with RDC app. 


### Kerberoasting: 
"In Active Directory, a [Service Principal Name (SPN)](https://learn.microsoft.com/en-us/windows/win32/ad/service-principal-names) is a unique service instance identifier. `Kerberos` uses SPNs for authentication to associate a service instance with a service logon account, which allows a client application to request that the service authenticate an account even if the client does not have the account name. When a Kerberos `TGS` service ticket is asked for, it gets encrypted with the service account's NTLM password hash.

Kerberoasting is a [cyberattack](https://www.ibm.com/topics/cyber-attack) that exploits the Kerberos authentication protocol. Threat actors steal Kerberos service tickets to uncover the plaintext passwords of network service accounts."

From a account we have access to we used:
```
.\Rubeus.exe kerberoast /outfile:output.txt
```

Here we got some Kerberos 5 hashes for some users. With hashcat we can crack some of these hashes:

```
hashcat -m 13100 output.txt rockyou.txt --outfile="cracked.txt"
```

Looking in the cracked.txt file we find a password: mariposa

#### Detection:
When a `TGS` is requested, an event log with ID `4769` is generated, but there can be many 4769 events . One way to detect Rubeus attack could be to look for events where one user requests **multiple tickets** in a short time span. 

In this case we only got 2-3 hashes. I was possible to manually look in event viewer for 4769 events in the relevant time frame, and i see 3 events happening as the same time. 



### AS-REProasting
"The `AS-REProasting` attack is similar to the `Kerberoasting` attack; we can obtain crackable hashes for user accounts that have the property `Do not require Kerberos preauthentication` enabled. The success of this attack depends on the strength of the user account password that we will crack."


#### Attack:

```
.\Rubeus.exe asreproast /outfile:asrep.txt
```
- Gives us some hashes in a txt file which we transfer to out machine. Then we can use hashcat in **Kerberos 5, etype 23, AS-REP** mode to crack it. 

For some reason we have to add 23$ to the hash, to make it possible to crack with hashcat. Note: Just look at the example format on the hashcat guide page [[Hashes]]. 

```
sudo hashcat -m 18200 asrep.txt rockyou.txt --outfile asrepcrack.txt
```

Then we can find the cracked hashes in the asrepcrack.txt file.
```
.....:mariposa
```

#### Detection:
The success of this attack is depentent on password strength and that the users have the `Do not require Kerberos preauthenticatio` configuration. 

"First and foremost, we should only use this property if needed; a good practice is to review accounts quarterly to ensure that we have not assigned this property. Because this property is often found with some regular user accounts, they tend to have easier-to-crack passwords than service accounts with SPNs (those from Kerberoast). Therefore, for users requiring this configured, we should assign a separate password policy, which requires at least 20 characters to thwart cracking attempts."

One way to start detecting is by looking for **Event id 4768 - Kerberos Authentication ticket was generated**. This is an event there are a lot of, since each time a user logs in to their machine, an 4768 event is logged to the domain controller. 

So to detect such attack, we have to know the time frame, or the spesific target user etc, such that we can narrow down the search. 
