# 📋 Web Application Checklist

* Fuzz first with feroxbuster

{% code overflow="wrap" %}
```
feroxbuster -u http://whatever.com/ -x php -C 404 -A --wordlist '/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt' -B --auto-tune
```
{% endcode %}

* `robots.txt` , `.svn`, `.DS_STORE`
* Try different request type(**POST & GET**) in burp
* Try to analyse website with html2markdown:

```
curl -s http://192.168.219.140:8000/ | html2markdown
```

* Use `cewl` for custom wordlists and `--lowercase` for lowercase letters.
* If apache try shellshock (`cgi-bin`) might be in windows too

```
nmap -sV -p8081 --script http-shellshock --script-args uri=/cgi-bin/user.sh,cmd=echo\;/bin/ls 127.0.0.1
```

* [ ] Try DoNotExist.php, DoNotExist.html, DoNotExist to check if the website 404 message changes . Refer snoopy ippsec.
* [ ] **Always add the DNS address**
* [ ] Check for default passwords
* [ ] Check for headers if any special header required to access vhost
* [ ] Nmap http enum scan: `sudo nmap -p80 --script=http-enum <ip>`
* [ ] In curl use `--data-urlencode` to automaticatlly url encode
* [ ] If _Apache 2.4.49_ then path traversal is vulnerable.
* [ ] phpinfo for path:

<figure><img src="../../.gitbook/assets/Web app checklist.png" alt=""><figcaption></figcaption></figure>

## Git

To find git repo use linpeas or winpeas and to check use `ls -la` or `ls -fo` to look for .git file in the repo.

* If git repo found as a directory:

```
git-dumper
```

* To check the commit log

```
git log
```

* Then check the commit diff:

```
git show 967fa71c359fffcbeb7e2b72b27a321612e3ad11
```

Use [git-extractor](https://github.com/internetwache/GitTools)

## Common Attacks

* [ ] [less-than-greater-than-command-injections.md](less-than-greater-than-command-injections.md "mention")
*   [ ] [path-traversal-and-file-inclusion.md](path-traversal-and-file-inclusion.md "mention")

    * [ ] Check for [phpinfo](https://support.testrail.com/hc/en-us/articles/7146363428372-Running-Phpinfo-diagnostics) if the HTTP server uses PHP
    * [ ] Fuzzing for LFI

    ```
    ffuf -u http://<url>/download?file=FUZZ -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt
    ```

    There are other worldlists to fuzz for LFI as well.
* [ ] [file-upload-attacks.md](file-upload-attacks.md "mention")
* [ ] [idor-insecure-direct-object-references.md](idor-insecure-direct-object-references.md "mention")
* [ ] [sql-injection.md](sql-injection.md "mention") & [Broken link](broken-reference "mention")
* [ ] [xss-cross-site-scripting.md](xss-cross-site-scripting.md "mention")
* [ ] [xxe-xml-external-entity.md](xxe-xml-external-entity.md "mention")
* [ ] [log4shell.md](log4shell.md "mention")
* [ ] Check [abusing-apis.md](abusing-apis.md "mention")
* [ ] Try [custom-wordlist.md](custom-wordlist.md "mention") on the website if login required
* [ ] [bypassing-waf.md](bypassing-waf.md "mention")

## Wordpress

First check plugins

* [ ] To enumerate wordpress plugins

```
wpscan --url http://192.168.50.244 --enumerate p --plugins-detection aggressive 
```

Run normally too otherwise might miss plugins

* [ ] Normally:

```
wpscan --url http://192.168.50.244
```

## Directory Busting

For overall content search: Ferozbuster with —thorough and smart Dirsearch - brings in different stuff. Check _robots.txt_ and _sitemap.xml_ Also try _txt_ and _pdf_ files

* `-f` can cause a ton of false positives
* `-n` stops recursive directory lookups
* `-b` searches for backups; can produce false positives

```
feroxbuster -u http://host.domain.tld:80/ -f -n -C 404 -A -e -S 0 --auto-tune --burp-replay
```

```
feroxbuster -u http://host.domain.tld:80/ -f -n -C 404 -A -e -S 0 --auto-tune --burp-replay --dont-scan Css Js css img js IMG JS Img CSS fonts Fonts master
```

```
feroxbuster -u http://host.domain.tld:80/ -x asp,aspx,html,php,xml,json,txt,log -C 404 -A -e -S 0 --auto-tune --burp-replay
```

```
feroxbuster -u http://host.domain.tld:80/ -C 404 -A -e -S 0 --wordlist '/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt' -B --auto-tune --burp-replay
```

#### Gobuster

```
gobuster dir -u ${url} -w /usr/share/wordlists/dirb/common.txt -t 5
```

#### Curl

`-L` - to follow redirects `-d` - To add json data `-i` - To
