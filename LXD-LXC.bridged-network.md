# Configure LXD/LXC bridged network Network-Manager

there are several methods to configure bridged networking on Linux/Ubuntu. Why bridged networking for LXD? Well, `macvlan` is easier to configure, but you will not be able to reach the container via SSH from the LXD host. To enable virtual networking bridged network is the way to go. Some will argue about this... 

## Create network bridge with nmcli
`nmcli` - package is preinstalled on Ubuntu server

* see `man nmcli` for manual
* see `nmcli --help` for help

### Create network bridge

```
$ sudo nmcli con add ifname br0 type bridge con-name br0 
$ sudo nmcli con add type bridge-slave ifname eno1 master br0 
$ nmcli connection show
```

**Disable STP:**

```
$ sudo nmcli con modify br0 bridge.stp no 
$ nmcli con show 
$ nmcli -f bridge con show br0
```

The following information will be displayed after issuing the last command:

```
bridge.mac-address: -- 
bridge.stp: no 
bridge.priority: 32768 
bridge.forward-delay: 15 
bridge.hello-time: 2 
bridge.max-age: 20 
bridge.ageing-time: 300 
bridge.multicast-snooping: yes
```

Disable the standard network connection. The bridged connection should start automatically:

```
$ sudo nmcli con down standardconnection1
$ sudo nmcli con up br0
```

To ensure that the bridge is started on reboot the standard connection may be deleted using `nmtui` :

`$ sudo nmtui`

### Assign fixed IP to bridge

```
$ sudo nmcli connection modify br0 ipv4.addresses '192.168.2.200/24'
$ sudo nmcli connection modify br0 ipv4.gateway '192.168.2.1'
$ sudo nmcli connection modify br0 ipv4.dns '192.168.2.1'
$ sudo nmcli connection modify br0 ipv4.dns-search '9.9.9.9'
$ sudo nmcli connection modify br0 ipv4.method manual
```

Alternatively IP's may be assigned in DHCP using router interface

----

# Configure LXD/LXC bridged network Netplan (from 24.04)

https://ubuntu.com/server/docs/configuring-networks#bridging-multiple-interfaces

Configure the bridge by editing your netplan configuration found in /etc/netplan/, entering the appropriate values for your physical interface and network:
```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp3s0:
      dhcp4: no
  bridges:
    br0:
      dhcp4: yes
      interfaces:
        - enp3s0
```
Now apply the configuration to enable the bridge:
```
sudo netplan apply
```

The new bridge interface should now be up and running. The `brctl` provides useful information about the state of the bridge, controls which interfaces are part of the bridge, etc. See man brctl for more information.
