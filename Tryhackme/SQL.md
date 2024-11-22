


#### Basic commands:

```
- CREATE DATABASE <name>;
- SHOW DATABASES;
- USE <database name>; 
- CREATE TABLE <table name> (
	key datatype,
	.. );
- ALTER TABLE <table_name>;
- SHOW TABLES;
- DROP TABLE <table_name>;
- DECRIBE <db_name>;

```


#### Interact commands:
```
- INSERT;
- SELECT;
- UPDATE; 
- DELETE;


EXAMPLES:


SELECT name, description FROM books;

UPDATE books SET description = "An In-Depth Guide to Android's Security Architecture."  WHERE id = 1;


```



#### Clauses:
```
- DISTICT:
- GROUP BY:
- ORDER BY: Often used with ASC or DESC
- HAVING: Example HAVING name LIKE "%pyramide-jim%"


EXAMPLES:

SELECT DISTINCT category FROM hacking_tools;

SELECT name FROM hacking_tools ORDER BY name DESC;

```

#### Logical operators 
```
- LIKE
- AND
- OR 
- NOT
- BETWEEN: ex search for itemes with dates in a spesific range 
- =, !=, <, >, <=, >=



EXAMPLES:

SELECT * FROM hacking_tools WHERE category="Multi-tool" AND description LIKE "%pentester%";

SELECT name FROM hacking_tools WHERE category="Network intelligence" AND amount < 100;



```

#### Some useful functions:

```
- CONCAT(): Allow us to combine text from different columns 
- SUBSTRING(colum_name, word, amount_of_characters)
- LENGTH();
- MAX(), MIN(), SUM()
- COUNT()
```


#### More examples:

```




Gets name and name_length in descending order:
- SELECT name, LENGTH(name) AS length FROM hacking_tools ORDER BY length DESC;



Gets the names of all entries in hacking_tools where the amount does not end with a 0. The output prints it in the format of: name1 & name2 & ..
- SELECT GROUP_CONCAT(name SEPARATOR " & ") FROM hacking_tools WHERE amount NOT LIKE "%0";

```