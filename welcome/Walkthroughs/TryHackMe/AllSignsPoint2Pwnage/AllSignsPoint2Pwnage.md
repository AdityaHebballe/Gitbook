# Host:
```
10.10.164.14
```

# Nmap
```
PORT      STATE SERVICE       REASON          VERSION
21/tcp    open  ftp           syn-ack ttl 127 Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_11-14-20  04:26PM                  173 notice.txt
80/tcp    open  http          syn-ack ttl 127 Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.4.11)
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.4.11
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
|_http-title: Simple Slide Show
| http-methods: 
|   Supported Methods: OPTIONS HEAD GET POST TRACE
|_  Potentially risky methods: TRACE
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
443/tcp   open  ssl/http      syn-ack ttl 127 Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.4.11)
| ssl-cert: Subject: commonName=localhost
| Issuer: commonName=localhost
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2009-11-10T23:48:47
| Not valid after:  2019-11-08T23:48:47
| MD5:   a0a4:4cc9:9e84:b26f:9e63:9f9e:d229:dee0
| SHA-1: b023:8c54:7a90:5bfa:119c:4e8b:acca:eacf:3649:1ff6
| -----BEGIN CERTIFICATE-----
| MIIBnzCCAQgCCQC1x1LJh4G1AzANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDEwls
| b2NhbGhvc3QwHhcNMDkxMTEwMjM0ODQ3WhcNMTkxMTA4MjM0ODQ3WjAUMRIwEAYD
| VQQDEwlsb2NhbGhvc3QwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMEl0yfj
| 7K0Ng2pt51+adRAj4pCdoGOVjx1BmljVnGOMW3OGkHnMw9ajibh1vB6UfHxu463o
| J1wLxgxq+Q8y/rPEehAjBCspKNSq+bMvZhD4p8HNYMRrKFfjZzv3ns1IItw46kgT
| gDpAl1cMRzVGPXFimu5TnWMOZ3ooyaQ0/xntAgMBAAEwDQYJKoZIhvcNAQEFBQAD
| gYEAavHzSWz5umhfb/MnBMa5DL2VNzS+9whmmpsDGEG+uR0kM1W2GQIdVHHJTyFd
| aHXzgVJBQcWTwhp84nvHSiQTDBSaT6cQNQpvag/TaED/SEQpm0VqDFwpfFYuufBL
| vVNbLkKxbK2XwUvu0RxoLdBMC/89HqrZ0ppiONuQ+X2MtxE=
|_-----END CERTIFICATE-----
| tls-alpn: 
|_  http/1.1
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.4.11
|_http-title: Simple Slide Show
|_ssl-date: TLS randomness does not represent time
445/tcp   open  microsoft-ds? syn-ack ttl 127
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
| ssl-cert: Subject: commonName=DESKTOP-997GG7D
| Issuer: commonName=DESKTOP-997GG7D
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-09-07T07:22:32
| Not valid after:  2025-03-09T07:22:32
| MD5:   1604:e4e7:3883:76dd:fafa:32cc:e2ae:ddd5
| SHA-1: 8571:3ab0:da70:91f1:605a:a97e:5441:35b7:6add:9e82
| -----BEGIN CERTIFICATE-----
| MIIC4jCCAcqgAwIBAgIQQdHwe59ThbdAN+DeXCvrajANBgkqhkiG9w0BAQsFADAa
| MRgwFgYDVQQDEw9ERVNLVE9QLTk5N0dHN0QwHhcNMjQwOTA3MDcyMjMyWhcNMjUw
| MzA5MDcyMjMyWjAaMRgwFgYDVQQDEw9ERVNLVE9QLTk5N0dHN0QwggEiMA0GCSqG
| SIb3DQEBAQUAA4IBDwAwggEKAoIBAQDFveGCbpnUUyuQA+9wIVX4l2NSEG90diTt
| JweNyIu72bOHUlAwDy5nCgprXBuY2snumHyzvc841rzVgzeOQNlHZNWEO3ApZXLe
| CLrajmzJJe9nsFZo1nNUpqWM7Sc3GKzx0xtdJyzP8gQtzhb27N4aCuah5KDN8Uvq
| TjTkmqwg9Eaeoijf1URYs7iLkXMncP3toGz8ZG5H9ZsnMi1wmgbQVKoalwgJ36/O
| 3/8RkcKXKDfqX5bkSBbSJwSXc70JS9Gr3NAqWAtoD4/208q468AX+OVReGtrJtWc
| 3BZhVE4XvE2JiqdAXIMWw5rvNHgEqjW4AdMVwY4j81caPY+gUUsBAgMBAAGjJDAi
| MBMGA1UdJQQMMAoGCCsGAQUFBwMBMAsGA1UdDwQEAwIEMDANBgkqhkiG9w0BAQsF
| AAOCAQEAhiA0IJPUOP+2yMjBNpYNgtyAOwL1tjz17ZQ0J8fJqCazdYdbrFiGNnbo
| kJGjW6Pr/7yJYYRVrDtjnUG0aXd9umEEIth6suya2wRaz6rYA+uh/qJpNk32gyGB
| W9lrmPQDtk9zCDoV84OQVyP0v0PrSUzw5zQ+FEAlZpDaUUMzba9Ewe/YZAPvtwah
| MBOHGfwDAGw5g7Ul97xqsZJYmViUrPUQuuzjVZYPyjV6aTQJTENut04bdSfNC42Y
| SlrSbNKVATP4Iigsgucv8VLzKSTUJ4Prh5kdZYYp7V5kKhfB0RLaaTWn38OOSbIH
| MtCMmVILU8bc2SvNqzBL+uMcNQPs3g==
|_-----END CERTIFICATE-----
5040/tcp  open  unknown       syn-ack ttl 127
5900/tcp  open  vnc           syn-ack ttl 127 VNC (protocol 3.8)
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49679/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49683/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
```
UDP?

