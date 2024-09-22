# 80 

Found a website with binary code:
![](attachment/b74e343dc4be01b77dd6c35d49f672a0.png)
![](attachment/5f6c8a1eda77b721e4a4aa8d55f396ed.png)
Challenge accepted
\
# UDP 161

Checking snmp we can find running  processes:
```bash 
snmpwalk -c public -v1 -t 10 192.168.180.42 1.3.6.1.2.1.25.4.2.1.2
```

![](attachment/16c1821c7e1de573375c6df59bf12fd6.png)

Now using this exploit for sendtmail and clamdv:
https://github.com/0x1sac/ClamAV-Milter-Sendmail-0.91.2-Remote-Code-Execution/blob/main/exploit.c

Testing with ping we get receive packets: 
![](attachment/a5cf06fafe5ef7c8fa508c27af49d743.png)

Now we can use a different exploit to get a shell 
https://www.exploit-db.com/exploits/4761

```
perl 4761.pl 192.168.180.42
```
Then connect to it:
![](attachment/5e331cfc6d51b8c45f4191dc93bbd2dd.png)

