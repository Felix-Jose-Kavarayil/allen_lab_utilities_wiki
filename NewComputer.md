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

### Check proxy settings
Check using
```printenv | grep -i proxy```

You might put these lines in `~/.bashrc`

```
export http_proxy=http://www-int2.inet.dkfz-heidelberg.de:80
export HTTP_PROXY=http://www-int2.inet.dkfz-heidelberg.de:80
export https_proxy=http://www-int2.inet.dkfz-heidelberg.de:80
export HTTPS_PROXY=http://www-int2.inet.dkfz-heidelberg.de:80
```

## Create a group called data
The group called data needs to have id = 501 and user needs this group as their main group
```
sudo groupadd -g 501 data
sudo usermod -g data $USER
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

## Create an access token

Before you clone a repository from either GitHub or Bitbucket, you need an access token (not identical to your password).

If you try to clone via password, it says "Support for password authentication was removed on August 13, 2021. Please use a personal access token instead." on GitHub or "403 while accessing" on Bitbucket.

Steps to follow for *GitHub* here:
[Creating a personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token), directly [add a token in your GitHub profile](https://github.com/settings/tokens/new).

Same for *Bitbucket*, there it is called "app password". Mange them on [Bitbucket: app password](https://bitbucket.org/account/settings/app-passwords/).

Use these tokens each for authentication instead of your personal password.
In  the settings for what this token shall serve, it is sufficient to check "repos".

On both Github and Bitbucket website, you can click on "Clone" to display the command to clone.



## Set git credential to pull from bitbucket without password


```
git config --global http.proxy www-int2.inet.dkfz-heidelberg.de:80
git config --global user.email "allen@uni-heidelberg.de"
git config --global user.name "Kevin Allen"
git config --global credential.helper store
```
Next time you enter your password, it will save in a file in your home directory and will be used later.
Make sure only you have access to this file.

## Get a few repositories
```

read -e -p "Enter Your Bitbucket username:" -i "kevin_allen" BBUSERNAME
read -e -p "Enter Your GitHub username:" -i "kevin-allen" GHUSERNAME

cd ~
mkdir repo
cd repo
git clone https://${BBUSERNAME}@bitbucket.org/kevin_allen/allen_lab_utilities.git
git clone https://${BBUSERNAME}@bitbucket.org/kevin_allen/allen_mouse_electrophysiology.git
git clone https://${BBUSERNAME}@bitbucket.org/kevin_allen/relectro.git
git clone https://${BBUSERNAME}@bitbucket.org/kevin_allen/shiny_lab.git
git clone https://${GHUSERNAME}@github.com/kevin-allen/spikeA.git
```

## Update R to the latest version

If you want to use a version of R that is more recent that the one coming with Ubuntu, try this.

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

Or 4.0 on Ubuntu 20
```
deb https://cloud.r-project.org/bin/linux/ubuntu focal-cran40/
```

Update and install 

```
sudo apt update
sudo apt-get install r-base
sudo apt-get install r-base-dev
```

Install the R packages from command line using
`sudo -E Rscript ~/repo/allen_lab_utilities/src/Rscripts/RDependencies.R`. 
This does not need RStudio but root access since the lib path is not user writable.
Additionally, you can install packages individually using  `sudo Rscript -e 'install.packages("tidyverse", repos="https://cloud.r-project.org")'`.



## Set auto mount and exportfs, update, etc.
Run newComputerSetup.sh
```
cd ~/repo/allen_lab_utilities/src/shell_scripts
sudo ./newComputerSetup.sh
```
This should install several packages, set up automount to access the database, etc.

You can edit data/maxKlustakwikProcesses manually to set the correct number of Klustakwik processes you want to run on the computer.

If you have problems installing the R packages. You can do it once you have installed RStudio. Just run the file `~/repo/allen_lab_utilities/src/Rscripts/RDependencies.R`

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
cd ~/repo/allen_lab_utilities/src/shell_scripts
./updateRepos.sh
sudo ./updateAutoMountExports.sh
```

Test if this worked by trying to visit a mounted directory.
```cd /adata/projects```

## Adjust the crontab

You might want to remove some entries from the crontab depending on the use of the new computer. You can comment out a line with ```#```

```crontab -e```


## Install relectro
```
cd ~/repo
R CMD build relectro
R CMD INSTALL relectro
```

If the last command fails due to missing root privileges, you can open RStudio and select "packages", install from zip: select the one create using the build command (probably `relectro_0.0.0.9002.tar.gz`).


## Install Rstudio
Go to [Rstudio](https://www.rstudio.com/products/rstudio/download/)
Download the version for your Ubuntu version via the installer for supported platforms.

Open Rstudio and try to install a package. For example, try to install tidyverse (this takes 10 minutes).

```
install.packages("tidyverse")
```

To have the right permission (wr-wr--r-) when creating files within R, you need to call the Sys.umask() command. We will set this so that it runs every time you start R.

```
emacs ~/.Rprofile
```

Add this line to the file

```
Sys.umask(0002) 
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

If you want to use pip behind a proxy, you might need to do the following
```
pip install --proxy=http://www-int2.inet.dkfz-heidelberg.de:80 [packagename]
```
or set it before
```
export https_proxy=www-int2.inet.dkfz-heidelberg.de:80
echo $https_proxy
```

It may be useful to upgrade all pip packages
First upgrade pip itself
```
pip install --upgrade pip
```
Follow [instructions](https://stackoverflow.com/a/3452888) to update all packages
```
pip list --outdated --format=freeze | grep -v '^\-e' | cut -d = -f 1  | xargs -n1 pip install -U
```


## install phy
It is useful to have this installed on the computer where the data is stored, so that with remote graphical access it can be much faster

Follow [instructions](https://github.com/cortex-lab/phy#installation-instructions)
```
pip install phy --pre --upgrade
```
logout and login again to have phy in the path, check with `which phy`.

If the data was already processed with phy on a different computer, some files are created with "-rw-r--r--" permissions (i.e. no group writing), so you might delete them first using
```
rm -rf .phy && rm -v phy.log
```

Using `ssh -X ...` you can forward the graphical windows to your machine. Login to the remote machine.

Then go ahead using
```
phy template-gui params.py
```
in the corresponding session folder.


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



## Nvidia GPU (install newest version)

remove all nvidia related stuff:
```
sudo apt-get remove --purge '^nvidia-.*'
sudo apt-get remove --purge '^libnvidia-.*'
sudo apt-get remove --purge '^cuda-.*'
```
install appropriate kernel
```
sudo apt-get install linux-headers-$(uname -r)
```
Download newest version
[CUDA Toolkit 12.0 Downloads](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&
Distribution=Ubuntu&target_version=20.04&target_type=deb_local)

Select

* Operating System: Linux
* Architecture: x86_64
* Distribution: Ubuntu
* Version: 20.04
* Installer Type: deb (local)

Follow the instructions there by running the commands.

The public key can be imported as follows
```
sudo apt-key adv --keyserver keyserver.ubuntu.com   --keyserver-options http-proxy=http://www-int2.inet.dkfz-heidelberg.de:80    --recv-keys A4B469963BF863CC
```

Useful commands:

```
nvcc --version
```

```
nvidia-smi
```

```
sudo modprobe nvidia
```


## Installing Matlab (dear god!)

online installation:
It will not work behind a proxy. I used my mobile phone internet via usb

### License:

We have one license (license number (SN) = 40942222). 

To actiavate it on several computers, follow these steps:

First, on a pc with Matlab installed, you can check the license.
```
grep -A4 "INCREMENT MATLAB " ~/.matlab/R*_licenses/*.lic
```

If there is a file (not ending in "deact"), you may deactivate it first by running `/usr/local/MATLAB/R2020b/bin/deactivate_matlab.sh`

* For each computer, you need to activate it individually on the Mathworks page.
* Follow the instructions here: https://de.mathworks.com/matlabcentral/answers/235126-how-do-i-generate-a-matlab-license-file (Option 2: Manually creating a License File).
* License Center: https://www.mathworks.com/licensecenter
* Installieren und aktivieren: Aktuelle Aktivierungen anzeigen
* This is the current overview of activated licenses on the computers:

| Aktivierungsbezeichnung | Host-ID      | Datum der Aktivierung | Betriebssystem |
|-------------------------|--------------|-----------------------|----------------|
| a230-pc59               | 1831BF0DD305 | 09 Jan 2023           | Linux          |
| a230-pc84               | F02F7485E2A3 | 27 Sep 2021           | Linux, Windows |
| a230-pc73               | 244BFE828416 | 20 Okt 2020           | Linux          |

* Aktivieren, um die Lizenzdatei abzurufen / Einen Computer aktivieren
* Use the pc name as label (Aktivierungsbezeichnung)
* transfer the lic file generated
* Run /usr/local/MATLAB/R2020b/bin/activate_matlab.sh on the machine, select offline installation

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

On the Jetson Xavier NX, I had to change `vers=4` to `vers=3` in `/etc/auto.data` and `/etc/auto.ext_drives` to get automount to mount.

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



## neuroscope

From [neurosuite website](http://neurosuite.sourceforge.net/GNULinux.html) download the most recent version of `LibKlustersShared` and `Neuroscope`.

Install them using package manager
```
sudo dpkg -i libklustersshared_2.0.0-bionic_amd64.deb 
sudo dpkg -i neuroscope_2.0.0-bionic_amd64.deb 
```

If this is not working due to missing Qt4, install it (it is not available in the official repos anymore)
Follow the instructions on [askubuntu](https://askubuntu.com/a/1243318).
If `apt update` says that there is a signing key missing for the ppa (external package), try to add it manually from the [ppa site](https://launchpad.net/~rock-core/+archive/ubuntu/qt4/) by looking into `Technical details about this PPA`. Add these lines manually using `sudo nano /etc/apt/sources.list`.
To import the key, fetch the file from `https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x78cb407d3e3d8f94` if the command `sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 78CB407D3E3D8F94` does not work.
These issues might be due to proxy settings, but defining the appropriate config by passing the variables  `http_proxy`/`https_proxy` and using `sudo -E` or temporarily disabling ipv6 did not work either.

Check with `sudo apt --fix-broken install` if dependencies are not resolved yet.

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

## Set the computer time to the dkfz server time

Install chrony

```
sudo apt-get install chrony
```

Change the ntp server to dkfz by editing the conf file

```
sudo nano /etc/chrony/chrony.conf
```

Remove all the "pool" lines and add the following one

```
pool ns.dkfz-heidelberg.de  iburst
```

Enable, start, and restart the chrony.service

```
sudo systemctl enable chrony.service
sudo systemctl start chrony.service
sudo systemctl restart chrony.service
```

[DKFZ NTP time server](https://itcfwiki.inet.dkfz-heidelberg.de/itcfwiki/index.php/Zeitserver) file `/etc/ntp.conf`