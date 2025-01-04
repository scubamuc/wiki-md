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

` lxc config set <container-name> security.nesting true`

#### Security modules
https://ubuntu.com/tutorials/how-to-run-docker-inside-lxd-containers#2-create-lxd-container

```
lxc config set <container-name> security.syscalls.intercept.mknod=true security.syscalls.intercept.setxattr=true

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
``
