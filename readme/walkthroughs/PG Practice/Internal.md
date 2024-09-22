![](attachment/1823c8c7fe1d066048dc7573582f58a1.png)
We have windows 8 here

# SMB

Enumerating SMB:
```
sudo nmap --script smb-vuln* 192.168.180.40
```
![](attachment/22f8cf270bfa91d5ed00a93ee67edbd7.png)

Using metasploit:
![](attachment/97e690c57f6db36597d86c7f525378ee.png)

We get a shell:
![](attachment/091b8c868130729d6389b8be616f7058.png)

