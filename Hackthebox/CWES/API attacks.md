#CWES #API 

#### Lessons:
- if we are using bash online commands, it matters if we use "" or '', really impotent if we are using variables within a string. 
- Generally two things we are interested in; getting access to data we should not have access to, AND manipulating data we are not authorized to.
- When attacking APIs, check out what we can access and what data it shares with us. Sometimes we can use the data from one endpoint to invoke another.
- Even if the application claims you dont have access to an endpoint, you should still try - sometimes you have access.
#### API types

**REST:** (Representational state transfer)
- Main focus of HTB module
- Get, post, put, delete
- Client server model
- Stateless - each request contains all necessary information 

**SOAP:** (Simple object access protocol)
- xml for message exchange 
- often standardized and more complex then rest apis

**GraphQL:**
- Its own query language
- Can be more specific about what data we want to fetch

**gRPC:**
- Protocol buffers for message serialization
- High performance
- Fitting for microservices and distributed systems


#### Types of vulnerabilities and attacks:
- Exposure of sensitive data
- Authentication and authorization issues
- Improper error handling
- More: https://owasp.org/API-Security/editions/2023/en/0x11-t10/

Note:
Create 30MB pdf file with random bytes
```shell
dd if=/dev/urandom of=certificateOfIncorporation.pdf bs=1M count=30
```

#### Prevent insecure APIs:
- encrypted channels for data transmission between apis, preventing mitm attacks
- Validate and sanizate data received from APIs before processing or forwarding it. NEVER TRUST USER INPUT
- Robust authentication of the different APIs
- Sufficient rate limiting - to protect against DOS
- Monitoring of APIs to detecting misuse and incidents 

