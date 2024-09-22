# Ldap Enumeration

* [ ] Netexec ldap more to less info order:

```
nxc ldap 192.168.235.122 -u 'fmcsorley' -p 'CrabSharkJellyfish192' --query "(objectClass=*)" "*"
```

```
nxc ldap 192.168.235.122 -u '' -p '' --query "(sAMAccountName=*)" "*"
```

```
nxc ldap <ip> -u username -p password --query "(sAMAccountName=Administrator)" ""
```

Change the name here

```
nxc ldap <ip> -u username -p password --query "(sAMAccountName=Administrator)" "sAMAccountName objectClass pwdLastSet"
```

To get descriptions for users:

```
nxc ldap 192.168.235.122 -u '' -p '' -M get-desc-users
```

* [ ] ldapnomnom for bruteforcing usernames fast asf. Check for usernames with

```
ldapnomnom --input 10m_usernames.txt --output multiservers.txt --dnsdomain contoso.local --maxservers 32 --parallel 16
```

```
ldapnomnom --input 10m_usernames.txt --output results.txt --server 192.168.0.11 --parallel 4
```

Extract rootDSE attributes:

```
ldapnomnom --output rootDSEs.json --dump
```

* [ ] Using nmap to enumerate ldap

```
nmap -sV --script "ldap* and not brute" <IP>` 
```

* [ ] Check ldap using ldapsearch: `ldapsearch -x -H ldap://<ip>`
* [ ] Get FQDN :`ldapsearch -x -H ldap://<ip> -s base namingcontexts`
* [ ] Get all info:

```
ldapsearch -x -H ldap://192.168.218.122 -s base namingcontexts
```

Get everything to grep for:

```
ldapsearch -x -H ldap://192.168.192.122 -D '' -w '' -b "DC=hutch,DC=offsec" > ldap-search
```

```
ldapsearch -H ldap://<ip> -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "(&(objectclass=user))"
```

Analysing:

```
cat ldap.txt | awl '{print $1}' | sort | uniq -c| sort -n | grep ':'
```

Print first word then sort then print unique and then show non repeating.`grep ':'` removes the base64 stuff.

* [ ] To **enumerate users** over ldap (ldap can be finicky revert):

```
ldapsearch -H ldap://<ip> -x -b "DC=absolute,DC=htb" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -f2 -d" "
```

Optionally use authentication with `-U` and `-w` for username and password

* [ ] To get everything:

```
ldapsearch -x -H ldap://192.168.192.122 -D '' -w '' -b "DC=hutch,DC=offsec"
```
