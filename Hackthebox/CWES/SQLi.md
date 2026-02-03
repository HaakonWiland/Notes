#CWES #SQLi #mysql

#### Concept: 
- Application uses a database to provide dynamic funtionalities, if userinput is not handled correctly, which can lead to the client executing database commands -> which we can exploit.
- SQLi can lead to: leak of sensitive information and elevation of privileges. 
- Cause of SQLi: Poorly coded web app or poorly secured back-end server and database. 

- **SQL = Relational databases**
- **NoSQL = Non-relational databases**

- SQL syntax of different databases may differ, but they all require to follow:  https://en.wikipedia.org/wiki/ISO/IEC_9075 



#### Mysql
- Any non-zero value is true (true=1), false is signified by 0.
- Note: logging into mysql might require --skip-ssl option to work. 


#### SQLi basics:
![[Pasted image 20260203181503.png]]

**In-band:**
- Output of the new query is printed to the front end, making it easier to debug if our query works.

- **Union Based**: We may have to spesify where the query output is to be printed. 
- **Error based**: When we can get the SQL errors in the front-end, so we can intentionally create queries that generate errors. 

**Blind:**
- Cannot see a direct output printed to the frontend.

- **Boolean based:** SQL conditional statements to control whether a page returns output, Example: return intended query output if our logical statement is true, else do not show output. 
- **Time based:** SQL conditional statements that delay the page response if the conditional statement returns true, using sleep().

**Out-of-band:**
- Cannot get direct access to query output, whatsoever, so have to be creative to evaluate the response of our modified query, example direct output to a remote location. 
