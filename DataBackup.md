# Data backup on DKFZ servers using tsm 

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

`emacs /opt/tivoli/tsm/client/ba/bin/dsm.opt`

1. Change the DOMain line so that it reflects your needs: eg /home /d66

You should backup all hard drive with data. Use `df -h` to know which hard drives are directly attached to your computer. 

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

`emacs /opt/tivoli/tsm/client/ba/bin/dsm.sys`

Change the NodeName line so that it reflects your pc name: eg a230-pc84

#### backup.excl

`emacs /opt/tivoli/tsm/client/ba/bin/backup.excl`


6) Install the .deb files:
dpkg -i gskcrypt64-8.0.deb
dpkg -i gskssl64-8.0.deb
dpkg -i TIVsm-API64-7.1.8.deb
dpkg -i TIVsm-APIcit-7.1.8.deb
dpkg -i TIVsm-BA-7.1.8.deb
dpkg -i TIVsm-Bacit-7.1.8.deb

7) Make a log file
cd /var/log/
mkdir tsm
chown -R your_user_name:root tsm
chmod 755 tsm


7) Check the installed libraries:

ldd /usr/bin/dsmc
If some libraries are not found, run
emacs /etc/ld.so.conf.d/tsm.conf
and copy the following lines into the file:
/opt/tivoli/tsm/client/api/bin/
/opt/tivoli/tsm/client/api/bin64/
/usr/local/ibm/gsk8_64/lib64/
/usr/local/ibm/gsk8/lib/

Create the necessary library links 
sudo ldconfig
Test that it is now working 
ldd /usr/bin/dsmc

8) Test if dsmc can connect to the server:
dsmc q sched
It will ask for the node name (your pc name) and the password that the ICTF sent to you.




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


* You might need to import the public key: rpm --import RPM-GPG-KEY-ibmpkg