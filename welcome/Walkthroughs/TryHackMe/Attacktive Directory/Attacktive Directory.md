
# Host:
```
10.10.80.70
```

# Nmap
```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-08-23 04:27:34Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
|_ssl-date: 2024-08-23T04:29:17+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=AttacktiveDirectory.spookysec.local
| Issuer: commonName=AttacktiveDirectory.spookysec.local
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-08-22T04:18:08
| Not valid after:  2025-02-21T04:18:08
| MD5:   8bb9:564f:aad3:9786:6a0e:d04e:349b:fda0
| SHA-1: a4c5:1c30:d06c:e9cd:a9eb:5268:99bf:bd0c:4503:890e
| -----BEGIN CERTIFICATE-----
| MIIDCjCCAfKgAwIBAgIQHTw9vne/369KYlzrGC556DANBgkqhkiG9w0BAQsFADAu
| MSwwKgYDVQQDEyNBdHRhY2t0aXZlRGlyZWN0b3J5LnNwb29reXNlYy5sb2NhbDAe
| Fw0yNDA4MjIwNDE4MDhaFw0yNTAyMjEwNDE4MDhaMC4xLDAqBgNVBAMTI0F0dGFj
| a3RpdmVEaXJlY3Rvcnkuc3Bvb2t5c2VjLmxvY2FsMIIBIjANBgkqhkiG9w0BAQEF
| AAOCAQ8AMIIBCgKCAQEAxhgLa15X1FfQovxLbXAOeC+WZbqGMSURWeG7NQBVEnlw
| 0iyrTayeCX+m9L0A77VzOfO7Fc7hklhpLnHzgA4LBxIs/Xo+XVHbsWhgdyLVmoRc
| eCHZ639AEZT0J91WUMkFVHOzhIWbN6gwrZ+frJ/5wbl5NIPMbbjeZNV2Gvdp8oSW
| XlPZj67bDDVRptb1+i9KrvcD6B6h4ur5fZvU1c3tqq7PYS13x9CdZ+pWfeR5S3zy
| JGNZ/QQqY/svlgC7JishiJNi4HJ6egn4chvrC/04CSFQl5quw7QiitqbX+rTvJFQ
| u7BcCXPDz1nG5jjAPgHB8Jv3VobFOUAvK/HY2yPy6QIDAQABoyQwIjATBgNVHSUE
| DDAKBggrBgEFBQcDATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQELBQADggEBALGb
| b+h6UrsOqC54k4AaN9uMsHR9s72dIGSQpjC5gM0gZPQIaUsxjPJ66/3DPqM85II8
| HSGebBi/8cFnrlyJoIHynYntMJnNaYTsElXAjdE46q6EGY/r4u0fUGbHzR2j1kTY
| 76qwJgtMk3C3cwGmTZl29I/77jh0aC6HiglyxGit2FYqxd+AdgSVCOoHWtEhyGYK
| nkgDNg4LDO1nf1eMshD2rNVYukOKfLulzxDiSkncfxw4N10Zfhq7v3Wmb48q6IaA
| n0Rw2c01l9GoriZA5mLhSYMrw3ds1mPVudayeEck7HiYssPbzZjQ65tCwVkt6602
| JP0BJ9++KE3OKcvPVEQ=
|_-----END CERTIFICATE-----
| rdp-ntlm-info: 
|   Target_Name: THM-AD
|   NetBIOS_Domain_Name: THM-AD
|   NetBIOS_Computer_Name: ATTACKTIVEDIREC
|   DNS_Domain_Name: spookysec.local
|   DNS_Computer_Name: AttacktiveDirectory.spookysec.local
|   DNS_Tree_Name: spookysec.local
|   Product_Version: 10.0.17763
|_  System_Time: 2024-08-23T04:29:08+00:00
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49672/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49673/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49674/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49678/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49685/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49695/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
```
UDP?