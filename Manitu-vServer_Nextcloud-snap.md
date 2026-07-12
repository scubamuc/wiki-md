<h1 align="center">Install Nextcloud snap on Manitu vServer</h1>

<p align="center" width="100%">
    <img width="15%" src="https://github.com/user-attachments/assets/e32a41fc-59ad-45db-8bcc-572363f8ab7b" alt="Nextcloud snap"> 
</p>

<h2 align="center">Nextcloud snap requirements</h2>

You have decided to use Nextcloud snap to set up Nextcloud as a safe home for your data, that's great!

Nextcloud snap is a community driven [snap installation](https://snapcraft.io/nextcloud) making [Nextcloud](https://nextcloud.com/) easy to install and simple to maintain. The ideal Nextcloud snap is an "install and forget" Nextcloud instance that works on most architectures and [updates itself](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-Nextcloud-snap-with-Snap#managing-automatic-updates) without requiring administrative skills. Combining Nextcloud with [snapd](https://ubuntu.com/core/services/guide/snaps-intro) makes it a perfect fit for IoT or scalable environments. Snapd is a secure and robust technology which the Nextcloud snap team has embraced. The team packages the latest stable upstream Nextcloud, adds some snap magic and releases that snap package upon testing fresh installs and automated updates, so that the Nextcloud snap community has peace of mind.

However, Nextcloud snap is opinionated. The following recommended components are included and not optional;

- [x] Nextcloud snap uses recommended Apache 
- [x] Nextcloud snap uses recommended MySQL 
- [x] Nextcloud snap uses recommended PHP 
- [x] Nextcloud snap uses recommended Redis 

----

Before getting started be aware of what you expect from your Nextcloud instance and what system requirements have to be met to fulfil your needs. There are various aspects you might consider;
- [x] number of users
- [x] storage & space requirements
- [x] power consumption & efficiency
- [x] network & connectivity
- [x] backup & redundancy
- [ ] etc. ...

Plan your setup. Do some research, read the [docs](https://github.com/nextcloud-snap/nextcloud-snap) and the [wiki](https://github.com/nextcloud-snap/nextcloud-snap/wiki).

> [!IMPORTANT]
> The [Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap) team is neither responsible for [Manitu vServer](https://www.manitu.de/vserver/) nor for issues pertaining to [Manitu](https://www.manitu.de). For Nextcloud issues refer to  [Nextcloud community support](https://help.nextcloud.com).

----
<h2 align="center">Install Nextcloud snap</h2>

### Option 1. manual install

* fire up your [Manitu vServer](https://www.manitu.de/vserver) using **Ubuntu LTS** as OS and make sure 'snapd' is installed,
    * issue command `# apt install snapd`
    * issue command `# snap install nextcloud`
    * issue command `# nextcloud.manual-install <ADMINUSER> <PASSWORD>` (replace with your own adminuser and **secure** password)
    * [configure Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap) manually
    * install apps from Nextcloud App Store
    * done :heavy_check_mark: 

### Option 2. scripted install

* automate Nextcloud snap installation using a shell script
  * fire up you [Manitu vServer](https://www.manitu.de/vserver)
  * copy the bash script to your vServer and make executable `# chmod +x <SKRIPTNAME>`
  * edit and replace `<VARIABLES>`, `<ADMINUSER>` and `<PASSWORD>` in the script
  * execute the script
* complete [HTTPS Lets Encrypt certification](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap#https-encryption-with-lets-encrypt) `# nextcloud.enable-https lets-encrypt`
* set [trusted domain/domains](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap#trusted-domains-configuration) `# nextcloud.occ config:system:set trusted_domains 0 --value="<CLOUD.MY.DOMAIN.TLD>"`
* truncate logs and restart the snap `# truncate -s 0 /var/snap/nextcloud/current/logs/nextcloud.log && snap restart nextcloud`
* open `<CLOUD.MY.DOMAIN.TLD>` in browser, install default apps and enjoy Nextcloud
* install apps from Nextcloud App Store
* done :heavy_check_mark: 

#### Example script: 

> [!WARNING]
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
