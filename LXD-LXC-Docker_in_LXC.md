# Run Docker container inside LXC host container

Be aware that this setup is basically running a container inside a container. While this has some advantages (i.e. live snapshots etc), it requires careful configuration. 
The default volume format for LXC is ZFS and Docker supports BTRFS natively, thus it will be necessary to create an new BTRFS volume for Docker containers inside LXC. 
In addition `security nesting` must be enabled to allow Docker to "run as root" on the LXC host, otherwise writing persistent data will be impossible. 

The easiest way to do this is to copy the `default` profile to create a `default-docker` profile with both options enabled and simply assign the profile to LXC running Docker containers.

## Profile example

```
name: default-docker
description: Default LXD profile
config:
  boot.autostart: "true"
  security.nesting: "true"
  limits.memory: 4GB
devices:
  eth0:
    name: eth0
    nictype: bridged
    parent: br0
    type: nic
  root:
    path: /
    pool: DOCKPOOL
    type: disk
``` 

## ZFS vs. BTRFS
the default volume format for LXC containers is ZFS

```⚠️ Docker will not run well with the default zfs file system```

Running Docker inside an LXC on a ZFS volume will prohibit persistent data. Thus a BTRFS is required for Docker on LXC.

### create a new btrfs storage pool

```lxc storage create DOCKPOOL btrfs```

##  Security nesting
the LXC container hosting a Docker container must have `security nesting` enabled so that the Docker container can "run as root" on the LXC host.

` security.nesting: "true"`

`lxc config set {container-name} security.nesting true`

