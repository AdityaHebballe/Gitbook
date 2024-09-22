# Credential Hunting

### Find password files:

```
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

Sensitive IIS information such as credentials may be stored in a _web.config_ file. For the default IIS website, this could be located at `C:\inetpub\wwwroot\web.config`, but there may be multiple versions of this file in different locations, which we can search for recursively

### Dictionary Files

If a password is saved to dictionary to avoid red underlines:

```
gc 'C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password
```

interchange _password_ for any keyword

### Unattended Installation Files

Check for _unattend.xml_

### PowerShell History File

Starting with Powershell 5.0 in Windows 10, PowerShell stores command history to the file:

```
C:\Users\<username>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt.
```

To check history path:

```
gc (Get-PSReadLineOption).HistorySavePath
```

To check all history files:

```
foreach($user in ((ls C:\users).fullname)){cat "$user\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt" -ErrorAction SilentlyContinue}
```

### Powershell creds

We can obtain cleartext passwords from _encrypted.xml_

```
$credential = Import-Clixml -Path 'C:\scripts\pass.xml'
$credential.GetNetworkCredential().username
$credential.GetNetworkCredential().password
```

### Check Sticky Notes

People sometimes save passwords in sticky notes: Located at&#x20;

```
C:\Users\<user>\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite 
```

<figure><img src="../../../.gitbook/assets/Credential Hunting.png" alt=""><figcaption></figcaption></figure>

Open it using [DB Browser for SQLite](https://sqlitebrowser.org/dl/) And use commands such as `select Text from Note;.` Can also be opened using powershell

Can also use `strings plum.sqlite-wal` on linux.

## Files of Interest

```
%SYSTEMDRIVE%\pagefile.sys
%WINDIR%\debug\NetSetup.log
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software, %WINDIR%\repair\security
%WINDIR%\iis6.log
%WINDIR%\system32\config\AppEvent.Evt
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
%WINDIR%\system32\CCM\logs\*.log
%USERPROFILE%\ntuser.dat
%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat
%WINDIR%\System32\drivers\etc\hosts
C:\ProgramData\Configs\*
C:\Program Files\Windows PowerShell\*
```
