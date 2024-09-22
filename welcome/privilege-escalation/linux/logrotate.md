# ↻ logrotate

* [ ] config: `/etc/logrotate.conf`
  * [ ] To exploit we need following:
    * [ ] we need _write permissions_ on the log file
    * [ ] logrotate must run as a _privileged user or root_
    * [ ] vulnerable versions: _3.8.6, 3.11.0, 3.15.0, 3.18.0_
  * [ ] Check `cat /var/lib/logrotate.status` to force rotate use `-f`
  * [ ] Use this [exploit](https://github.com/whotwagner/logrotten) with payload as and check which option is used in logrotate.conf with `grep "create\|compress" /etc/logrotate.conf | grep -v "#"` and run with `./logrotten -p ./payload /tmp/tmp.log` where tmp.log is a writable log file
