# Enumeration
- We use nmap to scan using `nmap -p- -A -T4 -Pn 10.10.10.75`![](attachment/1c482b2e9c037c752482fee99822ed7b.png)
- We found an Apache server running:![](attachment/3d9d8e8357fdca6d5714acf5b6c84126.png)
- Using dirbuster found this:![](attachment/23e297613a7f5f5cb7684ff06e09bac1.png)indicates poor hygiene
- Upon inspect element found we found a hidden comment: ![](attachment/b0f43be05c67844a7114c0123844007e.png)
- Leads to a nibbleblog website where we find the following using dirbuster:![](attachment/e1b068c7df278378ff2efe21e2a57dbb.png)
- Just attempting *username*:admin *password*:nibbles gets us logged in
- We find that the version used is 4.0.3:![](attachment/efa8b1eb248900bfc00ba70366f540e2.png) 
	Which can be exploited to get remote code execution.
- using metasploit we can use the exploit `exploit(multi/http/nibbleblog_file_upload)` to get access to the machine:![](attachment/f637571f11ca72e8f3238c5ede425c1d.png)
- **Note**: The `history` command lets us view the previous commands executed by the user.
- `sudo -l` lets us know the allowed commands for a user:![](attachment/6669e776df2c1381e552acbc39a15ad9.png)
We notice that monitor.sh can be run as sudo so we create a monitor.sh as following:![](attachment/a399d5a858f70ef26dc062d5afcc2772.png)
This creates a bash interactive shell with root privileges like so:
![](attachment/4d89443847bae20f29f8ca0433169729.png)
- We have successfully pwned the machine!