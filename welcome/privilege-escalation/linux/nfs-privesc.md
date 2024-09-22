# nfs privesc

Create a user with same uid in our machine:&#x20;

<figure><img src="../../../.gitbook/assets/nfs privesc.png" alt=""><figcaption></figcaption></figure>

Modify `/etc/login.defs` to allow higher UID\_MAX

Then copy bash to the nfs share and give it SUID&#x20;

<figure><img src="../../../.gitbook/assets/nfs privesc-1 (1).png" alt=""><figcaption></figcaption></figure>

If GLIBC error then copy bash from victim and give it SUID
