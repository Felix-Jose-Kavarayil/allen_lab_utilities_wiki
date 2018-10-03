# Problems

## exportfs fails at boot time

After reboot, the computer does not export correctly. You can see the problem by running
```
/etc/init.d/nfs-kernel-server status
```
Failed to resolve hm001-pc034.inet.dkfz-heidelberg.de

The nfs server is running. The cause is mostly that exportfs is run before a DNS server was set.
```
systemd-resolve --status
```