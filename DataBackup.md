# Data backup on DKFZ servers using tsm 

This procedure was tested on September 6th, 2022, on an Ubuntu 20 computer. Use this protocol instead of the older and more complex one on the [dokuwiki](http://a230-pc46/doku.php?id=backup_ubuntu).

## Register your computer for TSM backup

1. Go to [ICTF User Portal](https://info.dkfz-heidelberg.de/itcf/UserPortal/), log in and ask for TSM backup of your computer (Benutzerservice/Datensicherung, then click on `TSM backup`).
 
![dsm_register.png](https://bitbucket.org/repo/baqxqdz/images/2395493039-dsm_register.png)


## Getting the software

Three days after registering, I received an email with the link to download some software.

```
Sehr geehrte:r Kevin Allen,

ihr Knoten würde aktualisiert. Das neugesetzte Passwort ist "a230a230" und der Backupserver ist EXT_BACKUP.
Unter folgendem Link können sie den Client neu herunterladen.
https://trr1.inet.dkfz-heidelberg.de/ibmsp/linux/deb/

Mit freundlichen Grüßen

das ITCF-Team

Um den aktuellen Status Ihrer Serviceanfrage zu verfolgen, melden Sie sich bitte im Kundenbereich an.

Deutsches Krebsforschungszentrum
Stiftung des öffentlichen Rechts
Im Neuenheimer Feld 280
69120 Heidelberg
Tel:  +49 6221 42-2376
Fax: +49 6221 42-2399
```

* Go to the [link](https://trr1.inet.dkfz-heidelberg.de/ibmsp/linux/deb/) with your browser and download the files. In Chrome, right-click on a file and click `save targtet as...`. Do it for all the files in the folder. 

![deb.png](https://bitbucket.org/repo/baqxqdz/images/742565410-deb.png)


## Instructions for the installation 

In theory, the instructions are in the `howto.dkfz` file. But this will not work as the files we have are .deb instead of .rpm. Try the instructions below.


* `cd ~/Downloads`
* `tar -xvf *-TIV-TSMBAC-LinuxX86_DEB.tar`
* `sudo dpkg -i gskcrypt64_8.0-55.24.linux.x86_64.deb`
* `sudo dpkg -i gskssl64_8.0-55.24.linux.x86_64.deb`
* `sudo dpkg -i tivsm-api64.amd64.deb`
* `sudo dpkg -i tivsm-apicit.amd64.deb`
* `sudo dpkg -i tivsm-ba.amd64.deb`
* `sudo dpkg -i tivsm-bacit.amd64.deb`


## Configuration of the backup system. 

The configuration is done via three text files: `dsm.opt`, `dsm.sys` and `backup.excl`.
These files should be in the folder `/opt/tivoli/tsm/client/ba/bin/`.
We will modify the one provided by the dkfz. 


### Copy the configuration file to their destination

* `sudo cp dsm.opt.dkfz /opt/tivoli/tsm/client/ba/bin/dsm.opt`
* `sudo cp dsm.sys.dkfz /opt/tivoli/tsm/client/ba/bin/dsm.sys`
* `sudo cp backup.excl.dkfz /opt/tivoli/tsm/client/ba/bin/backup.excl`


### Modify the configuration file


#### dsm.opt

`sudo emacs /opt/tivoli/tsm/client/ba/bin/dsm.opt`

You will need to modify the `DOMain` line so that it reflects your needs: eg /home /d66

You should back up all hard drives with data. Use `df -h` to know which hard drives are directly attached to your computer. 

Here is the output of `df -h` on one computer.

```
Filesystem      Size  Used Avail Use% Mounted on
udev            7,8G     0  7,8G   0% /dev
tmpfs           1,6G  2,1M  1,6G   1% /run
/dev/sda6       218G   30G  177G  15% /
tmpfs           7,8G  369M  7,5G   5% /dev/shm
tmpfs           5,0M  4,0K  5,0M   1% /run/lock
tmpfs           7,8G     0  7,8G   0% /sys/fs/cgroup
/dev/loop0      128K  128K     0 100% /snap/bare/5
/dev/loop1       62M   62M     0 100% /snap/core20/1328
/dev/loop2       44M   44M     0 100% /snap/snapd/14978
/dev/loop3      249M  249M     0 100% /snap/gnome-3-38-2004/99
/dev/loop5       66M   66M     0 100% /snap/gtk-common-themes/1519
/dev/loop4       55M   55M     0 100% /snap/snap-store/558
/dev/sda1       511M  4,0K  511M   1% /boot/efi
/dev/sdb1       3,6T  3,2T  226G  94% /d47
/dev/sdc1       3,6T  2,9T  580G  84% /d36
/dev/sdd1       3,6T  2,1T  1,4T  60% /d55
tmpfs           1,6G   24K  1,6G   1% /run/user/125
tmpfs           1,6G   60K  1,6G   1% /run/user/1000
```
I wanted to backup `/d47`, `/d36`, `/d55` and `/`.

I changed the `DOMain line to `* DOMain	/ /d47 /d36 /d55`.


#### dsm.sys

`sudo emacs /opt/tivoli/tsm/client/ba/bin/dsm.sys`

Select the right `DEFAULTServer`

In the email from the ITCF-Team, they informed me that my backup server was `EXT_BACKUP`.

I edited the DEFAULTServer line like this.

`DEFAULTServer		EXT_BACKUP`


Change the NodeName line so that it reflects your pc name: eg a230-pc84

Here is an example
`* NODENAME		a230-pc46`

#### backup.excl

`emacs /opt/tivoli/tsm/client/ba/bin/backup.excl`

I left this file as it was. 


##  Connect to the backup server

`sudo dsmc q sched`

Press enter when asked for the node name and enter the password that the ICTF sent to you by email.

dsmc will store the password for future connections.

You should see an output like this one.

```
IBM Spectrum Protect
Command Line Backup-Archive Client Interface
  Client Version 8, Release 1, Level 13.3 
  Client date/time: 09/06/2022 09:29:36
(c) Copyright by IBM Corporation and other(s) 1990, 2022. All Rights Reserved. 

Node Name: A230-PC46
Please enter your user id <A230-PC46>: 

Please enter password for user id "A230-PC46": 

Session established with server EXT_BACKUP: Linux/ppc64le
  Server Version 8, Release 1, Level 12.114
  Server date/time: 09/06/2022 09:30:29  Last access: 04/08/2022 04:13:07

    Schedule Name: EVENING
      Description: 
   Schedule Style: Enhanced
           Action: Incremental
          Options: -quiet
          Objects: 
         Priority: 5
   Next Execution: 10 Hours and 30 Minutes
         Duration: 3 Hours 
           Period: 
      Day of Week: Monday, Tuesday, Wednesday, Thursday, Friday
            Month: Any
     Day of Month: Any
    Week of Month: Any
           Expire: Never

```

## Install and enable the TSM scheduler

The installation created the `/etc/init.d/dsmcad` link. This is a symbolic link to `/opt/tivoli/tsm/client/ba/bin/rc.dsmcad`.

I ran this command to install System-V style init script links.

```
update-rc.d dsmcad defaults
```

Copy the file dsmcad.service to /etc/systemd/system/

`sudo cp /opt/tivoli/tsm/client/ba/bin/dsmcad.service /etc/systemd/system/`

Check if the daemon is running

`systemctl status dsmcad`

If not running, 

`systemctl start dsmcad`


Enable the dsmcad at startup.

`systemctl enable dsmcad`

Restart your computer and check if the dsmcad is running.

`systemctl status dsmcad`


The output should be 

```
dsmcad.service - "IBM SP Client dsmcad service."
     Loaded: loaded (/etc/systemd/system/dsmcad.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2022-09-06 10:29:47 CEST; 54s ago
    Process: 2118 ExecStart=/usr/bin/dsmcad (code=exited, status=0/SUCCESS)
      Tasks: 3 (limit: 19041)
     Memory: 47.4M
     CGroup: /system.slice/dsmcad.service
             └─2159 /usr/bin/dsmcad

Sep 06 10:29:47 a230-pc46 systemd[1]: Starting "IBM SP Client dsmcad service."...
Sep 06 10:29:47 a230-pc46 systemd[1]: Started "IBM SP Client dsmcad service.".

```

If you get this output, the backup should be running automatically every working day.

You can confirm that a backup was made with the following command.

```
sudo dsmc q fi
```


## Useful commands

### Connect to the server

Test if dsmc can connect to the server
```
sudo dsmc q sched
```

### Check if dsmc is running

```
ps -ef | grep dsm
```
of 

`systemctl status dsmcad`



### Check for errors

```
cat /var/log/dsmsched.log
cat /var/log/dsmerror.log
```

### Test for recent backup activity

```
sudo dsmc q fi
```
### Check if a file was backed up

`sudo dsmc q ba filename`

### To do a full backup now

`sudo dsmc incr`



### Check configuration

```
sudo dsmc q opt 
```

### To backup only a directory

```
sudo dsmc selective "/d47/data/processing/mn829/mn829-01092019-1606/"
```

### Confirmation that the backup is working

The best way to test a backup system is to try to get your file back from it.

To check if a file has been backed up.

```
sudo dsmc q backup "/d13/data/processing/ka2413/ka2413-121211-0109/ka2413-121211-0109.clu.*"
```

To test if you can get a file back.

```
dsmc restore /d13/data/processing/ka2413/ka2413-121211-0109/ka2413-121211-0109.clu -latest /tmp/ka2413-121211-0109.clu
```

## Restore data

For more information on [restore](https://www.ibm.com/docs/en/spectrum-protect/8.1.0?topic=data-command-line-restore-examples)

Restore a file to its original directory

`dsmc restore /home/oraadm/data.txt`

Restore a file to a specified directory

`dsmc restore /home/oraadm/data.txt /tmp/data.txt`

Display a list of active and inactive backup versions of files from which you can select versions to restore

`dsmc restore "/home/oraadmin/*"-pick -inactive`

To restore a file from a specific date, in this case the date is 2020/Dec/26.

`sudo dsmc restore /path/to/my/missing/file -pick -inactive -sub=yes -pitd=12/26/20 /destination/path/`


To restore a directory with the subdirectories

sudo dsmc restore /d47/data/processing/ /media/kevin/_d64/data/processing/ -subdir=yes