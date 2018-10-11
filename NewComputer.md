# Adding new ubuntu computer

## Get an ip address 

Before you begin, you need an ip address for the computer. In the dkfz, you can order one via the intranet.

Go [here](https://intranet.dkfz-heidelberg.de/English/service-support/computer_support/itcf_userportal/Pages/default.aspx)

And click on Network/IP address.

You will receive an email when your PC has been registered. Do not start the installation process before having internet access. You will not be able to go very far.


## Install Ubuntu
Download the latest LTS Ubuntu from the official website. Use the desktop developer version. Burn the image on a bootable DVD.

Boot the computer from the DVD and follow the instructions.

Create a ```kevin``` user with a secure password.

As a computer name, use the Hostname given by the dkfz.

Reboot the computer, open a terminal.

## Proxy settings part 1

### Firefox

To have access to internet in Firefox, go to Preferences, Advance, Network, Settings... and select Auto-detect proxy settings for this network.

You should have access to internet now. If this is not the case, you need to sort this out before going to the next steps.

### General via gui

Go to system settings/network/network-proxy and add "www.inet.dkfz-heidelberg.de 80" to the http and https proxy line.

## Create a group called data
The group called data needs to have id = 501 and user needs this group as their main group
```
sudo groupadd -g 501 data 
sudo usermod -g data "USER"  
```
## Set correct permission to files you create
```
echo umask 0002 >> ~/.bashrc
```
You will need to log out and log back in so that the change take effect.

## Reboot the computer
Log in as ```kevin```


## apt-get proxy

Create or edit /etc/apt/apt.conf
```
sudo gedit /etc/apt/apt.conf
```
Add this line
```
Acquire::http::proxy "http://www.inet.dkfz-heidelberg.de:80/";
```

## Install git

```
sudo apt-get update
sudo apt-get install git-core
```

## Set git credential to pull from bitbucket without password

This will change ~/.gitconfig.

```
sudo apt install libgnome-keyring-dev
cd /usr/share/doc/git/contrib/credential/gnome-keyring
sudo make
git config --global http.proxy www.inet.dkfz-heidelberg.de:80
git config --global user.email "allen@uni-heidelberg.de"
git config --global user.name "Kevin Allen"
git config --global credential.helper /usr/share/doc/git/contrib/credential/gnome-keyring/git-credential-gnome-keyring
```
Next time you enter your password, it will save it for later.

## Get a few repositories
```
cd ~
mkdir repo
cd repo
git clone https://kevin_allen@bitbucket.org/kevin_allen/allen_lab_utilities.git
git clone https://kevin_allen@bitbucket.org/kevin_allen/allen_mouse_electrophysiology.git
git clone https://kevin_allen@bitbucket.org/kevin_allen/relectro.git
cd allen_mouse_electrophysiology
./autogen
./configure
make
sudo make install
cd ..
cd ~/repo
R CMD build relectro
R CMD INSTALL relectro

```

## Set auto mount and exportfs, update, etc.
Run newComputerSetup.sh
```
cd ~/repo/allen_lab_utilities/src/shell_scripts
sudo ./newComputerSetup.sh
```
This should install several packages, set up automount to access the database, etc.

You can edit data/maxKlustakwikProcesses manually to set the correct number of Klustakwik processes you want to run on the computer.


## Make changes available to other computers
```
cd ~/repo/allen_lab_utilities/
git commit -a -m "adding a computer"
git push
```

The next day, the other computers will have updated their configuration and the computer will be part of the database. 

If you want to do it manually, log on each computer and do
```
cd repo/allen_lab_utilities/src/shell_scripts
./updateRepos
sudo ./updateAutoMountExports.sh
```

Test if this worked by trying to visit a mounted directory.
```cd /adata/projects```


## Install Rstudio
Go to [Rstudio](https://www.rstudio.com/products/rstudio/download/)
Download and Ubuntu software center will take over.
If you can't install.packages(), try to unselect Tools/Global Options.../Packages/Use secure method for HTTP


## Graphics card in the computer, install the proprietary driver for the card

to check what graphic card you have.

```
lspci | grep -i --color 'vga\|3d\|2d'
```

If the card is a Nvidia or AMD card you should try to used the proprietary drivers.
To find information about what driver is currently used you need to the device ID for the next query. 
(The device ID are numbers at the beginning of the last output, in my case they are "01:00.0" but that can differ I suppose) 

```
sudo lspci -v -s 01:00.0
```
Look for "drivers" at the end of the output. If it states: "kernel driver in use: nouveau", try to install the proprietary driver. 

### Install proprietary graphic drivers

Go to Software & Updates under System Setting and select details in the dialogue box. Go to Hardware and/or additional drivers and select the tested proprietary drivers of your hardware.


## Debugging

If you are having trouble automounting your file systems, it may be useful to run automount in the foreground.

Stop the autofs daemon
```
sudo service autofs stop
```
Run automount in the foreground with verbose information
```
sudo automount -f -v
```
From another terminal, try to mount your file-systems by changing directories into the mountpoint.
Check the output from the first terminal for clues as to why the mount failed or was not attempted. 


==== Modify you PATH variable ====

The computer looks in the directory listed in the PATH variable when it looks for programs.
You can personalized this variable to your need in the ~/.profile file (on Fedora .bash_profile).

To have an example from a Fedora computer, 
<code>
ssh a230-pc08 -l kevin
cat ~/.bash_profile
</code>

Once you are done modifying your local .profile file, load it to update to the new PATH.
<code>
source .profile
</code>


## Automatic backup

If there are files stored on the new computer, it might be worth setting the automatic backup with the DKFZ facilities. The instructions are on this page  [[backup|Set the automatic backup on your computer]]. 

## Gain access to data on other computers
The workload for clustering is spread across the network of computers. For this to work, your computer needs to have access to hard drives attached to other computers. You need to add your ip address in the /etc/exports file of all other computers.
To get the list of ip of all other computers
```
ssh hm001-pc005 -l kevin
cat /etc/exports | grep processing
```

Leave the list in one terminal and use a second terminal to ssh into all these computers. You need to make sure the ip address of your new computer is in the /etc/exports of all the other computers. Add your ip at the end of each line in the file with (wr) following it. Then run exportfs -av. So for each id, do the following. If there is no /etc/exports file on one computer, there is nothing to do.
```
ssh compid -l kevin
su
emacs /etc/exports
exportfs -av
exit
exit
```