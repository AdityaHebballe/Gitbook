---
icon: server
---

# SSRF

Server-side request forgery is a web security vulnerability that allows an attacker to cause the server-side application to make requests to an unintended location.

In some situations, the SSRF vulnerability might allow an attacker to perform arbitrary command execution. SSRF attacks often exploit trust relationships to escalate an attack from the vulnerable application and perform unauthorized actions.

In an SSRF attack against the server, the attacker causes the application to make an HTTP request back to the server that is hosting the application, via its loop-back network interface.

Example:

```http
POST /product/stock HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 118 stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1
```

An attacker can modify it like this:

```http
POST /product/stock HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 118 
stockApi= http://localhost/admin
```

We can normally access it but admin privileges are given only to authenticated users but since it is coming from a local server it is bypassed.

In some cases, the application server is able to interact with back-end systems that are not directly reachable by users

```http
POST /product/stock HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 118 
stockApi=http://192.168.0.68/admin
```

## Bypassing

### Blacklist

Some applications block input containing hostnames like `127.0.0.1` and `localhost`, or sensitive URLs like `/admin`. In this situation, you can often circumvent the filter using the following techniques:

* Use an alternative IP representation of `127.0.0.1`, such as `2130706433`(32-bit integer), `017700000001`(octal), or `127.1`.
* Register your own domain name that resolves to `127.0.0.1`. You can use `spoofed.burpcollaborator.net` for this purpose.
* Obfuscate blocked strings using URL encoding or case variation.
* Provide a URL that you control, which redirects to the target URL. Try using different redirect codes, as well as different protocols for the target URL. For example, switching from an `http:` to `https:` URL during the redirect has been shown to bypass some anti-SSRF filters.

Example:

```http
http://127.1/%2561dmin
```

### Whitelist

Some applications only allow inputs that match, a whitelist of permitted values.

The URL specification contains a number of features that are likely to be overlooked when URLs implement ad-hoc parsing and validation using this method:

* You can embed credentials in a URL before the hostname, using the `@` character. For example: `https://expected-host:fakepassword@evil-host`
* You can use the `#` character to indicate a URL fragment. For example: `https://evil-host#expected-host`
* You can leverage the DNS naming hierarchy to place required input into a fully-qualified DNS name that you control. For example: `https://expected-host.evil-host`
* You can URL-encode characters to confuse the URL-parsing code. This is particularly useful if the code that implements the filter handles URL-encoded characters differently than the code that performs the back-end HTTP request. You can also try double-encoding characters; some servers recursively URL-decode the input they receive, which can lead to further discrepancies.
* You can use combinations of these techniques together.

### Open Redirection

For example, the application contains an open redirection vulnerability in which the following URL:

`/product/nextProduct?currentProductId=6&path=http://evil-user.net`

returns a redirection to:

`http://evil-user.net`

You can leverage the open redirection vulnerability to bypass the URL filter, and exploit the SSRF vulnerability as follows:

```http
POST /product/stock HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 118 stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin
```

This SSRF exploit works because the application first validates that the supplied `stockAPI` URL is on an allowed domain, which it is. The application then requests the supplied URL, which triggers the open redirection. It follows the redirection, and makes a request to the internal URL of the attacker's choosing.

Clicking on next product:&#x20;

﻿﻿![](app://3af3a0ef6654345ff77d177aeb54c472c642/home/aditya/Documents/Obsidian/Beyond%20OSCP/Web%20Exploitation/Images/SSRF.png?1731387398255)

There is a path parameter.

Now exploiting it in stockApi:&#x20;

﻿![](app://3af3a0ef6654345ff77d177aeb54c472c642/home/aditya/Documents/Obsidian/Beyond%20OSCP/Web%20Exploitation/Images/SSRF-1.png?1731387561674)

## Blind SSRF

Blind SSRF is harder to exploit but sometimes leads to full remote code execution on the server or other back-end components.

The most reliable way to detect blind SSRF vulnerabilities is using out-of-band (OAST) techniques We need to trigger an HTTP request to an external system we control and monitor it.

**NOTE:** It is common when testing for SSRF vulnerabilities to observe a DNS look-up for the supplied Collaborator domain, but no subsequent HTTP request. This typically happens because the application attempted to make an HTTP request to the domain, which caused the initial DNS lookup, but the actual HTTP request was blocked by network-level filtering. It is relatively common for infrastructure to allow outbound DNS traffic, since this is needed for so many purposes, but block HTTP connections to unexpected destinations.

## Hidden Attack Surface

Many server-side request forgery vulnerabilities are easy to find, because the application's normal traffic involves request parameters containing full URLs. Other examples of SSRF are harder to locate.

### Partial URL in requests

Sometimes, an application places only a **hostname** or **part** of a URL path into request parameters. The value submitted is then incorporated server-side into a full URL that is requested.

### URLs within data formats

Some applications transmit data in formats with a specification that allows the inclusion of URLs that might get requested by the data parser for the format. An obvious example of this is the XML data format, which has been widely used in web applications to transmit structured data from the client to the server. XXE Injection is also possible in this case.

### SSRF via the Referer header

Some applications use server-side analytics software to tracks visitors. This software often logs the Referer header in requests, so it can track incoming links. Often the analytics software visits any third-party URLs that appear in the Referer header
