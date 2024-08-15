# Database, Apps and Files maintenance

at times it may be nesessary to tidy up files, apps and the database in your Nextcloud snap.
Especially when some third party app has caused issues with the snap and left a "mess" behind. 
These commands in sequence will free up space and shrink the database, see [comment](https://github.com/nextcloud-snap/nextcloud-snap/issues/2758#issuecomment-2143605231)


## rescan and cleanup  

### 1. repair database tree
```bash
sudo nextcloud.occ files:repair-tree
```

### 2. rescan files for all users
```bash
sudo nextcloud.occ files:scan --all
```

### 3. rescan appdata
```bash
sudo nextcloud.occ files:scan-app-data
```

### 4. cleanup orphaned entries
```bash
sudo nextcloud.occ files:cleanup
```
## Known issues, 'preview generator app'

> [!NOTE]
> The 'preview generator app' which is sometimes installed manually to generate previews for all sorts of media files gobbles up disk space blowing up the database and increasing CPU load. The "cached" files will want to be removed from the system drive to free up space. You can safely delete the `/var/snap/nextcloud/common/nextcloud/data/appdata_occredxxxx/preview/` directory **after uninstalling** the 'preview generator app'. The file previews will be regenerated with 'default nextcloud previews' without requiring the 'preview generator app' saving disk space and CPU load.

> [!IMPORTANT]
> Be patient, regenerating default previews may take a while depending on system resources, files quantity or external media to be scanned!

----

# Database, clear 'undo' history

Nextcloud MySQL keeps an 'undo' history (`temp_undo_00x.ibu`) which may become duplicated and grow over time gobbling up disk space. The MySQL 'undo' files in `/var/snap/nextcloud/current/mysql` may needed clearing...

> [!WARNING]
> This is like open heart surgery... be **safe** and **backup** your database!
> ```
> sudo nextcloud.export -d
> ```

> [!CAUTION]
> Access the database with the mysql client:
> ```bash 
> sudo nextcloud.mysql-client
>```
>
> and run the below commands
> 
> ```bash
> use nextcloud;
> 
> CREATE UNDO TABLESPACE temp_undo_003 ADD DATAFILE 'temp_undo_003.ibu';
> 
> ALTER UNDO TABLESPACE innodb_undo_001 SET INACTIVE;
> SELECT NAME, STATE FROM INFORMATION_SCHEMA.INNODB_TABLESPACES WHERE NAME = 'innodb_undo_001';
> ALTER UNDO TABLESPACE innodb_undo_001 SET ACTIVE;
> 
> ALTER UNDO TABLESPACE innodb_undo_002 SET INACTIVE;
> SELECT NAME, STATE FROM INFORMATION_SCHEMA.INNODB_TABLESPACES WHERE NAME = 'innodb_undo_002';
> ALTER UNDO TABLESPACE innodb_undo_002 SET ACTIVE;
> 
> ALTER UNDO TABLESPACE temp_undo_003 SET INACTIVE;
> DROP UNDO TABLESPACE temp_undo_003;
> ```




