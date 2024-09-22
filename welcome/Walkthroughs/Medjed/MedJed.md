 

# Host:
```
192.168.198.127
```

# Nmap
```
Nmap scan report for 192.168.198.127
Host is up, received user-set (0.070s latency).
Scanned at 2024-07-04 09:41:41 IST for 635s
Not shown: 65382 closed tcp ports (reset), 136 filtered tcp ports (no-response)
PORT      STATE SERVICE       REASON          VERSION
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack ttl 125
3306/tcp  open  mysql?        syn-ack ttl 125
| mysql-info: 
|_  MySQL Error: Host '192.168.45.245' is not allowed to connect to this MariaDB server
| fingerprint-strings: 
|   NULL, SIPOptions, mqtt: 
|_    Host '192.168.45.245' is not allowed to connect to this MariaDB server
5040/tcp  open  unknown       syn-ack ttl 125
8000/tcp  open  http-alt      syn-ack ttl 125 BarracudaServer.com (Windows)
|_http-server-header: BarracudaServer.com (Windows)
|_http-title: Home
|_http-open-proxy: Proxy might be redirecting requests
| http-webdav-scan: 
|   WebDAV type: Unknown
|   Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, PUT, COPY, DELETE, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK
|   Server Date: Thu, 04 Jul 2024 04:21:43 GMT
|_  Server Type: BarracudaServer.com (Windows)
| http-methods: 
|   Supported Methods: OPTIONS GET HEAD PROPFIND PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK POST
|_  Potentially risky methods: PROPFIND PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK
| fingerprint-strings: 
|   FourOhFourRequest, Socks5: 
|     HTTP/1.1 200 OK
|     Date: Thu, 04 Jul 2024 04:12:48 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   GenericLines, GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Thu, 04 Jul 2024 04:12:43 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   HELP4STOMP: 
|     HTTP/1.1 200 OK
|     Date: Thu, 04 Jul 2024 04:15:42 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|     Content-Length: 0
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Thu, 04 Jul 2024 04:12:53 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   OfficeScan: 
|     HTTP/1.1 200 OK
|     Date: Thu, 04 Jul 2024 04:15:37 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|     Content-Length: 0
|   RTSPRequest: 
|     HTTP/1.1 200 OK
|     Date: Thu, 04 Jul 2024 04:12:54 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   SIPOptions: 
|     HTTP/1.1 400 Bad Request
|     Date: Thu, 04 Jul 2024 04:14:10 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|     Content-Type: text/html
|     Cache-Control: no-store, no-cache, must-revalidate, max-age=0
|     <html><body><h1>400 Bad Request</h1>Can't parse request<p>BarracudaServer.com (Windows)</p></body></html>
|   apple-iphoto: 
|     HTTP/1.1 400 Bad Request
|     Date: Thu, 04 Jul 2024 04:17:04 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|     Content-Type: text/html
|     Cache-Control: no-store, no-cache, must-revalidate, max-age=0
|_    <html><body><h1>400 Bad Request</h1>HTTP/1.1 clients must supply "host" header<p>BarracudaServer.com (Windows)</p></body></html>
|_http-favicon: Unknown favicon MD5: FDF624762222B41E2767954032B6F1FF
30021/tcp open  ftp           syn-ack ttl 125 FileZilla ftpd 0.9.41 beta
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
|_ftp-bounce: bounce working!
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -r--r--r-- 1 ftp ftp            536 Nov 03  2020 .gitignore
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 app
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 bin
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 config
| -r--r--r-- 1 ftp ftp            130 Nov 03  2020 config.ru
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 db
| -r--r--r-- 1 ftp ftp           1750 Nov 03  2020 Gemfile
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 lib
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 log
| -r--r--r-- 1 ftp ftp             66 Nov 03  2020 package.json
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 public
| -r--r--r-- 1 ftp ftp            227 Nov 03  2020 Rakefile
| -r--r--r-- 1 ftp ftp            374 Nov 03  2020 README.md
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 test
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 tmp
|_drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 vendor
33033/tcp open  unknown       syn-ack ttl 125
| fingerprint-strings: 
|   GenericLines: 
|     HTTP/1.1 400 Bad Request
|   GetRequest, HTTPOptions: 
|     HTTP/1.0 403 Forbidden
|     Content-Type: text/html; charset=UTF-8
|     Content-Length: 3102
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8" />
|     <title>Action Controller: Exception caught</title>
|     <style>
|     body {
|     background-color: #FAFAFA;
|     color: #333;
|     margin: 0px;
|     body, p, ol, ul, td {
|     font-family: helvetica, verdana, arial, sans-serif;
|     font-size: 13px;
|     line-height: 18px;
|     font-size: 11px;
|     white-space: pre-wrap;
|     pre.box {
|     border: 1px solid #EEE;
|     padding: 10px;
|     margin: 0px;
|     width: 958px;
|     header {
|     color: #F0F0F0;
|     background: #C52F24;
|     padding: 0.5em 1.5em;
|     margin: 0.2em 0;
|     line-height: 1.1em;
|     font-size: 2em;
|     color: #C52F24;
|     line-height: 25px;
|     .details {
|_    bord
44330/tcp open  ssl/unknown   syn-ack ttl 125
| ssl-cert: Subject: commonName=server demo 1024 bits/organizationName=Real Time Logic/stateOrProvinceName=CA/countryName=US/localityName=Laguna Niguel/emailAddress=ginfo@realtimelogic.com/organizationalUnitName=SharkSSL
| Issuer: commonName=demo CA/organizationName=Real Time Logic/stateOrProvinceName=CA/countryName=US/localityName=Laguna Niguel/emailAddress=ginfo@realtimelogic.com/organizationalUnitName=SharkSSL
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: md5WithRSAEncryption
| Not valid before: 2009-08-27T14:40:47
| Not valid after:  2019-08-25T14:40:47
| MD5:   3dd3:7bf7:464d:a77b:6d04:f44c:154b:7563
| SHA-1: 3dc2:5fc6:a16f:1c51:8eee:45ce:80cf:b35e:7f92:ebbe
| -----BEGIN CERTIFICATE-----
| MIICsTCCAhoCAQUwDQYJKoZIhvcNAQEEBQAwgZkxCzAJBgNVBAYTAlVTMQswCQYD
| VQQIEwJDQTEWMBQGA1UEBxMNTGFndW5hIE5pZ3VlbDEYMBYGA1UEChMPUmVhbCBU
| aW1lIExvZ2ljMREwDwYDVQQLEwhTaGFya1NTTDEQMA4GA1UEAxMHZGVtbyBDQTEm
| MCQGCSqGSIb3DQEJARYXZ2luZm9AcmVhbHRpbWVsb2dpYy5jb20wHhcNMDkwODI3
| MTQ0MDQ3WhcNMTkwODI1MTQ0MDQ3WjCBpzELMAkGA1UEBhMCVVMxCzAJBgNVBAgT
| AkNBMRYwFAYDVQQHEw1MYWd1bmEgTmlndWVsMRgwFgYDVQQKEw9SZWFsIFRpbWUg
| TG9naWMxETAPBgNVBAsTCFNoYXJrU1NMMR4wHAYDVQQDExVzZXJ2ZXIgZGVtbyAx
| MDI0IGJpdHMxJjAkBgkqhkiG9w0BCQEWF2dpbmZvQHJlYWx0aW1lbG9naWMuY29t
| MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDI9kHT2xeC8GaBWFcTTqBLU2iF
| Jt8gu5khgjW1LMkOQ1GgX53+siZP4QxPaua0pIEaGXh/qe1wYmucEjxJvidsyFyN
| vgUjS7yP8AMCRGqdxhkbM4A5mcnmxu/8cRxFf19CIVnsD+netpHrscJfmk5f70cz
| QLQQ2NlT8exLSh+5cQIDAQABMA0GCSqGSIb3DQEBBAUAA4GBAJFWpZDFuw9DUEQW
| Uixb8tg17VjTMEQMd136md/KhwlDrhR2Dqk3cs1XRcuZxEHLN7etTBm/ubkMi6bx
| Jq9rgmn/obL94UNkhuV/0VyHQiNkBrjdf4eY6zNY71PgVBxC0wULL5pcpfo0xUKc
| IDMYIaRX7wyNO/lZcxIj0xmxTrqu
|_-----END CERTIFICATE-----
| fingerprint-strings: 
|   RTSPRequest: 
|     HTTP/1.1 200 OK
|     Date: Thu, 04 Jul 2024 04:12:58 GMT
|     Server: BarracudaServer.com (Windows)
|_    Connection: Close
|_ssl-date: 2024-07-04T04:22:15+00:00; 0s from scanner time.
45332/tcp open  http          syn-ack ttl 125 Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.3.23)
| http-methods: 
|   Supported Methods: HEAD GET POST OPTIONS TRACE
|_  Potentially risky methods: TRACE
|_http-title: Quiz App
45443/tcp open  http          syn-ack ttl 125 Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.3.23)
|_http-title: Quiz App
| http-methods: 
|   Supported Methods: HEAD GET POST OPTIONS TRACE
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.3.23
49664/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
```

- Explore barracudadrive on port 44330 and upload a php reverse shell
- Execute php reverse shell from 45332 and gain access as jerren
- Now replace C:/bd/bd.exe with a reverse shell to gain access as nt authority/system

