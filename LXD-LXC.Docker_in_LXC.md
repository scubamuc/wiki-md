# Run Docker inside LXC container

Be aware that this setup is basically running a container inside a container. While this has some advantages (i.e. LXC snapshots etc), it requires careful configuration. See https://ubuntu.com/tutorials/how-to-run-docker-inside-lxd-containers.
The default volume format for LXC is ZFS and Docker natively uses BTRFS, thus it will be necessary to create a BTRFS volume in LXC for Docker containers. 
In addition `security nesting` must be enabled to allow Docker to "run as root" on the LXC host. 

### ZFS vs. BTRFS
the default volume format for LXC containers is ZFS

```⚠️ Docker will not run well with the default zfs file system```

Running Docker inside an LXC on a ZFS volume will prohibit persistent data storage. Thus a BTRFS volume is required for persistant storage for Docker on LXC.

#### Create a new btrfs storage pool

```lxc storage create DCKRPOOL btrfs```

###  Security nesting
the LXC container hosting a Docker container must have `security nesting` enabled so that the Docker container can "run as root" on the LXC host.

` security.nesting: "true"`

the option may be set per container if required:

` lxc config set <CONTAINERNAME> security.nesting true`

#### Security modules
https://ubuntu.com/tutorials/how-to-run-docker-inside-lxd-containers#2-create-lxd-container

```
lxc config set <CONTAINERNAME> security.syscalls.intercept.mknod=true security.syscalls.intercept.setxattr=true

```

## Profiles

The easiest way to do this is to copy the `default` profile to create a `default-docker` profile with these options defined and simply assign the profile to LXC containers running Docker. See https://documentation.ubuntu.com/lxd/en/stable-5.0/profiles/

**copy profile**:
```
lxc profile copy 'default' 'default-docker'
```

**edit profile**:
```
lxc profile edit 'default-docker'
```

**profile example**

```
name: default-docker
description: Default Docker profile
config:
  boot.autostart: "true"
  security.nesting: "true"
  security.syscalls.intercept.mknod: "true"
  security.syscalls.intercept.setxattr: "true"
devices:
  eth0:
    name: eth0
    nictype: bridged
    parent: br0
    type: nic
  root:
    path: /
    pool: DCKRPOOL
    type: disk
```

**assign/apply profile to instance**
```
lxc profile add <instance_name> 'default-docker'
```

**delete profile from instance**
```
lxc profile remove <instance_name> 'default-docker'
```
----

## Issue upgrading host to 24.04 breaks LXC with Docker

+ https://bugs.launchpad.net/apparmor/+bug/2067900
+ https://github.com/canonical/lxd/issues/13389

due to some Apprmor issues in 24.04, Docker may not start inside LXC. As a workaround remove the file `/etc/apparmor.d/runc` in the container and in the host.

``` 
sudo rm /etc/apparmor.d/runc
```
finally reinstall apparmor

```
sudo reinstall apparmor
```

restart the container

----

# Run Docker inside LXC vm (qemu virtual machine)

it may be easier for you to run Docker inside an LXC vm. This method is not as resource freindly as LXC container due to vm os overhead.
In addition the container cannot be moved, copied or backed up while its running and depending on the disksize you've created, does not 
allow incremental LXC snapshots and will require more diskspace and cause transfer trafic. On the other hand, vm's are stable and not as
susceptible to LXD apparmor issues.

> [!IMPORTANT]
> Be aware, the default LXC vm `qemu` cpu and memory will be `1cpu` and `1GB` and the default disk size wll be 10GB!

## create an LXC vm

### launch lxc vm
```
lxc launch ubuntu:24.04 <VMNAME> --vm
```
### launch lxc vm define cpu, ram and disk
```
lxc launch ubuntu:24.04 <VMNAME> --vm -c limits.cpu=2 -c limits.memory=2GiB -d root,size=20GiB
```

### set cpu, memory 
```
lxc stop <VMNAME> 

lxc config set <VMNAME> limits.memory=4GB 

lxc config set <VMNAME> limits.cpu=2

lxc start <VMNAME>
```

### enlarge default root disk size
```
lxc config edit <VMNAME>
```

edit config and enlarge `disk` size (disk canno be shrunk!)

```
devices:
  root:
    path: /
    pool: <POOLNAME>
    size: 20GiB
    type: disk

```

