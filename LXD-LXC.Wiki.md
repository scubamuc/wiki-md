# LXD - LXC Wiki

<https://documentation.ubuntu.com/lxd/en/latest/#>

1. <https://linuxcontainers.org/lxd/getting-started-cli/>
2. <https://blog.jrehkemper.de/index.php/lxd-linux-container/#SnapInstallation>
3. <https://discourse.world/h/2020/04/09/Basic-LXD-Features-Linux-Container-Systems#vybor-faylovoy-sistemy-dlya-storage-pool-navigaciya>
4. https://gist.github.com/berndbausch/a6835150c7a26c88048763c0bd739be6
5. <https://stgraber.org/2016/03/19/lxd-2-0-your-first-lxd-container-312/>

# Useful LXD commands

Summarized from <https://stgraber.org/2016/03/19/lxd-2-0-your-first-lxd-container-312/>.

https://gist.github.com/berndbausch/a6835150c7a26c88048763c0bd739be6

Interestingly, the LXD command line client is named.... `lxc`!

### List available containers

```
lxc image list ubuntu:        # ubuntu: is officially supported image source
lxc image list images:        # images: is an unsupported source
lxc image alias list images:  # lists user-friendly names
```

### List running containers

```
lxc list status=running
```

### List stopped containers

```
lxc list status=stopped
```

### Launch a container

This creates and starts a container.

```
lxc launch ubuntu:14.04 CONTAINERNAME   # image and container names are optional 
lxc launch ubuntu:14.04/armhf armcont   # specific architecture
lxc launch images:alpine/3.3/amd64      # unsupported images: source
```

#### Create container

Without starting it.

```
lxc init images:alpine/3.3/amd64 alpinecont
lxc copy CONTAINER1 CONTAINER2        # clone
lxc delete alpinecont [--force]       # --force if it is running
```

#### Stop container

```
lxc stop CONTAINER
```

#### Start container

```
lxc stop CONTAINER 
```

#### Stop all containers

```
lxc stop --all
```

#### Start/stop after creating it

```
lxc start alpinecont
lxc stop alpinecont [--force]         # --force if it doesn't want to stop
lxc restart alpinecont [--force]
lxc pause alpinecont                  # SIGSTOP to all container processes
```

### List local containers

```
lxc list 
lxc list --columns "nsapt"            # name, status, arch, PID of init, type
lxc list security.privileged=true     # filter for properties
lxc info CONTAINERNAME                # detailed info about one container
```

#### Available columns for the list command

```
    4 - IPv4 address
    6 - IPv6 address
    a - Architecture
    c - Creation date
    n - Name
    p - PID of the container's init process
    P - Profiles
    s - State
    S - Number of snapshots
    t - Type (persistent or ephemeral)
```

### Rename container

```
lxc move CONTAINER NEWNAME
```

or

```
lxc rename CONTAINER NEWNAME
```

### Configuration

Config changes are  effective immediately, even if container is running.

```
export VISUAL=/usr/bin/vim
lxc config edit CONTAINERNAME           # launches editor
lxc config set CONTAINERNAME KEY VALUE  # change a single config item
lxc config device add CONTAINERNAME DEVICE TYPE KEY=VALUE
lxc config show [--expanded] CONTAINERNAME
Configuration settings can be saved as **profiles**.
```

### Enter the container

```
lxc exec CONTAINERNAME -- PROGRAM OPTIONS
lxc exec CONTAINERNAME sh
lxc exec CONATINERNAME --env KEY=VALUE PROGRAM   # environment variable
```

This command runs the program in all the namespaces and cgroups of the container. The program must exist inside the container.

### Access container files

```
lxc file pull CONTAINERNAME/etc/passwd /tmp/mypasswd
lxc file push /tmp/mypasswd CONTAINERNAME/etc/passwd 
lxc file edit CONTAINERNAME/etc/passwd 
```

### Snapshots

```
lxc snapshot CONTAINERNAME SNAPNAME    # SNAPNAME is optional; default name snap*X*
lxc restore CONTAINERNAME SNAPNAME     # resets the container to snapshot
lxc copy CONTAINERNAME/SNAPNAME NEWCONTAINER               # new container from snapshot
lxc delete CONTAINERNAME/SNAPNAME
lxc info CONTAINERNAME                 # lists snapshots among other info
lxc move CONTAINERNAME/SNAPNAME CONTAINERNAME/NEWSNAPNAME  # rename snapshot
```

### Create a container from a snapshot

1. Find snapshot of a container

```
lxc info CONTAINER
```

1. Copy snapshot to new container

```
lxc copy CONTAINER/SNAPSHOT NEW-CONTAINER
```

### Desktop images

In addition to cloud images for a variety of distributions, we also support desktop images that allow you to launch a desktop VM with no additional configuration needed.

For launching an Ubuntu 22.04 VM the command would look like this:

```
lxc launch images:ubuntu/22.04/desktop ubuntu --vm -c limits.cpu=4 -c limits.memory=4GiB --console=vga   
```

The whole process takes seconds, as shown below.

### Ubuntu Desktop VM

```
lxc launch ubuntu:22.04 --vm -c limits.cpu=4 -c limits.memory=4GiB --console=vga
```

### Archlinux Desktop VM

```
lxc launch images:archlinux/desktop-gnome archlinux --vm -c security.secureboot=false -c limits.cpu=4 -c limits.memory=4GiB --console=vga
```

### View/open Desktop VM

The host will need \`remote-viewer\` install to be able to open the console.

```
sudo apt install virt-viewer
```

```
lxc console <VMNAME> --type=vga
```

### Enable clustering

When you do want to enable clustering, simply run

```
lxc cluster enable <CLUSTERNAME>
```

See `lxc cluster` for the rest of the subcommands.

`lxc cluster enable <my-node-name>` turns a non-clustered LXD instance into the first node of a LXD cluster *without losing any data*. Any additional node you want to add to the cluster **must be completely empty** though.

# Configure LXD/LXC bridged network

there are several methods to configure bridged networking on Linux/Ubuntu. Why bridged networking for LXD? Well, ``macvlan` is easier to configure by simply changing one option in the default LXD config, but you will not be able to reach the container via SSH from the LXD host. To enable "true" virtual networking ability bridged network is the way to go. Some will argue about this...
Create network bridge with nmcli

nmcli - package is preinstalled on Ubuntu server

    see man nmcli for manual
    see nmcli --help for help

Create network bridge

$ sudo nmcli con add ifname br0 type bridge con-name br0 
$ sudo nmcli con add type bridge-slave ifname eno1 master br0 
$ nmcli connection show

Disable STP:

$ sudo nmcli con modify br0 bridge.stp no 
$ nmcli con show 
$ nmcli -f bridge con show br0

The following information will be displayed after issuing the last command:

bridge.mac-address: -- 
bridge.stp: no 
bridge.priority: 32768 
bridge.forward-delay: 15 
bridge.hello-time: 2 
bridge.max-age: 20 
bridge.ageing-time: 300 
bridge.multicast-snooping: yes

Disable the standard network connection. The bridged connection should start automatically:

$ sudo nmcli con down standardconnection1
$ sudo nmcli con up br0

To ensure that the bridge is started on reboot the standard connection may be deleted using nmtui :

$ sudo nmtui
Assign fixed IP to bridge

$ sudo nmcli connection modify br0 ipv4.addresses '192.168.2.200/24'
$ sudo nmcli connection modify br0 ipv4.gateway '192.168.2.1'
$ sudo nmcli connection modify br0 ipv4.dns '192.168.2.1'
$ sudo nmcli connection modify br0 ipv4.dns-search '9.9.9.9'
$ sudo nmcli connection modify br0 ipv4.method manual

Alternatively IP's may be assigned in DHCP using router interface


# Run Docker inside LXC host container

Be aware that this setup is basically running a container inside a container. While this has some advantages (i.e. LXC snapshots etc), it requires careful configuration. 
The default volume format for LXC is ZFS and Docker supports BTRFS natively, thus it will be necessary to create an new BTRFS volume for Docker containers inside LXC. 
In addition `security nesting` must be enabled to allow Docker to "run as root" on the LXC host. 

### ZFS vs. BTRFS
the default volume format for LXC containers is ZFS

```⚠️ Docker will not run well with the default zfs file system```

Running Docker inside an LXC on a ZFS volume will prohibit persistent data. Thus a BTRFS is required for Docker on LXC.

#### create a new btrfs storage pool

```lxc storage create DOCKPOOL btrfs```

###  Security nesting
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
description: Default LXD for Docker profile
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

# Syncronise/Copy containers between LXD nodes

If an LXD-cluster with three nodes is overkill for you, a cold standby failover can easily be configured and works fine with two nodes. Be sure to configure WOL (Wake on Lan) for the cold standby node and ensure regular (skripted) container syncronisation or do it manually. Note also, container snapshots on the primary node belong to the container and are syncronised to the secondary node. You may want to prevent snapshots from being syncronised for speed and space with the option `--instance-only`.

One caveat of this model is that the controller node holding the primary database is not present. Thus ensure that the containers on the secondary node are stopped as the syncronised containers have identical IP's as those on the primary node which causes network issues. Stopping all containers on the secondary node at startup sounds strange, since this is one of th great advantages of LXD... but for this use case add `@reboot sleep 60; lxc stop --all` to your crontab so that all containers are stopped after cold-starting the secondary LXD-backup server.

Assuming you have two identical servers **LXD1** (primary LXD-server) and **LXD2** (secondary LXD-backup). Both servers should be known to eachother by adding them to remotes respectively. Needless to say that passwordless SSH between both servers should be configured.

#### On production **LXD1** server

Check remote servers:

```
lxc remote list
```

add remote server:

on **LXD1**
```
lxc remote add LXD2 <ip.address.of.server>:8443
```

#### On secondary **LXD2** server

add remote server:

on **LXD2**
```
lxc remote add LXD1 <ip.address.of.server>:8443
```

#### On production **LXD1** server

Copy container to ***LXD2***

```
lxc copy CONTAINER LXD2: --stateless --refresh
```

Check containers on ***LXD2*** server:

```
lxc list LXD2:
```

Copying or syncronising containers between servers can be scripted.
Be sure to *stop* the containers to be syncronised on ***LXD2*** server.

Example script:

```
#!/bin/bash
####################################################################
## Syncronise/copy containers and snapshots from LXD1 to LXD2 server
####################################################################
## Stop all containers on backup-server
  ssh <ip.address.of.LXD2> 'lxc stop --all'
#copy container1
  lxc copy CONTAINER1 LXD2: --instance-only --stateless --refresh ;
#copy container2
  lxc copy CONTAINER2 LXD2: --stateless --refresh ;
exit
```
----

See lxc copy --help

```
lxc copy

Description:
  Copy instances within or in between LXD servers

  Transfer modes (--mode):
   - pull: Target server pulls the data from the source server (source must listen on network)
   - push: Source server pushes the data to the target server (target must listen on network)
   - relay: The CLI connects to both source and server and proxies the data (both source and target must listen on network)

  The pull transfer mode is the default as it is compatible with all LXD versions.

Usage:
  lxc copy [<remote>:]<source>[/<snapshot>] [[<remote>:]<destination>] [flags]

Aliases:
  copy, cp

Flags:
      --allow-inconsistent   Ignore copy errors for volatile files
  -c, --config               Config key/value to apply to the new instance
  -d, --device               New key/value to apply to a specific device
  -e, --ephemeral            Ephemeral instance
      --instance-only        Copy the instance without its snapshots
      --mode                 Transfer mode. One of pull, push or relay (default "pull")
      --no-profiles          Create the instance with no profiles applied
  -p, --profile              Profile to apply to the new instance
      --refresh              Perform an incremental copy
      --stateless            Copy a stateful instance stateless
  -s, --storage              Storage pool name
      --target               Cluster member name
      --target-project       Copy to a project different from the source

Global Flags:
      --debug          Show all debug messages
      --force-local    Force using the local unix socket
  -h, --help           Print help
      --project        Override the source project
  -q, --quiet          Don't show progress information
      --sub-commands   Use with help or --help to view sub-commands
  -v, --verbose        Show all information messages
      --version        Print version number
```

