# Vulnerable Services

To enumerate installed applications:

```
wmic product get name
```

<figure><img src="../../../.gitbook/assets/Vulnerable Services.png" alt=""><figcaption></figcaption></figure>

Druva inSync is sus.

1. **Enumerate local ports**

```
netstat -ano
```

<figure><img src="../../../.gitbook/assets/Vulnerable Services-1.png" alt=""><figcaption></figcaption></figure>

2\. **Enumerate process ID**

```
get-process -Id 3324
```

<figure><img src="../../../.gitbook/assets/Vulnerable Services-2.png" alt=""><figcaption></figcaption></figure>

To get process name

3. **Enumerate Running Service**

```
get-service | ? {$_.DisplayName -like 'Druva*'}
```

<figure><img src="../../../.gitbook/assets/Vulnerable Services-3.png" alt=""><figcaption></figcaption></figure>
