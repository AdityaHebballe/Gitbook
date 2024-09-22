# RPC Enumeration

#### To connect to rpc client as anonymous user

```
rpcclient -U "" <ip>
```

#### To enumerate

```
enumdomusers #get users
enumprinters
querydispinfo #users and user info
```

#### To add users to a userlist file:

{% code overflow="wrap" %}
```
rpcclient -U "" <ip> -N -c "enumdomusers" | grep -oP '\[.*?\]' | grep "0x" -v | tr -d '[]' > userlist.txt
```
{% endcode %}

Then spray passwords and try [asreproasting.md](../active-directory/ad-exploitation/asreproasting.md "mention") with kerbrute
