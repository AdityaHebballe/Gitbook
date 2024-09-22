# Enumeration
```
PORT    STATE SERVICE VERSION
21/tcp  open  ftp     vsftpd 3.0.5
22/tcp  open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 ae:3c:b4:e7:1d:b7:c2:43:86:40:83:19:90:f5:33:c1 (ECDSA)
|_  256 8a:d0:1b:54:0b:aa:15:85:b3:ac:7b:4b:1c:09:14:2a (ED25519)
80/tcp  open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Login
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
873/tcp open  rsync   (protocol version 31)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
So enumerating rsync:
`rsync -av --list-only rsync://10.10.80.132/`
![](attachment/f5c7c739673f54939729b5a0ec736559.png)
Now downloading everything from httpd:
We notice a site.db so opening it there are 2 users:
![](attachment/afd15af084fe9285f7dd392e097da837.png)
Analysing the index.php from the same directory:
![](attachment/139773b299d1df487fd53116ccfca637.png)
it is a salted md5 hash so using mode 20 and arranging hash in the format:

![](attachment/fce0ed2ff5d80af65e413964a4b6fe39.png)
Now we can crack the password:
![](attachment/188278c7289d6d1e0bbfa618b8833f3f.png)
Now logging in through ftp and putting authorized keys to login with ssh. We get a shell as triss.
Then trying to su to jennifer she uses the same password
Now we can access the /backup folder which contains passwd and shadow files:
![](attachment/d24c7fa628719db361496c6e91f9218a.png)
Using unshadow and john:
![](attachment/cb7a78258d0537bf81d0984ee5bb08d0.png)
We find the password sa user.

Now trying to enumerate files owned by user sa
we find `/usr/local/bin/backup.sh`
![](attachment/56e6841542b9b3c213435799cc99a0c3.png)
Adding `chmod +s /bin/bash`
We can see it works:
![](attachment/7c8b82ed91a2447c4453a181974a4523.png)
Now trying to run bash as root user:
`/bin/bash -p`

