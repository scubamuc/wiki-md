# Configure bridged network with Network-Manager

there are several methods to configure bridged networking on Linux/Ubuntu. Why bridged networking for LXD? Well, `macvlan` is easy to configure, but you will not be able to reach the container via SSH from the LXD host. To enable virtual networking bridged network is the way to go. Some will argue about this... 

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

# Configure bridged network with Netplan (from 24.04)

if you're happy using **Network-Manager** instead of **Netplan**, you can simply install Network-Manager `sudo apt install network-manager` and use that instead... Network-Manager can be used too.

further Netplan reading:
* https://thenewstack.io/how-to-create-a-bridged-network-for-lxd-containers/
* https://ubuntu.com/server/docs/configuring-networks#bridging-multiple-interfaces
* https://netplan.readthedocs.io/en/latest/netplan-tutorial/
* https://luppeng.wordpress.com/2023/01/10/make-lxd-containers-visible-on-host-network/
* https://thomas-leister.de/en/lxd-use-public-interface/

Configure the bridge by editing or creating your Netplan configuration in /etc/netplan/, entering the appropriate values for your physical interface and network:

1. view appropriate values using `ip a` and note you ethernet interface names (`enp0s25` or similar)
2. create: `sudo nano /etc/netplan/01-netcfg-br0.yaml`

```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s25:
      dhcp4: no
  bridges:
    br0:
      dhcp4: yes
      interfaces:
        - enp0s25
```

3. activate your bridge using `sudo netplan apply`
4. you may get a warning `Permissions for /etc/netplan/01-netcfg-br0.yaml are too open. Netplan configuration should NOT be accessible by others.` which you can correct with `sudo chmod 600 01-netcfg-br0.yaml`
5. Now apply the configuration to enable the bridge:
```
sudo netplan apply
```

Check to see your bridge is available `ip a`:
```
4: br0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 86:c7:15:fb:a6:c0 brd ff:ff:ff:ff:ff:ff

```
The new bridge interface should now be up and running. The `brctl` provides useful information about the state of the bridge, controls which interfaces are part of the bridge, etc. See man brctl for more information.

## Manage bridges with `brctl`

**bring down bridge**
```
ip link set br0 down
```
**delete bridge**
```
brctl delbr br0
```

## Restart the network

```
systemctl restart networking
```
----

# Configure bridged network with `brctl` (bridge control)

* https://www.zenarmor.com/docs/linux-tutorials/how-to-configure-network-bridge-on-linux

`brctl` is preinstalled on Ubuntu 24.04 but can be installed by issuing command  `apt install bridge-utils`

## Add bridge `br0`

issue command to create a new bridge <name> (here `br0` but it may be anything)
```
brctl addbr br0
```

## Delete bridge 

issue command to detelete brigde if you change your mind
```
brctl delbr br0
```

## Discover your network (device) interface name 

issue command to discover/view the interfaces that will be bridged (eth0, eth1, etc. is typical)
```
ip addr show
``` 

## Add network (device) to your bridge

add interface to the newly added bridge device issue command:
```
brctl addif br0 enp0s25
```

add **multiple** intefaces if required `brctl addif br0 enp0s25 ens18`
**remove** an interface from a bridge `brctl delif br0 ens18`

## View interfaces in a bridge

view the summary of the overall bridge status

```
brctl show
```

## Configure Spanning Tree (`stp`)

* **enable** `stp` issue: `brctl stp br0 on`
* **disable** `stp` issue: `brctl stp br0 off`
* **view** `stp` parameters: `brctl showstp br0`

## Start your bridge

```
ifup br0  
```

## Restart the network

```
systemctl restart networking
```
