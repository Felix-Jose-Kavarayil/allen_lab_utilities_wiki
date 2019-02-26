# Crontab jobs #

The crontab is used to automatically run a program at a specified time or date. Some jobs are added there automatically when you install a new computer. 

To edit: ```crontab -e```

Here is what I have in my user crontab

```
# m h  dom mon dow   command
1 1 * * * tar -cjf /d47/data/dokuwiki-backup.tar /var/www/dokuwiki
0 21 * * * /home/kevin/repo/shiny_lab/src/scripts/feedCheck.sh /adata/run/feedCheck.log 2>&1
1 1 1 * * /home/kevin/repo/shiny_lab/src/scripts/usageReport.sh /adata/run/usageReport.log 2>&1
1,16,31,46 * * * *  /home/kevin/repo/allen_lab_utilities/src/shell_scripts/KlustaKwikLauncher.sh >> /adata/run/clusteringLog
#1,16,31,46 * * * * /home/kevin/repo/allen_lab_utilities/src/shell_scripts/earlyProcessing.sh -s /adata/run/spikeDetectionSessions -c /adata/run/klustakwikTetrodes >> /adata/run/rEarlyProcessingLog 2>&1
0 1,16 * * * /home/kevin/repo/allen_lab_utilities/src/shell_scripts/updateRepos.sh >> /adata/run/updateRepos 2>&1
```

Some jobs need to be run as root and I put them in the root crontab.
To edit: ```sudo crontab -e```
```
# m h  dom mon dow   command
13 4 * * * /usr/bin/dsmc inc
14 11 * * * /home/kevin/repo/allen_lab_utilities/src/shell_scripts/updateAutoMountExports.sh >> /adata/run/updateAutoMountExportsLog
@reboot /home/kevin/repo/allen_lab_utilities/src/shell_scripts/exportfsReboot.sh
```