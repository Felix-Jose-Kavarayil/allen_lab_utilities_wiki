# Allen lab utilities

This repository contains software that takes care of the database and some automatic steps of data analysis. 

The repository includes a list of computer and hard drives that are part of the network used for data analysis.

There is a script used to set up a computer from scratch. Just follow the instruction for adding a new computer in this wiki. The computer will be automatically updated when this repository changes. So there is no need to go and modify all computers when someone adds a computer or a hard drive.
 
This lab is only used in Kevin Allen's lab for the moment. On each computer, it needs a user called ```kevin``` with a strong password.

We will need to find a solution for the backup of data.


## List of exciting software

* **earlyProcessing.R**: a program to do spike extraction, make position data file (whd), and send spike to clustering.
* **ktanPositrackTrial.sh**: a program to start and stop positrack and test the integrity of the files.
* **addNewComputer**: a script to configure a new computer in the lab.
* **updateAutoMountExports.sh**: a script to update the auto.mount and exportfs configuration from repo
* **updateRepo**: a script to git pull some repository and install the programmes. 
* **KlustaKwikLauncher.sh**: a script to launch KlustaKwik.sh when the resources are available.
* **KlustaKwik.sh**: a script to launch Klustakwik and remove artifacts.

## List of data kept in the repository

* **data/computerList**: This file contains the ip addresses of all computers in the lab.
* **data/auto/ext_drives**: Contains the name and location of the hard drives accessible through the network.
* **data/auto/auto.master, data/auto/auto.ext_drives and data/auto/auto.data **: Additional files used by auto.mount

## Database organization
* [**Adding a new Ubuntu computer**](https://bitbucket.org/kevin_allen/allen_lab_utilities/wiki/edit/NewComputer)
* [**Database description**](https://bitbucket.org/kevin_allen/allen_lab_utilities/wiki/edit/Database)
* [**Updating the database**](https://bitbucket.org/kevin_allen/allen_lab_utilities/wiki/edit/UpdateDatabase)
* [**Problems**](https://bitbucket.org/kevin_allen/allen_lab_utilities/wiki/edit/Problems)