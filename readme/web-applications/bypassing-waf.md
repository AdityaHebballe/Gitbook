# 📛 Bypassing WAF

<figure><img src="../../.gitbook/assets/Bypassing WAF (1).png" alt=""><figcaption></figcaption></figure>

This means the access is denied for our host but maybe the localhost can access.

Intercept the request:&#x20;

<figure><img src="../../.gitbook/assets/Bypassing WAF-1.png" alt=""><figcaption></figcaption></figure>

Now we can use `X-Forwarded-For` Header to spoof the localhost:&#x20;

<figure><img src="../../.gitbook/assets/Bypassing WAF-3.png" alt=""><figcaption></figcaption></figure>
