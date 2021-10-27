# Problems

## Current problems

### Installation of R packages
Install all R packages as user and not superuser. Otherwise we can't update the packages later own because of permission problems.

Fix: sudo chown kevin -R /home/kevin/R/x86_64-pc-linux-gnu-library/3.4

### Time of computer drifts

a230-pc51 suddenly 18:43 when it is 8:43.

Fix: run ```sudo ntpdate ns.dkfz-heidelberg.de```

## Solved problems

### exportfs fails at boot time

After reboot, the computer does not export correctly. You can see the problem by running
```
/etc/init.d/nfs-kernel-server status
```
Failed to resolve hm001-pc034.inet.dkfz-heidelberg.de

The nfs server is running. The cause is mostly that exportfs is run before a DNS server was set.
```
systemd-resolve --status
```
I added exportfsReboot.sh to root crontab job at reboot.
This will be added by newComputerSetup.sh.

It is also useful to restart autofs in verbose mode: [Debugging Auto Mount Problems](https://help.ubuntu.com/community/Autofs#Debugging_Auto_Mount_Problems). Then try to `cd` into one of the mount points provided in `/etc/auto.master`.