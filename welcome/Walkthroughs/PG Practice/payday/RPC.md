We can get users:
```
enum4linux-ng 192.168.152.39
```
![](../attachment/063e2b7b3080cccee3b471e6b040b806.png)
To put it into a file:
```
rpcclient -U "" 192.168.152.39 -N -c "enumdomusers" | grep -oP '\[.*?\]' | grep "0x" -v | tr -d '[]' > userlist.txt
```
