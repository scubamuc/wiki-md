# Apparmor profile `syslog` warnings

In some cases **apparmor="DENIED"** `syslog` warnings are "complaining" about failing Apparmor audits. These warnings are nothing to worry about since snapd is doing what it should and **confining** the snap!

```
audit: type=1400 audit(1732273383.121:15114): apparmor="DENIED" operation="ptrace" namespace="root//lxd-NEXTCLOUD_<var-snap-lxd-common-lxd>" profile="snap.nextcloud.nextcloud-cron" pid=2057758 comm="ps" requested_mask="read" denied_mask="read" peer="unconfined"
kernel
```
Apparmor comes with a predefined profiles, but there are also additional upstream Apparmor profiles which may reduce log warnings;

```
sudo apt install apparmor-profiles-extra
```

## Manual Apparmor profile auditing

However if you're "allergic" to log "spamming" or believe these log entries are causing issues, let Apparmor scan the logs and **audit/correct** the profiles causing those log entries. 

> [!CAUTION]
> This procedure is not required and should only be done if your Nextcloud snap host is secured within a container (LXD/LXC) or vm.

Apparmor has tools for auditing/correcting profiles which are not installed by default.

+ install apparmor utilities on the host `sudo apt install apparmor-utils` 
+ run `sudo aa-logprof` to scan the logs for misbehaving Apparmor profiles and correcting these

You'll be presented with a list of misbehaving Apparmor profiles which can be audited/corrected by the Apparmor-log-profiler.

Documentation:
+ https://help.ubuntu.com/community/AppArmor
+ https://ubuntu.com/server/docs/apparmor
