# FTP Enumeration

If ftp acting acoustic**`quote PASV`** to fix ftp or use **`passive`**

**Check for ftp anonyomous access:**

```
ftp <ip>
```

**To bruteforce use hydra**:

{% code overflow="wrap" %}
```
hydra -C /usr/share/seclists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt ftp://<ip>
```
{% endcode %}

Default location of ftp is _**/var/ftp**_ can be useful for exploiting redis like seen in [sybaris](https://medium.com/@vivek-kumar/offensive-security-proving-grounds-walk-through-sybaris-491b23545014)
