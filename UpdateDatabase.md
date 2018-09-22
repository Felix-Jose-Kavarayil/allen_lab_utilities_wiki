#Update database

This page describes how the database is updated when a computer or a hard drive is added or removed from the database.

There are only two files that are used to store the organization of the database:

* allen_lab_utilities/data/auto/auto.ext_drives
* allen_lab_utilities/data/computerList

If a computer or hard drive is added or removed, do
````
cd ~/repo/allen_lab_utilities
git pull
````
Edit the two files. 

```git push``` 

The script ```updateDatabase``` should be running from the crontab of each computer and will update the database structure of each computer automatically.