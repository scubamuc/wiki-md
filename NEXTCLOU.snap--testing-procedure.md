# Nextcloud snap testing procedure

As a member of the [Nextcloud development team](https://github.com/nextcloud-snap) and help out with [support](https://github.com/nextcloud-snap/nextcloud-snap/issues) and [documentation](https://github.com/nextcloud-snap/nextcloud-snap/wiki) as well as [PR testing](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Develop-and-contribute).
To speed up things a little these tests are partially automated using self designed scripts.

## PR testing procedure

## 1. Install Nextcloud snap
+ install Nextcloud snap latest/beta or PR --channel=beta/pr-<number> on testing host

## 2. Manual first start 
+ complete Nextcloud initialisation see first start. this should be done manually and test if "upstream" issues are resolved

## 3. Autotest setup script
+ run [automated configuration script](https://github.com/scubamuc/bash-scripts/blob/scubamuc-wiki/ncsnap-autotest.sh)

## 4. Check logs  
+ check logs or run the [debugging script](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Issue-Debugging-Scripts#debugging-script)

## 5. Test result overview
+ run [Nextcloud-Info-overview](https://github.com/scubamuc/bash-scripts/blob/scubamuc-wiki/ncinfo_en.sh)

Generally thir procedure will take approximately 15-30 Minutes depending on resources and personal practise
