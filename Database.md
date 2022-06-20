#Database description

## Structure
The database is made up of hard drives located on computers that are part of the database. The list of computer is in the file `data/computerList`. The list of hard drives is in `data/auto/auto.ext_dirves`. So the data are distributed on several computers, but access is possible from the other computers of the network.

One computer acts as a server for a few key directories. You can find which one it is by looking into the file `/data/auto/auto.data`. Here is the list of directories

* `/adata/electro/`: link to all mice that are in the database
* `/adata/histo/`: histology of all mice
* `/adata/projects/`: link to directories in which you can save data generated during your analysis. You should also have a link there to all animals that are part of the project
* `/adata/run/`: directory used to list task to do on the database

These directories will appear on each computer because auto.mount will mount the hard drive in the background for you. 

##

## Update or set symbolic link of mouse data manually
In `/adata/electro`, run `ln -sfn /ext_drives/dXX/data/electro/xy1234 xy1234` where dXX is the disk and xy1234 is the mouse name. Check with `ll /adata/electro` to list all destinations of symbol link or `realpath .` in the appropriate folder.