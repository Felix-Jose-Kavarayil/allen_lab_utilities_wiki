#Update database 

This page describes how the database is updated when a computer or a hard drive is added or removed from the database.

## On a single computer

Before you start, get the latest state of the database
```
cd ~/repo/allen_lab_utilities
git pull
```

There are only two files that are used to store the organization of the database:

* allen_lab_utilities/data/auto/auto.ext_drives
* allen_lab_utilities/data/computerList

If you are adding a new computer, you will probably run ```newComputerSetup``` which will add the new ip to ```allen_lab_utilities/data/computerList```. If a new drive is added, put it in ```allen_lab_utilities/data/auto/auto.ext_drives```. 

Once you are done modifying these two files, commit and push the changes.
```
cd ~/repo/allen_lab_utilities
git commit -a -m "adding a new computer (a230-pc55)"
git push
``` 
The changes will now be available for the other computers.

## Update the other computers on the database

When you run ```newComputerSetup``` on a computer, this creates a crontab job that pulls the latest version of allen_lab_utilities and runs ```updateAutoMountExports.sh```. This is done once a day. If you want the changes to take effect immediately, you can always do it manually.

```
cd ~/repo/allen_lab_utilities
git pull
cd src/shell_scripts
sudo ./updateAutoMountExports.sh
```