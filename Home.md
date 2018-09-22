# Allen lab utilities

This repository contains software that we are developing in the lab to improve processes. The repository includes shiny apps, a script to install most programs needed when installing a new computer, a script to add a computer to the database, etc.

I will only distribute this repository within my lab for the moment. On each computer, I will have a user called ```kevin``` with a strong password. git will be able to pull the repository from this account only.

## List of exciting software

* **earlyProcessing.R**: a program to do spike extraction, make position data file (whd), and send spike to clustering.
* **ktanPositrackTrial.sh**: a program to start and stop positrack and test the integrity of the files.
* **newRecSession**: a shiny app to create a new recording session in a database.
* **addNewComputer**: a script to configure a new computer in the lab.
* **updateDatabase**: a script to update the configuration on single computers when a new computer or hard drive is added.
* **KlustaKwik4b.sh**: a script to launch KlustaKwik2b when the resources are available.
* **KlustaKwik2b.sh**: a script to launch Klustakwik and remove artifacts.

## List of data kept in the repository

* **computerList**: This file contains the ip addresses of all computers in the lab.
* **hardDrives**: Contains the name, location of the hard drives accessible through the network.
* **auto.auto, auto.ext_drives, auto.data**: Files used by auto.mount

## Database organization
* [**Adding a new Ubuntu computer**](https://bitbucket.org/kevin_allen/allen_lab_utilities/wiki/edit/NewComputer)
* [**Database description**](https://bitbucket.org/kevin_allen/allen_lab_utilities/wiki/edit/Database)
* [**Updating the database**](https://bitbucket.org/kevin_allen/allen_lab_utilities/wiki/edit/UpdateDatabase)