#Update database

This page describes how the database is updated when a computer or a hard drive is added or removed from the database.

There are only two files that are used to store the organization of the database:

* allen_lab_utilities/data/auto/auto.ext_drives
* allen_lab_utilities/data/computerList

If a computer or hard drive is added or removed, simply update these two files in the repository and do a ```git pull```. The script ```updateDatabase``` will need to run on each computer to modify the configuration of each computer.