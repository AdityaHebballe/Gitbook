
# Host:
```
192.168.182.96
```

# Nmap
```
PORT     STATE SERVICE             REASON         VERSION
22/tcp   open  ssh                 syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 51:56:a7:34:16:8e:3d:47:17:c8:96:d5:e6:94:46:46 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDEXg8FXWFLif7j7FQTu8UHDljVPydJScrSANssTmxaJiGrN+gwRDyUv2b+RDyMJQIh78HqKdGNtN2DyFkj7Mt78Rgb8+cHhCM4+qQAIj79w3BaA/U4776LXcoCGzJbZsaevQdLTCaVALgs7udySPJvQ+1s6dFHuNPuxmIRmRP9gf0K3rRbV04il4uUVkOD27qyPmzdSDH/BF+L/ySHkrZqHR4jU6oss2bUtJxT2XU/MS/uxxIoJW8Mx772nL/raLMhxpa3DRKbOCW32F/Jwn3St70NIuA1NX54djYYUJX7DyNFEJEcG7dv9IaPVPtqn+06VlxqfWkuMG9++OfbxPSJcLyPQev/KqLmXGfcMtcbx5q3dt4OphXFQX2XgRpaFlUva3VWLPiuXWK1inHCCRuS4XstquGaw4fFpAhwkSRlsU/Pp+21hqcsZP0IksRddCsqThMQ7G6UwnopYPAmX9HxZhFIq3OuF2Vrsmd2RqOP6kbHBQCl6e5RSiOMAGwrS/s=
|   256 fe:76:e3:4c:2b:f6:f5:21:a2:4d:9f:59:52:39:b9:16 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKul0mG57ikFKG/R4NkI3WOrqKfelhaucgrNRakIlWBS5kOhuds8Atr7ln/+Ibx68OAC4OmNCCIVfgCOTTeUNqY=
|   256 2c:dd:62:7d:d6:1c:f4:fd:a1:e4:c8:aa:11:ae:d6:1f (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIE8eByMdnF3xIjUD57sRJV68xTPIIajqwIJvps6f6Hu9
9090/tcp open  zeus-admin?         syn-ack ttl 61
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Thu, 15 Aug 2024 17:35:23 GMT
|     Last-Modified: Tue, 02 Aug 2022 12:04:43 GMT
|     Content-Type: text/html
|     Accept-Ranges: bytes
|     Content-Length: 115
|     <html>
|     <head><title></title>
|     <meta http-equiv="refresh" content="0;URL=index.jsp">
|     </head>
|     <body>
|     </body>
|     </html>
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Thu, 15 Aug 2024 17:35:29 GMT
|     Allow: GET,HEAD,POST,OPTIONS
|   JavaRMI, drda, ibm-db2-das, informix: 
|     HTTP/1.1 400 Illegal character CNTL=0x0
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x0</pre>
|   SqueezeCenter_CLI: 
|     HTTP/1.1 400 No URI
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 49
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: No URI</pre>
|   WMSRequest: 
|     HTTP/1.1 400 Illegal character CNTL=0x1
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|_    <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x1</pre>
9091/tcp open  ssl/xmltec-xmlmail? syn-ack ttl 61
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost, DNS:*.localhost
| Issuer: commonName=localhost
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-06-28T07:02:39
| Not valid after:  2029-06-27T07:02:39
| MD5:   41ee:f1c1:0cef:c4b0:2436:f5e7:75f4:b064
| SHA-1: c90c:b82e:0547:56be:1f7b:adb4:4a3a:5de7:c177:a4d3
| -----BEGIN CERTIFICATE-----
| MIIDDzCCAfegAwIBAgIIbgECN6DaQ+wwDQYJKoZIhvcNAQELBQAwFDESMBAGA1UE
| AwwJbG9jYWxob3N0MB4XDTI0MDYyODA3MDIzOVoXDTI5MDYyNzA3MDIzOVowFDES
| MBAGA1UEAwwJbG9jYWxob3N0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKC
| AQEA0kAENEQochDNPuEOjx6OVtw065xn/4VFEJnvIkNltcmy71HGxn8sEjmhlUlI
| uQ5oMm+CAf0yk+jZI00eoM/Hdv2BcSKFE0cn20vc1Ht6crRAg1QOYE1qHFf8WADj
| k8P9PAPzyHd6nkwg3dl1LFjqQ8+tUxNGH3QADJDwZvln+iCD1LFLpBdKEpphnLYm
| sN5SW1UandvlwvQKAaHk0k804m1/clJiCD7RLwENqTOmpS4ZBo+j2rNye1ZkWDrv
| akPsW0dXoJwJOF6XmvFY+etbGjL0r+KZLeNXwtmmHPKgnXiRgK6tJHDrOs9YFed6
| LSWuBUVMRDiCofzDry1RAfF2EwIDAQABo2UwYzAhBgNVHREEGjAYgglsb2NhbGhv
| c3SCCyoubG9jYWxob3N0MB0GA1UdDgQWBBTCfjZX0g2ZzFubIKxpJUUtDkCmRDAf
| BgNVHSMEGDAWgBTCfjZX0g2ZzFubIKxpJUUtDkCmRDANBgkqhkiG9w0BAQsFAAOC
| AQEAF+a26AV7ATql2PmevKuMO6HvAw75WsdIHYem0vCDUewiq2Ru9kaUc+yOSzCP
| jZOc3zFvHAyVBKal5w+EPDTU3CxiKkZJtN0sLNCwQy5TDSfllyZxYdiEP3siHTRy
| T2KB2GsNfopJfZliPqKf+xCyvuTPH7/jj46BKehFLhBDJ8cgcgIIKUt2UYp95Ipp
| IeMzBeH1OT4QCXbMz4J4Y2YbIULhG46Cm2dyHfnrILYTJz1EdQq+MfFx6aNE8/GF
| Lz9UdhWZ9Ne4bS/hMqTOu0vcLe7/HL3jupwv5wSK4ZQaUe9YMbi9HA5+Ycyr39GK
| ZtbikUcLXOiuewBkRoOhLi2GxA==
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP: 
|     HTTP/1.1 400 Illegal character CNTL=0x0
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x0</pre>
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Thu, 15 Aug 2024 17:35:40 GMT
|     Last-Modified: Tue, 02 Aug 2022 12:04:43 GMT
|     Content-Type: text/html
|     Accept-Ranges: bytes
|     Content-Length: 115
|     <html>
|     <head><title></title>
|     <meta http-equiv="refresh" content="0;URL=index.jsp">
|     </head>
|     <body>
|     </body>
|     </html>
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Thu, 15 Aug 2024 17:35:41 GMT
|     Allow: GET,HEAD,POST,OPTIONS
|   Hello, Help: 
|     HTTP/1.1 400 No URI
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 49
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: No URI</pre>
|   RPCCheck: 
|     HTTP/1.1 400 Illegal character OTEXT=0x80
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 71
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character OTEXT=0x80</pre>
|   RTSPRequest: 
|     HTTP/1.1 505 Unknown Version
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 58
|     Connection: close
|     <h1>Bad Message 505</h1><pre>reason: Unknown Version</pre>
|   SSLSessionReq: 
|     HTTP/1.1 400 Illegal character CNTL=0x16
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 70
|     Connection: close
|_    <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x16</pre>
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints
```
UDP?