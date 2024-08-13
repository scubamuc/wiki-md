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

> [!TIP]
>An example is the preview generator app which may be install to generate previws for all sorts of media files. 
Installing the app and generating previews takes up space and slows down system increasing cpu load. In such a 
case, the "cached" files will need to be removed from the system drive to free up space. You can safely remove
the `/var/snap/nextcloud/common/nextcloud/data/appdata_occredxxxx/preview/` directory after uninstalling the app. 
Default previews will be regenerated withou the need of the preview generator app taking up much less space.
