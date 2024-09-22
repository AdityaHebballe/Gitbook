# 🖥️ Active Directory

{% embed url="https://wadcoms.github.io/" %}

{% embed url="https://github.com/PowerShellMafia/PowerSploit" %}

{% embed url="https://github.com/61106960/adPEAS" %}

## DNS

{% code title="Kerberos" %}
```bash
dir \\za.tryhackme.com\SYSVOL
```
{% endcode %}

{% code title="NTLM" %}
```bash
dir \\<DC IP>\SYSVOL
```
{% endcode %}

To try to leak dns info:

```bash
nslookup
server <ip>
```

Powershell DNS config:

```powershell
$dnsip = "<DC IP>"
$index = Get-NetAdapter -Name 'Ethernet' | Select-Object -ExpandProperty 'ifIndex'
Set-DnsClientServerAddress -InterfaceIndex $index -ServerAddresses $dnsip
```

## LDAP(389)

```bash
ldapsearch -x -H ldap://<ip> -s base namingcontexts
```

Then use -b to select and then filter,etc ..

Ex:

{% code overflow="wrap" %}
```bash
ldapsearch -x -b "DC=htb,DC=local" -H ldap://10.10.10.161 'objectClass=Person' sAMAccountName
```
{% endcode %}

To use kerberos authentication:

```bash
kinit d.klay
```

{% code overflow="wrap" %}
```bash
ldapsearch -H ldap://dc.absolute.htb -s base -Y GSSAPI -b "cn=users,dc=absolute,dc=htb" "user" "description"
```
{% endcode %}

To get list of users:

{% code overflow="wrap" %}
```bash
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -f2 -d" "
```
{% endcode %}

```bash
./windapsearch.py --dc-ip 172.16.5.5 -u "" -U
```

EXAMPLES:

{% code overflow="wrap" %}
```bash
ldapsearch -x -H ldap://<IP> -D '<DOMAIN>\<username>' -w '<password>' -b "DC=<1_SUBDOMAIN>,DC=<TLD>"
```
{% endcode %}

{% code overflow="wrap" %}
```bash
ldapsearch  -D 'ldap@support.htb' -w 'password' -b "DC=support,DC=htb" -H ldap://support.htb
```
{% endcode %}

## RPC

```bash
rpcclient -U '' -N <ip>
```

Then can do multiple commands:

```
enumdomusers
```

If valid users list is present use this to password spray:

```bash
for u in $(cat valid_users.txt);do rpcclient -U "$u%Welcome1" -c "getusername;quit" 172.16.5.5 | grep Authority; done

```

## WinRM(5985)

```bash
evil-winrm -u user -p password -i <ip>
```

## SMB(445,139)

### CrackMapExec

To check password policy(from kali):

```bash
crackmapexec smb 10.10.10.161 --pass-pol -u '' -p ''
```

To get TGT hashes for users with

For enumeration as it handles proxied traffic better:

```bash
proxychains nmap -n -Pn -F -sV -sT -oA nmap_results -vvv -iL ${target or targets.txt} -T4 --max-retries 1 --max-rtt-timeout 2s --ttl 50ms --open 
```

To use kerberos authentication

{% code overflow="wrap" %}
```bash
KRB5CCNAME=svc_smb.ccache ./smbclient.py -k absolute.htb/svc_smb@dc.absolute.htb -target-ip 10.10.11.181
```
{% endcode %}

