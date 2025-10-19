# LXD setup with macvlan

Be aware that the recommended setup is a bridged network (see https://github.com/scubamuc/wiki-md/blob/scubamuc-wiki/LXD-LXC.bridged-network.md). 
Setting up LXD/LXC with macvlan is easiest to get up and running quickly. There are howerver caveats; it is not possible to ssh into the container
from the host! to access the container use `lxc shell <CONTAINERNAME>`

* `sudo snap install lxd`
* `lxd init`
--> use defaults, except `network bridge setup`
```
Would you like to use LXD clustering? (yes/no) [default=no]: 
Do you want to configure a new storage pool? (yes/no) [default=yes]: 
Name of the new storage pool [default=default]:     
Name of the storage backend to use (btrfs, lvm, ceph, dir, powerflex, pure, zfs) [default=zfs]: 
Create a new ZFS pool? (yes/no) [default=yes]: 
Would you like to use an existing empty block device (e.g. a disk or partition)? (yes/no) [default=no]: 
Size in GiB of the new loop device (1GiB minimum) [default=11GiB]:
Would you like to connect to a MAAS server? (yes/no) [default=no]: 
Would you like to create a new local network bridge? (yes/no) [default=yes]: no
Would you like to configure LXD to use an existing bridge or host interface? (yes/no) [default=no]: 
Would you like the LXD server to be available over the network? (yes/no) [default=no]: 
Would you like stale cached images to be updated automatically? (yes/no) [default=yes]: 
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]: 
```

* add macvlan to default profile, defining network device (find network device `ip a`)
```
lxc profile device add default eth0 nic nictype=macvlan parent=enp0xxx
```
## Launch your container

* launch your first container (omit `<CONTAINERNAME>` for random name)
```
lxc launch ubuntu:24.04 <CONTAINERNAME>
```
