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




1) Go to the ICTF User Portal and demand a tsm backup. They will send you a mail with the node name (your pc name) and a password as well as a link to the files you need to install. 

The howto.dkfz file contains installation instructions that cover the next four steps.

2) Go to the folder with the rpm files. Save the tar file on your pc. The following commands need to be executed as root (sudo su) and run the following command after having changed to the directory where the file is downloaded: tar -xvf *-TIV-TSMBAC-LinuxX86.tar (replacing * with the version)

3) Install the following packages:
alien gskcrypt64-8.0.50.66.linux.x86_64.rpm
alien gskssl64-8.0.50.66.linux.x86_64.rpm
alien TIVsm-API64.x86_64.rpm
alien TIVsm-APIcit.x86_64.rpm 
alien TIVsm-BA.x86_64.rpm
alien TIVsm-Bacit.x86_64.rpm

4) You might need to import the public key: rpm --import RPM-GPG-KEY-ibmpkg

5) Create the dsm.opt, dsm.sys and backup.excl files:
emacs /opt/tivoli/tsm/client/ba/bin/dsm.opt
Copy the dsm.opt.dkfz file content into the file. Change the DOMain line so that it reflects your needs: eg /home /d66
emacs /opt/tivoli/tsm/client/ba/bin/dsm.sys
Copy the dsm.sys.dkfz file content into the file. Change the NodeName line so that it reflects your pc name: eg a230-pc84
emacs /opt/tivoli/tsm/client/ba/bin/backup.excl
Copy the backup.excl.dkfz file content into the file.

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