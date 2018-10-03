# Problems

## Current problems


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