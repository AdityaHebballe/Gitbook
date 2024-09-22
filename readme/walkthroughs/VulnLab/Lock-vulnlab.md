```
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Lock - Index
| http-methods: 
|_  Potentially risky methods: TRACE
445/tcp  open  microsoft-ds?
3000/tcp open  ppp?
| fingerprint-strings: 
|   GenericLines, Help, RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Content-Type: text/html; charset=utf-8
|     Set-Cookie: i_like_gitea=22d7f7046c315183; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=py_Ez-k9JVaDfFdcFPOngcYRogA6MTcxMzYzNDg1NjY2ODg1MjcwMA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Sat, 20 Apr 2024 17:40:56 GMT
|     <!DOCTYPE html>
|     <html lang="en-US" class="theme-auto">
|     <head>
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <title>Gitea: Git with a cup of tea</title>
|     <link rel="manifest" href="data:application/json;base64,eyJuYW1lIjoiR2l0ZWE6IEdpdCB3aXRoIGEgY3VwIG9mIHRlYSIsInNob3J0X25hbWUiOiJHaXRlYTogR2l0IHdpdGggYSBjdXAgb2YgdGVhIiwic3RhcnRfdXJsIjoiaHR0cDovL2xvY2FsaG9zdDozMDAwLyIsImljb25zIjpbeyJzcmMiOiJodHRwOi8vbG9jYWxob3N0OjMwMDAvYXNzZXRzL2ltZy9sb2dvLnBuZyIsInR5cGUiOiJpbWFnZS9wbmciLCJzaXplcyI6IjU
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Allow: HEAD
|     Allow: HEAD
|     Allow: GET
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Set-Cookie: i_like_gitea=1849576a481e700f; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=6Tw9yRNNM32FP0UJc3_4iFV1sAI6MTcxMzYzNDg2MjM2ODcyMzkwMA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Sat, 20 Apr 2024 17:41:02 GMT
|_    Content-Length: 0
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=Lock
| Not valid before: 2023-12-27T14:19:36
|_Not valid after:  2024-06-27T14:19:36
| rdp-ntlm-info: 
|   Target_Name: LOCK
|   NetBIOS_Domain_Name: LOCK
|   NetBIOS_Computer_Name: LOCK
|   DNS_Domain_Name: Lock
|   DNS_Computer_Name: Lock
|   Product_Version: 10.0.20348
|_  System_Time: 2024-04-20T17:42:22+00:00
5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows


```

There is a website on port 80:
![](attachment/a93c7ebe26009b82766a81bfdc76b7d9.png)

There is a website on port 3000:
![](attachment/03dd77fa3776819e9f1657af86c3d47a.png)

There is a repo with a script:
![](attachment/2cfc2e18c49f92716bded6d66871f23f.png)
Checking commits:
![](attachment/0123bd1273fbb6e742c5aa56f45f23de.png)
We get the access token

Now we can list repositories:
```
curl -H "Authorization: token 43ce39bb0bd6bc489284f2905f033ca467a6362f" "http://10.10.108.22:3000/api/v1/user/repos" | jq
```
![](attachment/c8db88c521616fd381e7c676b30cb243.png)
We get a private repository

Now since we have the access token we might be able to clone it

Using username as `ellen.freeman` and password as the token:
![](attachment/7cf14eb0260ddc08025f6f8c0322b556.png)

Now we have access to the *website* repository
Reading the readme.md:
![](attachment/4d785d7fec3ebdc9713aca260088488b.png)

Any changes we make will be deploy:
Now uploading a [webshell](https://github.com/xl7dev/WebShell/blob/master/Aspx/ASPX%20Shell.aspx)
```
git add shell.aspx
git commit -am "Shell added
git push
```
Now we can access the webshell:
![](attachment/2a94241237643e0dccc646ffcfb044c2.png)

Now getting a reverse shell and checking documents folder we find config.xml for *mremoteNG* 
Now we can decode the password:
![](attachment/2079fa5f3f7225b60e5b2e8c8ed470d4.png)

Using mremoteng_decrypt:
![](attachment/86f6abe3395329ff6dd9eabd590eef5d.png)

Now on desktop:
![](attachment/56d0c0380696d8b3516eecea9b324e55.png)
We have PDF24 toolbox

And checking hidden folder `_install` on c drive we find the msi file:
![](attachment/9cb6f4a4079c948c8c5e636c75c5f9d6.png)

Then following the privesc from https://sec-consult.com/vulnerability-lab/advisory/local-privilege-escalation-via-msi-installer-in-pdf24-creator-geek-software-gmbh/
We get a system shell.
