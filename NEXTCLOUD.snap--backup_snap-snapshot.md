# Nextcloud snap snapshot

## Backup, restore or migrate Nextcloud snap using snap snapshots
Snapshots of Nextcloud snap are created with the snap snapshot utility. This works for random snapshots or as an automated backup script/cronjob and has the added convenience of easy Nextcloud snap migration to a new device when needed. 

Snap snapshot will backup the entire snap including configuration, apps, certificates, database and data into a compressed file (*.zip) in `/var/lib/snapd/snapshots`

[documentation](https://snapcraft.io/docs/snapshots)

### Procedure

 1. **Stop Nextcloud** (optional but recommended)
```
sudo snap stop nextcloud
```
 2. **Create snapshot**
```
sudo snap save nextcloud
```
 3. **Start Nextcloud** (see step 1.)
``` 
sudo snap start nextcloud#
```
 4. **Find snapshot** (`*.zip`) in `/var/lib/snapd/snapshots` and copy/move to wherever

This works fine as weekly automatic cronjob as root and has the added convenience of easy Snap transfer to different server when needed. thus always 4 weeks of Snap snapshots on backup media.

Snap snapshots may be scripted, see example below

---

### Rotating Nextcloud snap snapshots script to media incl. logging:

1\. create mount directory for media in `/media` or `/mnt`

2\. mount media using `/etc/fstab` with option `nofail`

3\. snapshots are kept for 30 days

4\. save script in `$USER/bin` as `snapsnapshot.sh`

5\. set preference variables

6\. create root-cronjob for weekly execution ( ``` 0 1 * * 0 su $USER /home/$USER/bin/snapsnapshot.sh ``` )

```bash
#!/bin/bash
##############################################################
# Script description  -scubamuc- https://scubamuc.github.io/ #
# Nextcloud-snap backup with Snap snapshot
##############################################################
## create target directory "sudo mkdir /media/SNAPBACKUP"
## snapshot rotation 30 days 
## create crontab as root for automation
## 0 1 * * 0 su $USER /home/$USER/bin/snapsnapshot.sh
##############################################################
# VARIABLES #
##############################################################

SNAPNAME="nextcloud"
TARGET="/media/SNAPBACKUP"  ## target directory
LOG="$TARGET/snapbackup-nc.log"  ## logfile
SOURCE="/var/lib/snapd/snapshots" ## source directory
RETENTION="30" ## retention in days

##############################################################
# FUNCTIONS #
##############################################################

## Timestamp for Log ##
timestamp()
{
 date +"%Y-%m-%d %T"
}

##############################################################
# SCRIPT #
##############################################################

## must be root, enter sudo password for manual snapshot
## sudo pwd

## start log  
 echo "********************************************************" >> "$LOG" ; ## log seperator
 echo "$(timestamp) -- Snapbackup "$SNAPNAME" Start" >> "$LOG" ; ## start log

## optional stop snap for snapshot  
 sudo snap stop "$SNAPNAME" ;
## create snap snapshot 
 sudo snap save --abs-time "$SNAPNAME" ;
## optional if stopped restart snap after snapshot 
 sudo snap start "$SNAPNAME" ;

## find snapshot file in $SOURCE and move to $TARGET  
 sudo find "$SOURCE"/ -maxdepth 1 -name "*.zip" -exec mv {} "$TARGET"/ \; # find and move
## find old snapshots and delete snapshots older than $RETENTION days
 sudo find "$TARGET"/ -name "*.zip" -mtime +"$RETENTION" -exec rm -f {} \; # find and delete

## end log 
 echo "$(timestamp) -- Snapbackup "$SNAPNAME" End " >> "$LOG" ; ## end log 
 echo "" >> "$LOG" ;  ## log linefeed 

exit

```

---

## Restore snapshot using snap restore 

> * When moving to new device, be sure to install Nextcloud snap first
> * Snap restore replaces previous installation incl. certs, DB and data
> * See documentation in `snap restore --help` and [snapcraft](https://snapcraft.io/docs/snapshots#heading--restoring)

### Procedure

1. **Copy snapshot** (`*.zip`) from backup media to `/var/lib/snapd/snapshots`

2. **Discover snapshot-ID** using `snap saved`

3. **Restore snapshot** 
```bash
sudo snap restore "snapshot-ID"
```

---
