# RPC Enumeration

*   [ ] enumerate&#x20;

    `rpcclient -U '' -N <ip>`&#x20;

    then try `enumdomusers`,`querydispinfo`,etc.
* [ ] Only users:

```
rpcclient -U "" <ip> -N -c "enumdomusers" | grep -oP '\[.*?\]' | grep "0x" -v | tr -d '[]' > userlist.txt
```

* [ ] User info can be changed with `setuserinfo`. To change password:

```
setuserinfo christopher.lewis 23 'Admin!23'
```
