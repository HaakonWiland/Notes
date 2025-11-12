#CWES #hackthebox #web #pentesting 

OWASP testing guide: https://github.com/OWASP/wstg/tree/master/document/4-Web_Application_Security_Testing 

### General
- Frontend: Client side 
- Backend: Server side
- Static websites: Content of the site does not change with user interaction (Web 1.0)
- Dynamic websites: Presents dynamic content based on user interaction 

#### Web app vs native operating system application
- Web apps can run in any browser, on any os 
- Web apps does not consume space on the clients hard drive (they dont have to install the application.)
- All users use the same version of the web app, no need for the user to manually upgrade the app, this is handled by the webserver. 

- Native os application usually has higher operating speed and has the ability to utilize native operating system libs and local hardware. 

#### Risks of web apps
- Web apps are exposed to the internet, and available from a browser
- If the web app is hosted on a server with other sensitive information, a compromise on the web app could lead to compromise of this information. 

#### Web app infrastructure
**Client-server**
- Front end: Interpreted and executed on the client side
- Back end: Compiled, interpreted and executed by the hosting server 

**One server**
- Variant of the client-server 
- utilize only 1 server, which can host multiple web applications and even databases. 
- Con: If 1 web app is compromised, then everything hosted on the server my be compromised. 

**Many servers - one database** 
- Split the database into its own database server, which all web app servers talk to. 
- Can have many servers for 1 app(backups of the app) or host multiple apps on different servers. 
- Pro: segmentation, splitting the eggs in multiple baskets. 

**Many servers - many databases** 
- Now use multiple database servers, such that we are backup and can reduce downtime 
- Pro: robust setup 
- Con: Complex 

#### Web app components:
**Client:**
**Server:**
- Webserver
- Web Application Logic
- Database
 
 **Microservices:**
- Independent components of the web application, and usually programmed for one task only.
- Communication is stateless, since data is stored separately from different microservices. 
- "Service oriented architecture (SOA)"
- Can be written in different programming languages 
- 3rd Party Integrations
- Web Application Integrations

**Serverless:** 
- Stateless computing in containers on a cloud provider. 
- Dont have to manage infrastructure (Scaling, maintaining servers, etc)

Note: 
- They may not always be a programming error that causes a web app vulnerability, it might be design error in its architecture. 

### Front end:
- usually: html, css, js 

#### HTML injection:
- User input not validated, user can send in html code as input and get it rendered.
- This can for example be used to trick other users of the site; ex. inject a html element with a malicious link. 

#### XSS:
- Inject javascript instead of html
![[Pasted image 20251110170626.png]]
NOTE: Browsers only execute javascript when it is places in an executable HTML context: script tags, event handlers, urls etc. 
- Common mistake: User-controlled HTML into innerHTML without sanitization. 


#### CSRF (Cross site request forgery)
- Execute queries, api calls, on a web app that the victim is currently authenticated to. 
- Ex: Attacker inject script into the app, and once the victim views the payload on the page, the code script will execute and do something malicious (change password or something). NOTE: The script must then be present in the victims browser/copy of the page. 
- Preventions: **Sanitization** of user input (and output) for non-standard characters  and **validate** that the input matches the expected format 
- Preventions: Web application firewall 


### Back end:
- Back end servers: Hardware and os hosting 
- web server: Handles http requests and connection 
- databases: 
- Developer frameworks 

#### Databases:
- Speed in storing and retrieving data, size to store large amounts of data, scalability, and cost.  
- NoSQL: Not in a standard table format, but commonly use: key-value, Document-Based, Wide-column, Graph. 

#### Frameworks and APIs
- Frameworks lets us build stuff faster 
- Can use APIs to communicate between front and backend. 

**REST:** Representational State Transfer 
- Shares data through the URL path, and output usually in a JSON format (Can be xml, raw data or something else)

**SOAP:** Simple object access 
- Share data through XML


### Most common web dev mistakes:
|       |                                                    |
| ----- | -------------------------------------------------- |
| `1.`  | Permitting Invalid Data to Enter the Database      |
| `2.`  | Focusing on the System as a Whole                  |
| `3.`  | Establishing Personally Developed Security Methods |
| `4.`  | Treating Security to be Your Last Step             |
| `5.`  | Developing Plain Text Password Storage             |
| `6.`  | Creating Weak Passwords                            |
| `7.`  | Storing Unencrypted Data in the Database           |
| `8.`  | Depending Excessively on the Client Side           |
| `9.`  | Being Too Optimistic                               |
| `10.` | Permitting Variables via the URL Path Name         |
| `11.` | Trusting third-party code                          |
| `12.` | Hard-coding backdoor accounts                      |
| `13.` | Unverified SQL injections                          |
| `14.` | Remote file inclusions                             |
| `15.` | Insecure data handling                             |
| `16.` | Failing to encrypt data properly                   |
| `17.` | Not using a secure cryptographic system            |
| `18.` | Ignoring layer 8                                   |
| `19.` | Review user actions                                |
| `20.` | Web Application Firewall misconfigurations         |

#### To learn more about web applications:
|**Step**|**To-Do**|
|---|---|
|`1.`|Set up a VM with a web server|
|`2.`|Create an `HTML` page|
|`3.`|Design it with `CSS`|
|`4.`|Add some simple functions with `JavaScript`|
|`5.`|Program a simple web application|
|`6.`|Connect your web application to the database|
|`7.`|Experiment with APIs|
|`8.`|Test your application for various vulnerabilities and security holes|
|`9.`|Try to adjust your code and configurations to close the vulnerabilities|