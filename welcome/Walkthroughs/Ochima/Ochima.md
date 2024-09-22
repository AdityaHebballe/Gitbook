
# Host:
```
192.168.182.32
```

# Nmap
```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 61 OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 b9:bc:8f:01:3f:85:5d:f9:5c:d9:fb:b6:15:a0:1e:74 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBYESg2KmNLhFh1KJaN2UFCVAEv6MWr58pqp2fIpCSBEK2wDJ5ap2XVBVGLk9Po4eKBbqTo96yttfVUvXWXoN3M=
|   256 53:d9:7f:3d:22:8a:fd:57:98:fe:6b:1a:4c:ac:79:67 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBdIs4PWZ8yY2OQ6Jlk84Ihd5+15Nb3l0qvpf1ls3wfa
80/tcp   open  http    syn-ack ttl 61 Apache httpd 2.4.52 ((Ubuntu))
| http-methods: 
|_  Supported Methods: OPTIONS HEAD GET POST
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
8338/tcp open  unknown syn-ack ttl 61
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Server: Maltrail/0.52
|     Date: Thu, 15 Aug 2024 15:39:54 GMT
|     Connection: close
|     Content-Type: text/html
|     Last-Modified: Sat, 31 Dec 2022 22:58:57 GMT
|     Content-Security-Policy: default-src 'self'; style-src 'self' 'unsafe-inline'; img-src * blob:; script-src 'self' 'unsafe-eval' https://stat.ripe.net; frame-src *; object-src 'none'; block-all-mixed-content;
|     Cache-Control: no-cache
|     Content-Length: 7091
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta http-equiv="X-UA-Compatible" content="IE=edge">
|     <meta http-equiv="Content-Type" content="text/html;charset=utf8">
|     <meta name="viewport" content="width=device-width, user-scalable=no">
|     <meta name="robots" content="noindex, nofollow">
|     <title>Maltrail</title>
|     <link rel="stylesheet" type="text/css" href="css/thirdparty.min.css">
|     <link rel="stylesheet" type="text/css" hre
|   HTTPOptions: 
|     HTTP/1.0 501 Unsupported method ('OPTIONS')
|     Server: Maltrail/0.52
|     Date: Thu, 15 Aug 2024 15:39:54 GMT
|     Connection: close
|     Content-Type: text/html;charset=utf-8
|     Content-Length: 500
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
|     "http://www.w3.org/TR/html4/strict.dtd">
|     <html>
|     <head>
|     <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 501</p>
|     <p>Message: Unsupported method ('OPTIONS').</p>
|     <p>Error code explanation: HTTPStatus.NOT_IMPLEMENTED - Server does not support this operation.</p>
|     </body>
|_    </html>
```
