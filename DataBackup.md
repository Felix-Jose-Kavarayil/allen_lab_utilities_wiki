# Data backup

## Configuration files
```
sudo emacs /opt/tivoli/tsm/client/ba/bin/dsm.sys

sudo emacs /opt/tivoli/tsm/client/ba/bin/dsm.opt
```

## Useful commands

Test if dsmc can connect to the server
```
sudo dsmc q sched
```

Check if dsmc is running
```
ps -ef | grep dsm
```

Check for errors
```
cat /var/log/dsmsched.log
cat /var/log/dsmerror.log
```

Test for recent backup activity

```
sudo dsmc q fi
```

Check configuration
```
sudo dsmc q opt 
```

To backup only a directory

```
sudo dsmc selective "/d47/data/processing/mn829/mn829-01092019-1606/"
```

## Confirmation that the backup is working

The best way to test a backup system is to try to get back your file from it.

To check if a file has been backed up

```
sudo dsmc q backup "/d13/data/processing/ka2413/ka2413-121211-0109/ka2413-121211-0109.clu.*"
```
To test if you can get a file back
```
dsmc restore /d13/data/processing/ka2413/ka2413-121211-0109/ka2413-121211-0109.clu -latest /tmp/ka2413-121211-0109.clu
```