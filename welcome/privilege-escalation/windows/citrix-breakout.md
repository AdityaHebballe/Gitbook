# Citrix Breakout

Basic Methodology for break-out:

1. Gain access to a Dialog Box.
2. Exploit the Dialog Box to achieve command execution.
3. Escalate privileges to gain higher levels of access. .ica files are used for logging into restricted environments

Try to get access to such dialogs:&#x20;

<figure><img src="../../../.gitbook/assets/Citrix Breakout.png" alt=""><figcaption></figcaption></figure>

Then enter [UNC](https://learn.microsoft.com/en-us/dotnet/standard/io/file-path-formats#unc-paths) path: `\\127.0.0.1\c$\users\pmorgan`

Same technique to access shares: `\\10.13.38.95\share`

In cases where strict restrictions are imposed on File Explorer, alternative File System Editors like Q-Dir or Explorer++ can be employed as a workaround

Or modify existing _shortcut_ files to cmd.exe path. If no existing shortcuts either transer one or create new using powershell(.lnk)

Write a bat file with `cmd` in it.
