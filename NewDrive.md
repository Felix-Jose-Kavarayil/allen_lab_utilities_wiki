# New Hard Drive

## If the hard drive is new

This section is for new hard drives containing no data. If your drive already has data on it, following the instructions below will destroy your data. Instead, go to the "For all drives" section

### Find a name for your drive

By convention, we label our drive d? where ? is an incrementing counter. To know what is the next number to be used do

```cat /etc/auto.ext_drives```

### Create a partition on the drive

Identify your drive on your computer. This is best done by using ```df -h```. Make sure the drive is not plugged in your computer. Run

```df -h```

Plug in your hard drive and run

```df -h```

The drive that was not there the first time and is there this time is your hard drive. If the two outputs are the same, this means your drive is not mounted by the computer and you can use ```dmesg``` to find your drive.

What you are looking for is something like ```/dev/sd?```, where ```?``` is a letter.

You can also try with ```dmesg```.

It is very important that you do not make a mistake at the previous step because you are about to erase what is on whatever drive you identified as your new hard drive.

If this doesn't work either ^^, try as root to find out what name the new block device file (generated at USB plug-in) has:

```fdisk -l```

Use fdisk to delete any old partition on the drive and create a new primary partition.
```
sudo umount /dev/sd?1
sudo fdisk /dev/sd? 
m
d
n
p
1
w
```

Alternatively, simply create a new partition table (GPT for large disks) and create one partition in it (use default values for number, sector start/end)
```
g
n
p
w
```

Format the new partition and label it

In the code below, replace ```/dev/sd?``` by what you used above and ```d?``` is what you identified in the first section. THIS STEP ERASES WHATEVER HAS BEEN ON THIS PARTITION BEFORE!

```sudo mkfs.ext4 /dev/sd?1 -L /d?```




## For all drives

If you have a hard drive with data, you start here.

### ID of the partition

```sudo blkid```

What you need is the UUID of your partition.

### Create a mounting point for the drive

```
sudo mkdir /d??
sudo chown kevin /d??
sudo chgrp data /d??
```

### Edit your fstab

```sudo emacs /etc/fstab```
```
UUID=3b06fdcd-29aa-49f4-b8d5-cefe4c989bd2 /                       ext4    defaults        1 1
UUID=74bb95da-1cc8-4ac8-acc6-b7135b2927e3 /boot                   ext4    defaults        1 2
UUID=85f9baec-d489-4ca8-b540-85df8c98eb22 /home                   ext4    defaults        1 2
UUID=f0b09312-5809-43aa-a19b-9fc02d2cf7d5 swap                    swap    defaults        0 0
UUID=1c90a999-b3a0-4423-ae5a-0cf2eb049d42 /d14			  ext4	  defaults	  0 0
UUID=70d64b22-e443-4a43-ab34-4faf0d8a8823 /d15			  ext4 	  defaults 	  0 0
UUID=1a40cffd-77ad-4a58-b764-8b37417bf608 /d5			  ext3	  defaults	  0 0
UUID=96520c43-0086-4e14-9db7-96e4a1e2863e /d46			  ext4	  defaults	  0 0
```
### Try to mount the drive

If your drive is already mounted, umount it.

You need to find the mounting point and use ```sudo umount mountingPoint```, for example:

```sudo umount /media/kevin/_d31```

Then mount everything that is listed in /etc/fstab

```sudo mount -a```


### Add the drive to the database. 

The official list of hard drives is in a file of the allen_lab_utilities repository. Add your drive to it.

Get the latest version
```
cd ~/repo/allen_lab_utilities
git pull
```

Edit and save the file

```emacs ~/repo/allen_lab_utilities/data/auto/auto.ext_drives```

Update the repository

```
git commit -a -m "adding hard drive d31"
git push
```

### Apply the changes locally

On the computer on which the drive is installed. Run

```
cd ~/repo/allen_lab_utilities/src/shell_scripts/
sudo ./updateAutoMountExports.sh
```

### Test that the drive is automounted in /ext_drives/

```
cd /ext_drives/d??
```
### Change the permissions

The drive should be mounted
```
sudo chown kevin /d??
sudo chgrp data /d??
sudo chmod g+w /d??
```


### Apply changes to other computers

Normally, this should be done automatically overnight.

```ssh``` onto another computer and run

```
cd ~/repo/allen_lab_utilities/src/shell_scripts/
./updateRepos.sh
sudo ./updateAutoMountExports.sh
```

### Set up an automatic backup of your new drive

Follow the instructions [here](https://bitbucket.org/kevin_allen/allen_lab_utilities/wiki/DataBackup).