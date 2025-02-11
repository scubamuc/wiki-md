# Nextcloud snap testing procedure

As a member of the [Nextcloud development team](https://github.com/nextcloud-snap) I help out with [support](https://github.com/nextcloud-snap/nextcloud-snap/issues) and [documentation](https://github.com/nextcloud-snap/nextcloud-snap/wiki) as well as [PR testing](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Develop-and-contribute).

## Setup testing environment per script (optional)

You may want to automate installation for testing, thus maintaining a reproducible environment. No need to do repetitive stuff if you can automate it.

This method enables quick PR testing (_15-30 minutes per test, depending on resources_). It is important to follow exactly the same procedure for every test so that any discrepancies are discovered immediately. Even the smallest deviations could have an impact. The test result should be identical each time it is run.

Assuming you're running your tests behind a [reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/NGINX-proxy-manager)

### 1. Install Nextcloud snap
+ install Nextcloud snap `latest/beta` or PR `--channel=beta/pr-<number>` on testing host

### 2. Manual first start 
+ complete Nextcloud initialisation see first start. this should be done manually and test if "upstream" issues are resolved

### 3. Autotest setup script
+ run [automated configuration script](https://github.com/scubamuc/bash-scripts/blob/scubamuc-wiki/ncsnap-autotest.sh)

### 4. Check logs  
+ check logs or run the [debugging script](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Issue-Debugging-Scripts#debugging-script)

### 5. Test result overview
+ run [Nextcloud-Info-overview](https://github.com/scubamuc/bash-scripts/blob/scubamuc-wiki/ncinfo_en.sh)

#### Example test result
![grafik](https://github.com/user-attachments/assets/5ba2d439-0c83-4215-8927-fe6c1cfe40d1)
