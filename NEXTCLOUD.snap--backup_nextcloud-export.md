# Nextcloud-snap export

## backup nextcloud-snap using nextcloud export

* nextcloud export
* compress backup
* move compressed backup
* remove old backups from directory

this works fine as weekly automatic (cronjob as root) job and for random quick backup when needed. compressed backup may be moved wherever.

nextcloud-snap backup may be scripted, see example below

---

### rotating nextcloud.export script:

1\. create mount directory for media in `/media` or `/mnt`

2\. mount media using `/etc/fstab` with option `nofail`

3\. snapshots are kept for 30 days

4\. save script in `$USER/bin` as `snapexport.sh`  (replace `$USER` with your user name) 

5\. set preference variables

6\. create root-cronjob for weekly execution ( ``` 0 1 * * 0 su $USER /home/$USER/bin/snapexport.sh ``` ) (replace `$USER` with your user name) 

```
#!/bin/bash
##############################################################

## Nextcloud-snap backup with nextcloud.export -- SCUBA --

##############################################################

## backup directory: '/var/snap/nextcloud/common/backups'
## restore directory: '/var/snap/nextcloud/common'
## backup rotation 30 days
## create crontab as root for automation

##############################################################

# VARIABLES #

##############################################################

SNAPNAME="nextcloud"
BACKUPNAME="mybackup"
TARGET="/media/SNAPBACKUP" ## target directory
SOURCE="/var/snap/nextcloud/common/backups" ## source directory
RETENTION="30" ## file retention in days

##############################################################

# SCRIPT #

##############################################################

## create backup with nextcloud.export ##
sudo nextcloud.export ; ## nextcloud.export, see options

## find and compress backup directory ##
list=`ls $SOURCE`
for i in $list
do
    if [ ! "$i" == "." ]; then
      sudo tar czf ${i}-$BACKUPNAME.tar.gz ${i}
    fi
done

## find and move compressed backup file to $TARGET
sudo find $SOURCE/ -name "*.tar.gz" -exec mv '{}' $TARGET/ \;

## find and rotate/delete old backups
sudo find $TARGET/ -name "*.tar.gz" -mtime +$RETENTION -exec rm -f {} \; 

exit
```

---

## restore export using nextcloud.import

* when moving to new device, be sure to install nextcloud-snap first
* nextcloud.import replaces previous installation incl. DB and data

1\. copy/move compressed backup file to `/var/snap/nextcloud/common`

2\. uncompress backup file in `/var/snap/nextcloud/common`

3\. issue command ` $ sudo nextcloud.import PATH TO DIRECTORY`

---
