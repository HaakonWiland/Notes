#CWES #methodology 


#### What should be in the report:
- How to reproduce - step by step
- CWE and CVSS (https://www.first.org/cvss/calculator/3.1)
- Vuln type and impact 
- POC
- Remediation


#### Good report examples:
- https://hackerone.com/reports/341876
- https://hackerone.com/reports/783877

#### Using CVE calculator:

**Attack Vector**
- `Network (N):` Attackers can only exploit this vulnerability through the network layer (remotely exploitable).
- `Adjacent (A):` Attackers can exploit this vulnerability only if they reside in the same physical or logical network (secure VPN included).
- `Local (L):` Attackers can exploit this vulnerability only by accessing the target system locally (e.g., keyboard, terminal, etc.) or remotely (e.g., SSH) or through user interaction.
- `Physical (P):` Attackers can exploit this vulnerability through physical interaction/manipulation.

**Attacker complexity**
- `Low (L):` No special preparations should take place to exploit the vulnerability successfully. The attackers can exploit the vulnerability repeatedly without any issue.
- `High (H):` Special preparations and information gathering should take place to exploit the vulnerability successfully.

**Privileges required**
- `None (N):` No special access related to settings or files is required to exploit the vulnerability successfully. The vulnerability can be exploited from an unauthorized perspective.
- `Low (L):` Attackers should possess standard user privileges to exploit the vulnerability successfully. The exploitation in this case usually affects files and settings owned by a user or non-sensitive assets.
- `High (H):` Attackers should possess admin-level privileges to exploit the vulnerability successfully. The exploitation in this case usually affects the entire vulnerable system.

**User interaction**
- `None (N):` Attackers can successfully exploit the vulnerability independently.
- `Required (R):` A user should take some action before the attackers can successfully exploit the vulnerability.

**Confidentiality**
- `None (N):` The confidentiality of the vulnerable component does not get impacted.
- `Low (L):` The vulnerable component will experience some loss of confidentiality upon successful exploitation of the vulnerability. In this case, the attackers do not have control over what information is obtained.
- `High (H):` The vulnerable component will experience total (or serious) loss of confidentiality upon successfully exploiting the vulnerability. In this case, the attackers have total (or some) control over what information is obtained.

**Integrity**
- `None (N):` The integrity of the vulnerable component does not get impacted.
- `Low (L):` Attackers can modify data in a limited manner on the vulnerable component upon successfully exploiting the vulnerability. Attackers do not have control over the consequence of a modification, and the vulnerable component does not get seriously affected in this case.
- `High (H):` Attackers can modify all or critical data on the vulnerable component upon successfully exploiting the vulnerability. Attackers have control over the consequence of a modification, and the vulnerable component will experience a total loss of integrity.

**Availability**
- `None (N):` The availability of the vulnerable component does not get impacted.
- `Low (L):` The vulnerable component will experience some loss of availability upon successfully exploiting the vulnerability. The attacker does not have complete control over the vulnerable component's availability and cannot deny the service to users, and performance is just reduced.
- `High (H):` The vulnerable component will experience total (or severe) availability loss upon successfully exploiting the vulnerability. The attacker has complete (or significant) control over the vulnerable component's availability and can deny the service to users. Performance is significantly reduced.
