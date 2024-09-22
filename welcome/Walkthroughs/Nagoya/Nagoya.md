
# Host:
```
192.168.162.21
```

# Nmap
```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 125 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 125 Microsoft IIS httpd 10.0
|_http-title: Nagoya Industries - Nagoya
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
|_http-favicon: Unknown favicon MD5: 9200225B96881264E6481C77D69C622C
88/tcp    open  kerberos-sec  syn-ack ttl 125 Microsoft Windows Kerberos (server time: 2024-08-20 12:48:26Z)
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: nagoya-industries.com0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 125
464/tcp   open  kpasswd5?     syn-ack ttl 125
593/tcp   open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 125
3268/tcp  open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: nagoya-industries.com0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 125
3389/tcp  open  ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: NAGOYA-IND
|   NetBIOS_Domain_Name: NAGOYA-IND
|   NetBIOS_Computer_Name: NAGOYA
|   DNS_Domain_Name: nagoya-industries.com
|   DNS_Computer_Name: nagoya.nagoya-industries.com
|   DNS_Tree_Name: nagoya-industries.com
|   Product_Version: 10.0.17763
|_  System_Time: 2024-08-20T12:49:32+00:00
|_ssl-date: 2024-08-20T12:50:12+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=nagoya.nagoya-industries.com
| Issuer: commonName=nagoya.nagoya-industries.com
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-08-01T01:46:30
| Not valid after:  2025-01-31T01:46:30
| MD5:   21dd:ee05:f731:db23:c7aa:082b:c258:7fe0
| SHA-1: 62cd:9159:e060:0cc5:4a2a:a278:214b:97d5:04df:0598
| -----BEGIN CERTIFICATE-----
| MIIC/DCCAeSgAwIBAgIQGMt7yxY4+bZMMZohDSgPazANBgkqhkiG9w0BAQsFADAn
| MSUwIwYDVQQDExxuYWdveWEubmFnb3lhLWluZHVzdHJpZXMuY29tMB4XDTI0MDgw
| MTAxNDYzMFoXDTI1MDEzMTAxNDYzMFowJzElMCMGA1UEAxMcbmFnb3lhLm5hZ295
| YS1pbmR1c3RyaWVzLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEB
| ANtE+B3NmlB84Oz1ErytcTRnOM+851YeRfHPUZB2KIlrEUGjBmv5TREeu3DSZWkN
| 9jDTylL0Kc+TtYy81WTBnnIaoP+TWnT4AGCUnjnic8fqDrJD9cGwmJL+NuGm6Zll
| l/gfN+QmxUEee+AYY2wRFt+Yz38gYhrX01dif7xCIgrHTYvWlOVW8LAow0eVi/+G
| q+EuJGem6Cg/XFPRTo/UrWrLQjgDp7JtFWAyKm2NEBIudwz6hkEGBrn+bB538C+q
| zLT1yqTDqjeLTQnYFYVvXP1LhjIZpqrBDyUPs4iLImzqUVrejbpRa+eCA28ZmrBa
| x5O1H+4IsU1CNs9Uq4uuBNkCAwEAAaMkMCIwEwYDVR0lBAwwCgYIKwYBBQUHAwEw
| CwYDVR0PBAQDAgQwMA0GCSqGSIb3DQEBCwUAA4IBAQAxiSTIuo1jJ8wK7x0lMaf/
| qih+Au2jjQcMoWQSPHxsnss1g1PYUvqRTQxAYbWqFRS6IQ/rg5epFWKlZ6oTb0uk
| gXm190NCzrP0UOoNgkKr8YF+u/8lwLK647W+mlyJ4zL6LhpFG3tKQrWMUi3BYD7X
| ZFd0qSk6Bf0Ec0dX4NW4pLEnWuw38T697FEHTrINbXby+ZKHtoL+TKhCnj7gRa4U
| LR2WqZ5Q1katKXh61P+YDWpMWnT9DXdNnXnDV2OkFe4KkODaNu+2/C5mjytQe6id
| zDdv/GpiyD/0/w11jp5BdhQMCNuy8DZDT/CMgXzErgVfubOPVNQOHhW/qsxuiz91
|_-----END CERTIFICATE-----
5985/tcp  open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 125 .NET Message Framing
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49676/tcp open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
49678/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49679/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49693/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49708/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49803/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
```
UDP?