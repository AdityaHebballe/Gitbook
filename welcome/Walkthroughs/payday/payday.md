
# Host:
```
192.168.152.39
```

# Nmap 
```
PORT    STATE SERVICE     REASON         VERSION
22/tcp  open  ssh         syn-ack ttl 61 OpenSSH 4.6p1 Debian 5build1 
80/tcp  open  http        syn-ack ttl 61 Apache httpd 2.2.4 (PHP/5.2.3-1ubuntu6)
| http-methods: 
|_  Supported Methods: HEAD
|_http-server-header: Apache/2.2.4 (Ubuntu) PHP/5.2.3-1ubuntu6
110/tcp open  pop3        syn-ack ttl 61 Dovecot pop3d
| ssl-cert: Subject: commonName=ubuntu01/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/localityName=Everywhere/emailAddress=root@ubuntu01/organizationalUnitName=Office for Complication of Otherwise Simple Affairs
| Issuer: commonName=ubuntu01/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/localityName=Everywhere/emailAddress=root@ubuntu01/organizationalUnitName=Office for Complication of Otherwise Simple Affairs
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2008-04-25T02:02:48
| Not valid after:  2008-05-25T02:02:48
| MD5:   90db:2a9a:2d86:29dc:f047:d19d:c636:9c8e
| SHA-1: 1bde:08b6:86fc:9892:33c9:7bd4:0125:c572:5b32:d829
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|_    SSL2_DES_192_EDE3_CBC_WITH_MD5
|_pop3-capabilities: CAPA SASL UIDL STLS PIPELINING TOP RESP-CODES
|_ssl-date: 2024-09-10T14:32:12+00:00; +7s from scanner time.
139/tcp open  netbios-ssn syn-ack ttl 61 Samba smbd 3.X - 4.X (workgroup: MSHOME)
143/tcp open  imap        syn-ack ttl 61 Dovecot imapd
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|_    SSL2_DES_192_EDE3_CBC_WITH_MD5
|_imap-capabilities: SASL-IR completed Capability CHILDREN STARTTLS LOGIN-REFERRALS LITERAL+ NAMESPACE OK THREAD=REFERENCES SORT MULTIAPPEND UNSELECT IDLE IMAP4rev1 LOGINDISABLEDA0001
| ssl-cert: Subject: commonName=ubuntu01/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/localityName=Everywhere/emailAddress=root@ubuntu01/organizationalUnitName=Office for Complication of Otherwise Simple Affairs
| Issuer: commonName=ubuntu01/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/localityName=Everywhere/emailAddress=root@ubuntu01/organizationalUnitName=Office for Complication of Otherwise Simple Affairs
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2008-04-25T02:02:48
| Not valid after:  2008-05-25T02:02:48
| MD5:   90db:2a9a:2d86:29dc:f047:d19d:c636:9c8e
| SHA-1: 1bde:08b6:86fc:9892:33c9:7bd4:0125:c572:5b32:d829
|_ssl-date: 2024-09-10T14:32:12+00:00; +7s from scanner time.
445/tcp open  netbios-ssn syn-ack ttl 61 Samba smbd 3.0.26a (workgroup: MSHOME)
993/tcp open  ssl/imap    syn-ack ttl 61 Dovecot imapd
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|_    SSL2_DES_192_EDE3_CBC_WITH_MD5
| ssl-cert: Subject: commonName=ubuntu01/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/localityName=Everywhere/emailAddress=root@ubuntu01/organizationalUnitName=Office for Complication of Otherwise Simple Affairs
| Issuer: commonName=ubuntu01/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/localityName=Everywhere/emailAddress=root@ubuntu01/organizationalUnitName=Office for Complication of Otherwise Simple Affairs
995/tcp open  ssl/pop3    syn-ack ttl 61 Dovecot pop3d
|_pop3-capabilities: USER CAPA UIDL SASL(PLAIN) PIPELINING TOP RESP-CODES
|_ssl-date: 2024-09-10T14:32:11+00:00; +6s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|_    SSL2_DES_192_EDE3_CBC_WITH_MD5
| ssl-cert: Subject: commonName=ubuntu01/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/localityName=Everywhere/emailAddress=root@ubuntu01/organizationalUnitName=Office for Complication of Otherwise Simple Affairs
| Issuer: commonName=ubuntu01/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/localityName=Everywhere/emailAddress=root@ubuntu01/organizationalUnitName=Office for Complication of Otherwise Simple Affairs
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2008-04-25T02:02:48
| Not valid after:  2008-05-25T02:02:48
| MD5:   90db:2a9a:2d86:29dc:f047:d19d:c636:9c8e
| SHA-1: 1bde:08b6:86fc:9892:33c9:7bd4:0125:c572:5b32:d829
```
UDP?