
# Host:
```
10.10.203.126
```

# Nmap
```
PORT     STATE SERVICE            REASON          VERSION
80/tcp   open  http               syn-ack ttl 127 Microsoft IIS httpd 8.5
|_http-title: hackpark | hackpark amusements
| http-methods: 
|   Supported Methods: GET HEAD OPTIONS TRACE POST
|_  Potentially risky methods: TRACE
| http-robots.txt: 6 disallowed entries 
| /Account/*.* /search /search.aspx /error404.aspx 
|_/archive /archive.aspx
|_http-server-header: Microsoft-IIS/8.5
3389/tcp open  ssl/ms-wbt-server? syn-ack ttl 127
| ssl-cert: Subject: commonName=hackpark
| Issuer: commonName=hackpark
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2024-09-06T07:05:58
| Not valid after:  2025-03-08T07:05:58
| MD5:   1eb2:0f4e:591a:e53e:a52c:6ca1:6ad4:34cf
| SHA-1: b57b:dea6:5e54:d8bd:0097:5779:d6d1:6fa9:db3a:009f
| -----BEGIN CERTIFICATE-----
| MIIC1DCCAbygAwIBAgIQRkPiRCU9vphFof9ClLgc8zANBgkqhkiG9w0BAQUFADAT
| MREwDwYDVQQDEwhoYWNrcGFyazAeFw0yNDA5MDYwNzA1NThaFw0yNTAzMDgwNzA1
| NThaMBMxETAPBgNVBAMTCGhhY2twYXJrMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
| MIIBCgKCAQEA1dWN7QjUmkqUQAdcZnaPRDL1ROfB5irZX+qZBHEP8XvwUA38cE/g
| e4DwNSIAWq/rLamgp/lWvk4NQMZ9SA6SjryZ9zV7voAhnHOUMpZ/Uz64H8GV8ovl
| XhswvqMbwKpcy9mbt3kxD4vuaCe/zGJ/U7LlUqVlNN4phy3ISJQ5Jw3lgtZK+Npr
| nMpC/k8jD/Koi8CrRJPA5MB7F8I2CdeG7JcYMN1YoejujCZ+L9l/n7pw3kjauP4h
| luIThZrnPXFBon9cAUaLi5IuzfIbQTpnLl6dTjlKNbNmDIkUNxXZmPLuYa8GDq8w
| /d2mmIpYi31J4Pnve7HUq1xREJ5xTu5dEwIDAQABoyQwIjATBgNVHSUEDDAKBggr
| BgEFBQcDATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQEFBQADggEBAGUwf0Lg82aj
| HOnhbaLJcyOEr4N1Y/M2baLY43gCSxuSinIOoZEEF3BvBwkwY5vPx+IHa9L5auB7
| 2bj69kpol8vZPXK7Vh68VTpoCuxxQiJipEiqiEfRwxdMIZ/2SWP61k3VRtO1rrQM
| Whbf4ifWckVeuliaKa9pJP3CxrWkRTX0IPlKBT0xqM67iz/FXtM9uDpNZruCftKb
| ykgzMtgpyMwRjgkdCMfw2swN6j8DzfM1QiCO0RB4zIaK5Lp4GYj5TTFW2W94c300
| xI+QJbOqSp387BYtV2Z4Ie5qw2xC6cbaMHpsNadjALjKJBBD4SuiQt5B1n+aIK/0
| Op+hgNsoX/A=
|_-----END CERTIFICATE-----
| rdp-ntlm-info: 
|   Target_Name: HACKPARK
|   NetBIOS_Domain_Name: HACKPARK
|   NetBIOS_Computer_Name: HACKPARK
|   DNS_Domain_Name: hackpark
|   DNS_Computer_Name: hackpark
|   Product_Version: 6.3.9600
|_  System_Time: 2024-09-07T07:16:24+00:00
|_ssl-date: 2024-09-07T07:16:29+00:00; 0s from scanner time.
```
UDP?