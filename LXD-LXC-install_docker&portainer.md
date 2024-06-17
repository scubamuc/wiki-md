# Install Docker & Portainer on LXC

## Run Docker inside LXC host container

Be aware that this setup is basically running a container inside a container. While this has some advantages (i.e. LXC snapshots etc), it requires careful configuration.
The default volume format for LXC is ZFS and Docker supports BTRFS natively, thus it will be necessary to create an new BTRFS volume for Docker containers inside LXC.
In addition `security nesting` must be enabled to allow Docker to "run as root" on the LXC host.

### ZFS vs. BTRFS

the default volume format for LXC containers is ZFS

`⚠️ Docker will not run well with the default zfs file system`

Running Docker inside an LXC on a ZFS volume will prohibit persistent data. Thus a BTRFS is required for Docker on LXC.

#### create a new btrfs storage pool

`lxc storage create DOCKPOOL btrfs`

### Security nesting

the LXC container hosting a Docker container must have `security nesting` enabled so that the Docker container can "run as root" on the LXC host.

` security.nesting: "true"`

the option may be set per container if required:

` lxc config set {container-name} security.nesting true`

## Docker profile

The easiest way to do this is to copy the `default` profile to create a `default-docker` profile with these options defined and simply assign the profile to LXC containers running Docker.

**copy profile**:

```
lxc profile copy 'default' 'default-docker'
```

**edit profile**:

```
lxc profile edit 'default-docker'
```

**Profile example**

```
name: default-docker
description: Default LXD profile
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
    pool: DOCKPOOL
    type: disk
```

---

## LXD host

On LXD host launch container:

```
lxc launch images:ubuntu/22.04 CONTAINERNAME
```

On LXD host configure security nesting (required):

```
lxc config set CONTAINERNAME security.nesting=true 
```

On LXD host security syscalls (optional):

```
lxc config set CONTAINERNAME security.syscalls.intercept.mknod=true 
```

```
lxc config set CONTAINERNAME security.syscalls.intercept.setxattr=true
```

Access LXC container:

```
lxc shell CONTAINERNAME
```

### LXC container

Update OS in container:

```
apt update && apt upgrade
```

Install **Docker** and **Docker Compose** in Container:

```
apt install docker.io docker-compose
```

---

## Portainer deployment

1. First, create the volume that Portainer Server will use to store its database:

```
docker volume create portainer_data
```

1. Then, download and install the Portainer Server container:

```
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

1. Check if its running

```
docker ps
```

1. Login to Portainer:

<https://IP.of.lxc.container:9443>

TIP: Portainer Login = admin and a looong password