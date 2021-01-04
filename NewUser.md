# Add new user

*  Go to Systems, Users, unlock the window, Add User..
*  Fill the form.
*  Make the group called data as the main group of the user ```sudo usermod -g data "USER"```. Replace "USER" with the name of the user.  
*  Log out and log in as new user.
*  Change the mask in the .bashrc file ```echo umask 0002 >> ~/.bashrc```
*  Log out and log back in.