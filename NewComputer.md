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

Go to system settings/network/network-proxy and add ```www-int2.inet.dkfz-heidelberg.de 80``` to the http and https proxy line.

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
Acquire::http::proxy "http://www-int2.inet.dkfz-heidelberg.de:80/";
Acquire::https::proxy "http://www-int2.inet.dkfz-heidelberg.de:80/";
Acquire::ftp::proxy "ftp://www-int2.inet.dkfz-heidelberg.de:80/";
```

Notice that the `https::proxy` is set to `http:` address.

## Install git and automake

```
sudo apt-get update
sudo apt-get install git-core
sudo apt-get install automake
```

## Set git credential to pull from bitbucket without password


```
git config --global http.proxy www.inet.dkfz-heidelberg.de:80
git config --global user.email "allen@uni-heidelberg.de"
git config --global user.name "Kevin Allen"
git config --global credential.helper store
```
Next time you enter your password, it will save in a file in your home directory and will be used later.
Make sure only you have access to this file.

## Get a few repositories
```
cd ~
mkdir repo
cd repo
git clone https://kevin_allen@bitbucket.org/kevin_allen/allen_lab_utilities.git
git clone https://kevin_allen@bitbucket.org/kevin_allen/allen_mouse_electrophysiology.git
git clone https://kevin_allen@bitbucket.org/kevin_allen/relectro.git
```

## Set auto mount and exportfs, update, etc.
Run newComputerSetup.sh
```
cd ~/repo/allen_lab_utilities/src/shell_scripts
sudo ./newComputerSetup.sh
```
This should install several packages, set up automount to access the database, etc.

You can edit data/maxKlustakwikProcesses manually to set the correct number of Klustakwik processes you want to run on the computer.

## Install allen_mouse_electrophysiology
```
cd ~/repo
cd allen_mouse_electrophysiology
./autogen.sh
./configure
make
sudo make install
cd ..
```

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
./updateRepos.sh
sudo ./updateAutoMountExports.sh
```

Test if this worked by trying to visit a mounted directory.
```cd /adata/projects```

## Adjust the crontab

You might want to remove some entries from the crontab depending on the use of the new computer. You can comment out a line with ```#```

```crontab -e```

## Update R to the latest version

```
sudo apt-key adv --keyserver keyserver.ubuntu.com   --keyserver-options http-proxy=http://www-int2.inet.dkfz-heidelberg.de:80    --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
```

Edit /etc/apt/sources.list 
```
sudo emacs /etc/apt/sources.list
```
Add this line for R 3.6 on Ubuntu 18

```
deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran35/
```

Add this line instead if you want R 4.0 on Ubuntu 18

```
deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/
```

Update and install 

```
sudo apt update
sudo apt-get install r-base
sudo apt-get install r-base-dev
```

## Install relectro
```
cd ~/repo
R CMD build relectro
R CMD INSTALL relectro
```


## Install Rstudio
Go to [Rstudio](https://www.rstudio.com/products/rstudio/download/)
Download the version for your Ubuntu version via the installer for supported platforms.

Open Rstudio and try to install a package. For example, try to install tidyverse (this takes 10 minutes).

```
install.packages("tidyverse")
```

If you can't install.packages(), you might need to configure R to use a proxy. 

```
emacs ~/.Renviron
```
Add these 2 lines
```
http_proxy=www-int2.inet.dkfz-heidelberg.de:80
https_proxy=www-int2.inet.dkfz-heidelberg.de:80
```


## Anaconda Python

To get all you need to get started with python, download the latest from [anaconda](https://www.anaconda.com/).

This will download a shell script.
```
chmod u+x Anaconda3-2019.10-Linux-x86_64.sh 
./Anaconda3-2019.10-Linux-x86_64.sh 
```


## ssh to the computer without a password

If you plan to ssh often a computer, you might want to give it a ssh key so you don't have to give a password every time you log in.
```
ssh-keygen
ssh-copy-id kevin@a230-pc49
```
## c++ mode in emacs for .cu files
Add the following to your ~/.emacs file
```
(add-to-list 'auto-mode-alist '("\\.cu\\'" . c++-mode))
```

## NVIDIA graphics card drivers

It is important to use a Cuda version that works with a tensorflow version that works with deeplabcut. As of January 2020, I used cuda 10.

I followed the instructions found on the [tensorflow website](https://www.tensorflow.org/install/gpu#ubuntu_1804_cuda_101)

You will need to reboot to have the graphics card driver active.

Then install this
```
sudo apt-get install nvidia-settings
```


Useful commands
```
lspci | grep -i --color 'vga\|3d\|2d'
lsmod
nvidia-settings
```

You will need to setup some environment variables to compile c++ programs using CUDA.
```
emacs ~/.profile
```
Add the following
```
export PATH=$PATH:/usr/local/cuda-10.1/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-10.1/lib64
```

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

## Arduino

Download the latest Arduino IDE version from the website. 
[website](https://www.arduino.cc/en/Main/Software)

```
cd ~/Downloads
tar xvf arduino-1.8.12-linux64.tar.xz
sudo mv arduino-1.8.12 /opt
cd /opt/arduino-1.8.12/
sudo ./install.sh
```
On one computer, I had to set up the proxy manually to download library.
1. Got to File/Preferences/Network
2. Select Manual proxy configuration and HTTP, add www-int2.inet.dkfz-heidelberg.de as Host name and 80 as Port number.


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
```