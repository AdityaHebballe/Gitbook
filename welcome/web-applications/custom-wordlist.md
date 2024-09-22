# 📖 Custom Wordlist

**Html2dic - Build dictionary from html**

You can build a dictionary from a html-page.

```
curl http://example.com > example.txt
```

Then run:

```
html2dic example.txt
```

Then you should probably remove duplicates.

**Cewl - Spider and build dictionary**

```
cewl -w createWordlist.txt https://www.example.com
```

Add minimum password length:

```
cewl -w createWordlist.txt -m 6 https://www.example.com
```

**Improve the custom wordlist** As we all know few password are just simple words. Many use numbers and special characters. To improve our password list we can use john the ripper. We can input our own rules, or we can just use the standard john-the-ripper rules

```
john ---wordlist=wordlist.txt --rules --stdout > wordlist-modified.txt
```

Refer Forest-HTB
