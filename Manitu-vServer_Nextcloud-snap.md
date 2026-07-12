## Install Nextcloud snap on Manitu vServer

* fire up your VPS and make sure snapd is installed, or run `# apt install snapd`
* issue command `# nextcloud.manual-install <ADMINUSER> <PASSWORD>` and continue manually **or**
  * copy the bash script to your VPS and make executable `# chmod +x <SKRIPTNAME>`
  * edit and replace `<VARIABLES>`, `<ADMINUSER>` and `<PASSWORD>` in the script
  * execute the script
* complete [HTTPS Lets Encrypt certification](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap#https-encryption-with-lets-encrypt) `# nextcloud.enable-https lets-encrypt`
* set [trusted domain/domains](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap#trusted-domains-configuration) `# nextcloud.occ config:system:set trusted_domains 0 --value="<CLOUD.MY.DOMAIN.TLD>"`
* truncate logs and restart the snap `# truncate -s 0 /var/snap/nextcloud/current/logs/nextcloud.log && snap restart nextcloud`
* open `<CLOUD.MY.DOMAIN.TLD>` in browser, install default apps and enjoy Nextcloud
* done :heavy_check_mark: 

#### Example script: 

> [!IMPORTANT]
> :warning: **never copy and paste!** without reading the script first :warning:
>
> **read the script** :eyes: *edit* and *replace* `<variables>` & values :exclamation:

```
 #!/bin/bash
#################################################################################
                ## -scubamuc- https://scubamuc.github.io/ ##
## Setup Nextcloud snap testing environment --> VPS                            
## Script assumes instance is a VPS with port 80 and 443 internet facing
## Script assumes you are root on VPS, no sudo required!  
## IMPORTANT: Script needs to be edited for your values --value=<yourvalue>
#################################################################################
## Auto-setup admin user **excluding** recommended apps                            
## IMPORTANT --> enter your adminuser and a SECURE password                    
## replace <ADMINUSER> and <PASSWORD> -- your adminuser and a SECURE password  
#################################################################################
    nextcloud.manual-install <ADMINUSER> <PASSWORD> 
## backup working config.php ##
    cp /var/snap/nextcloud/current/nextcloud/config/config.php /var/snap/nextcloud/current/ ;
## set default phone region edit <GB, DE, IT>##
    nextcloud.occ config:system:set default_phone_region --value="<GB>" ; 
## set http compression (optional) ##
    snap set nextcloud http.compression=true ;
## set trusted domains edit <CLOUD.MY.DOMAIN.TLD>##
    nextcloud.occ config:system:set trusted_domains 0 --value="<CLOUD.MY.DOMAIN.TLD>" ;
## set overwritehostprotocol ##
    nextcloud.occ config:system:set overwriteprotocol --value="https" ;
## disable appapi
    nextcloud.occ app:disable app_api ;
## update database
    nextcloud.occ maintenance:mimetype:update-db ;
## fix missing indices
    nextcloud.occ db:add-missing-indices ;
## fix missing mimetypes
    nextcloud.occ maintenance:repair --include-expensive ;
## set mail address in user profile for admin user ##
    nextcloud.occ occ user:setting <ADMINUSER> settings email "<ADMINUSER>@example.tld>"
## recommend start Lets Encrypt certification using built in service, see Wiki 
    nextcloud.enable-https lets-encrypt ;
## recommend truncate logs and restart the snap see Wiki
    truncate -s 0 /var/snap/nextcloud/current/logs/nextcloud.log ; 
    snap restart nextcloud ;
## ensure Nextcloud snap is up and running with valid certificate.
#################################################################################
```
