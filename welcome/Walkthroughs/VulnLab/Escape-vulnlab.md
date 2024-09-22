# Enumeration

Using rdesktop we get a display:
![](attachment/68d11d7ff2c9ad5de48baa18cde08c8b.png)

We are in kiosk mode as soon as login so opening edge:
![](attachment/a23a383222648401a4af2dce9a356f41.png)

We have an encrypted password:
![](attachment/d974d2f3ba4399e901df5cf47c34dbc0.png)

Now transfering the profile to windows and open the profile in remote desktop plus and edit it. With the bulletpass![](attachment/c78b1caaac1923b408e52e71cc8013a0.png)
Now use BulletsPassView and view the password:
![](attachment/f7d3c9fb4b49f9799b676a50f8489dd8.png)

But we cant login as we are not in the remote desktop group.

We can try to download cmd.exe from here and rename to msedge
![](attachment/808ed46a220daffcc83fe981a173f569.png)

That spawns a cmd shell.
![](attachment/c142e24a6dd14dfb62f32e419053be31.png)

Since we have the admin password we can use runas to run commands
```
runas /user:admin "cmd.exe"
```

Now we get an admin shell:
![](attachment/0b2acaa66d90f252f25f10b6ad5e8d6d.png)
But we don't have privileges even though we are in administrators group.

To get UAC use:
```
start-process cmd.exe -verb runas
```

Now we get UAC and get an admin shell.


