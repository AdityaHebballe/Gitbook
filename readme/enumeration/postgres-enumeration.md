# Postgres Enumeration

* [ ] Try authentication with admin:admin or postgres:postgres and such variations

```
psql -U postgres -W -h 192.168.212.47 -p 5437
```

* [ ] To view current user:

```
SELECT user;
```

* [ ] To get list of databases:

```
\list
```

* [ ] To check version:

```
SELECT version();
```

Now check for exploits and use `searchsploit -m multiple/remote/50847.py` if version is from 9.3 to 11.7
