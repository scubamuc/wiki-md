# LXD containers get IP addresses from your LAN using macvlan 
https://blog.simos.info/how-to-make-your-lxd-container-get-ip-addresses-from-your-lan/

We need to add a nic with nictype macvlan and parent to the appropriate network interface on the host and we are then ready to go. Let’s identify the correct parent, using the ip route command. This command shows the default network route. It also shows the name of the device (dev), which is in this case enp5s12. (Before systemd, those used to be eth0 or wlan0. Now, the name varies depending on the specific network cards).

```
$ ip route show default 0.0.0.0/0
default via 192.168.1.1 dev enp5s12 proto static metric 100
```

Now we are ready to add the appropriate device to the macvlan LXD profile. We use the lxc profile device add command to add a device eth0 to the profile lanprofile. We set nictype to macvlan, and parent to enp5s12.

```
$ lxc profile device add macvlan eth0 nic nictype=macvlan parent=enp5s12
Device eth0 added to macvlan
$ lxc profile show macvlan
config: {}
description: ""
devices:
  eth0:
    nictype: macvlan
    parent: enp5s12
    type: nic
name: macvlan
used_by: []
ubuntu@myvm:~$ 
$ 
```
Well, that’s it. We are now ready to launch containers using this new profile, and they will get an IP address from the DHCP server of the LAN.
Launching LXD containers with the new profile

Let’s launch two containers using the new macvlan profile and then check their IP address. We need to specify first the default profile, and then the macvlan profile. By doing this, the container will get the appropriate base configuration from the first profile, and then the networking will be overridden by the macvlan profile.

```
$ lxc launch ubuntu:18.04 net1 --profile default --profile macvlan
Creating net1
Starting net1
$ lxc launch ubuntu:18.04 net2 --profile default --profile macvlan
Creating net2
Starting net2
$ lxc list
+------+---------+---------------------+------+-----------+-----------+
| NAME |  STATE  |        IPV4         | IPV6 |   TYPE    | SNAPSHOTS |
+------+---------+---------------------+------+-----------+-----------+
| net1 | RUNNING | 192.168.1.7 (eth0)  |      | CONTAINER | 0         |
+------+---------+---------------------+------+-----------+-----------+
| net2 | RUNNING | 192.168.1.3 (eth0)  |      | CONTAINER | 0         |
+------+---------+---------------------+------+-----------+-----------+
$ 
```
Both containers got their IP address from the LAN router. Here is the router administration screen that shows the two containers. I edited the names by adding LXD in the front to make them look nicer. The containers look and feel as just like new LAN computers!

Let’s ping from one container to the other.
```
$ lxc exec net1 -- ping -c 3 192.168.1.7
PING 192.168.1.7 (192.168.1.7) 56(84) bytes of data.
64 bytes from 192.168.1.7: icmp_seq=1 ttl=64 time=0.064 ms
64 bytes from 192.168.1.7: icmp_seq=2 ttl=64 time=0.067 ms
64 bytes from 192.168.1.7: icmp_seq=3 ttl=64 time=0.082 ms

--- 192.168.1.7 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2036ms
rtt min/avg/max/mdev = 0.064/0.071/0.082/0.007 ms
```
You can ping these containers from other computers on your LAN! But the host and these macvlan containers cannot communicate over the network. This has to do with how macvlan works in the Linux kernel.
Troubleshooting
Help! I cannot ping between the host and the containers!

To be able to get the host and containers to communicate with each other, you need some additional changes to the host in order to get added to the macvlan as well. It discusses it here, though I did not test because I do not need communication of the containers with the host. If you test it, please report below.
Help! I do not get anymore those net1.lxd, net2.lxd fancy hostnames!

The default LXD DHCP server assigns hostnames like net1.lxd, net2.lxd to each container. Then, you can get the containers to communicate with each other using the hostnames instead of the IP addresses.

When using the LAN DHCP server, you would need to configure it as well to produce nice hostnames.
Help! Can these new macvlan containers read my LAN network traffic?

The new macvlan LXD containers (that got a LAN IP address) can only see their own traffic and also any LAN broadcast packets. They cannot see the traffic meant for the host, nor the traffic for the other containers.
Help! I get the error Error: Device validation failed “eth0”: Cannot use “nictype” property in conjunction with “network” property

A previous version of this tutorial had the old style of how to add a device to a LXD profile. The old style was supposed to work in compatibility mode in newer versions of LXD. But at least in LXD 4.2 it does not, and gives the following error. You should not get this error anymore since I have updated the post. You may get an error if you are using a very old LXD. In that case, report back in the comments please.
```
$ lxc profile device set macvlan eth0 nictype macvlan
Error: Device validation failed "eth0": Cannot use "nictype" property in conjunction with "network" property
$
```
Summary

With this tutorial, you are able to create containers that get an IP address from the LAN (same source as the host), using macvlan.

A downside is that the host and these macvlan containers cannot communicate over the network. For some, this is a neat advantage, because they shield the host from the containers.

The macvlan containers are then visible on the LAN and work just like another LAN computer.

This tutorial has been updated with the newer commands to edit a LXD profile (lxc profile device add). The older command now gives an error as you can see in the more recent comments below.
Simos Xenitellis
Simos Xenitellis
Share this:

    X Facebook Reddit Email Print 

Like this:
Related
Configuring public IP addresses on cloud servers for LXD containers
July 23, 2018

In "Linux"
How to make your LXD containers get IP addresses from your LAN using a bridge

Background: LXD is a hypervisor that manages machine containers on Linux distributions. You install LXD on your Linux distribution and then you can launch machine containers into your distribution running all sort of (other) Linux distributions. Using macvlan. See https://blog.simos.info/how-to-make-your-lxd-container-get-ip-addresses-from-your-lan/ Using bridged. It is this tutorial, you are reading it…
January 29, 2018

In "Planet Ubuntu"
How to get LXD containers get IP from the LAN with routed network

Update #3 - 27 January 2020: Fedora requires special instructions for routed to work. See the section below named The routed profile for Fedora for more. UPDATE #2 - 26 January 2020: Debian requires special instructions for routed to work. See the section below named The routed profile for Debian…
April 4, 2020

In "general"

    dhcp, lan, lxd, macvlan, networking, ubuntu

86 comments
10 pings

        Blackpaw on March 21, 2021 at 14:04 # 

Yup, got it working using a public bridge! thank you so much.

Just for info – using lxd to setup 3 ubuntu(focal) containers on a ubuntu server, to act as a moosefs filesystem, passing through the hosts 9 drives for storage.

Nearly finished, working very nicely. Looking fwd to testing performance.
Loading...

    max on April 11, 2021 at 22:17 # 

Hello @ Blackpaw ,

I did the same with lightweigh VMs following this guide:

https://nasilemaktech.com/guide-lizardfs-drive-level-redundancy/

..and was going to try with lxd containers.

Is it something similar that you are doing?

Do you mind to share the setup?

Cheers,

Max
Loading...

    Radu on June 3, 2021 at 12:27 # 

Simos, thank you for this excellent How To guide. I have successfully set up macvlan on my host running LXD 4.13. I am trying to create a Traefik reverse proxy container that will expose services for the other containers, all running on macvlan. I am Not running Docker, and I do not want to, just the Traefik executable inside LXD container. I can connect from to the Traefik dashboard from any computer on my lan, but I cannot proxy to the services. Do you know if Traefik is supposed to work with LXD, especially macvlan? Kind regards, Radu
Loading...

        Simos Xenitellis on June 3, 2021 at 17:28
        Author # 

    Thank you Radu!

    Traefik supports dynamic configuration so that it works automatically without the need for configuration from the administrator’s side. However, it does not support LXD as a provider yet. Please see https://github.com/traefik/traefik/issues/2195 and https://doc.traefik.io/traefik/providers/overview/ (list of providers)

    Having said that, there is the option for static configuration. You would need to go through that route. Since you can access the dashboard, it means that there are no network issues.

    Note that you can put traefik into a container, and then expose that container using a proxy device. As shown at https://blog.simos.info/how-to-use-the-lxd-proxy-device-to-map-ports-between-the-host-and-the-containers/

    When creating a proxy device, you can add the parameter nat=true so that LXD will create automatically for you the necessary iptables/nftables rules and would not need to spawn a proxy process.
    Loading...

    xikky on June 22, 2021 at 19:05 # 

Thanks a lot for these articles, they are very helpful! One thing I got very confused with, lost a lot of time on and I couldn’t get the result at the end – and it seems it is a technology limitation. And this is having my home server running on both WiFi and Ethernet, with ethernet being the preferred one. I managed to set this up with nmcli command, so far so good. But at the end there was no way how to get LXD working with WiFi – I will be using both containers and VMs. So macvlan doesn’t work, as per your #1 warning, but neither does bridge, as bridging a wifi interface is not possible by IEEE standards.

I found this info very misleading all over the internet and ended up burning a couple of days trying to solve this. If I’m mistaken I would be happy if someone can correct me, but also I would like to put it out there, that so far I couldn’t get and seems that it is not possible to get both your machine’s Ethernet and Wifi interfaces working side by side, according to connection, to provide LAN access for your LXD containers.
Loading...

    Radu on June 30, 2021 at 16:23 # 

Thank you Simos. I am using static configuration as suggested. If all my containers are using macvlan profile (including Traefik), do I still need to use LXD proxy device to map ports? I was hoping not to use bridge/nat configs. As mentioned previously, I can access the Treafik dashboard, routers and services have a green tick, but when I am trying to access the proxied service, I get This site can’t be reached Check if there is a typo in guac.traefik. DNS_PROBE_FINISHED_NXDOMAIN. All of this is running on my lan, nothing external. Any suggestions to what I’m doing wrong? Kind Regards.
Loading...

    Darold Lucus on July 20, 2021 at 17:07 # 

This is all well and good, but you can still use the macvlan on 4.2 lxd. you need to remove the network stance in the profile before you can set the profile to macvlan. So lxc profile edit name of profile. Then remove the network stance and replace with nictype: macvlan, and parent: your ETH.

EG:

nictype: macvlan
parent: enp2s0f1

After that you can just do a profile copy and the rest of your profiles will already be set to macvlan, you will need to set the parent nic on all of your copies though. “lxc profile device set profilename eth0 parent ‘your ETH’ “
Loading...

    Alexei on October 26, 2021 at 22:38 # 

Great series of articles! Suppose I have a single network card in my host. Then I use a macvlan profile to create two separate LXD containers. Each of these containers gets their own mac address. This works fine.

Now, in my network I have two VLANS: VLAN10 and VLAN20. I’d like one of the LXD containers to be on VLAN20 (say the second LXD container), and the LXD1 and the host on VLAN10. Is this possible?
Loading...

    Miguel on October 27, 2022 at 13:41 # 

Hello! I’m having some problems trying to set up my home server using LXD containers. I’m using docker inside my containers, using a “central” portainer and agents to control all my stacks.

The first problem is that in my SWAG (nginx+letsencrypt) the ip for all accesses is always the gateway of the docker bridge (you don’t know from where is the access). I was thinking that maybe I can fix it using the macvlan method described here, but then I’m unable to communicate from the macvlan container to the bridged (default) containers…

My question is if using macvlan will fix my nginx issue, and how to reach the containers in the bridged network.
Loading...

    RO on January 16, 2023 at 19:11 # 

The mentioned blog about the host-guest connectivity (http://noyaudolive.net/2012/05/09/lxc-and-macvlan-host-to-guest-connection/) in the troubleshooting section, doesn’t exist anymore. Another blog refers to that fact and add its solution/version for it: https://limbenjamin.com/articles/macvlan-host-guest-connectivity.html
Loading...

    Heiko on February 21, 2023 at 16:17 # 

    I just created a bridge br0 on the host and created a profile “bridged” like:

    devices:
    eth0:
    name: eth0
    nictype: bridged
    parent: br0
    type: nic

    so I may choose on which network the container should start by adding the profile.
    What is the benefit of using the macvlan aproach?
    Loading...

    « Prev
    1
    2	

Leave a Reply

This site uses Akismet to reduce spam. Learn how your comment data is processed.
Subscribe to Blog via Email

Enter your email address to subscribe to this blog and receive notifications of new posts by email.

Email Address

Recents posts

    How to run a Linux Desktop virtual machine on Incus May 2, 2025
    A networking guide for Incus September 29, 2024
    How to recover or reconnect an Incus storage pool September 11, 2024
    How to use your Android phone with ADB in an Incus container August 22, 2024
    How to share a folder between a host and a container in Incus August 22, 2024

Recent Comments

    Christopher on How to run a Windows virtual machine on Incus on Linux
    Vidur Butalia on How to install and setup the Incus Web UI
    tschacara on Running OCI images (i.e. Docker) directly in Incus
    How to run a Linux Desktop virtual machine on Incus – Mi blog lah! on How to run a Windows virtual machine on Incus on Linux
    el dge on Running OCI images (i.e. Docker) directly in Incus

webpage

    Προσωπική Σελίδα

