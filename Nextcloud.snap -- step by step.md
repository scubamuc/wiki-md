# Nextcloud snap, step by step

## 1. Usage and requirements
You have decided to set up your own [Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap) as a safe and secure home for your data, **that's great!**

Before getting started be aware of what you expect from your Nextcloud instance and what requirements have to be met to fulfill your needs. 
Aside from the [installation requirements](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Installation-requirements) there are various aspects you might consider such as;
+ number of users
+ storage & space requirements
+ power consuption & efficiency
+ network & connectivity
+ backup & redundancy
+ etc...

Plan your setup. Do some research, read the [docs](https://github.com/nextcloud-snap/nextcloud-snap) and the [Wiki](https://github.com/nextcloud-snap/nextcloud-snap/wiki).

See expamle: [Nextcloud snap server setup & specs](https://github.com/scubamuc/scubamuc.github.io)

## 2. Network
Assuming a home network where the host running Nextcloud snap acquires a static **IPv4** address from DHCP/Router and the required ports **80** and **443** are enabled and internet facing, 
the routers public **IPv4** address must be available via DNS (**D**omain **N**ame **S**ystem) request. 

+ static host **IPv4** from DHCP/Router
+ enabled Ports **80** and **443** for **IPv4** address
+ DNS provider connecting to public **IPv4** address

## 3. Domain, DNS provider and dynamic DNS provider
While some folks own a TLD (**T**op **L**evel **D**omain) and will create a subdomain pointing to the host, like `cloud.domain.com`. 

Other folks will need a DNS provider to acquire a domain name. All you really need is a name for your **IPv4** to be connected to, something like `cloud.user.dnsprovider.xyz` 
which will be the address you enter into the browser to reach your Nextcloud instance in the internet. 

There are plenty DNS providers out there to choose from. Some come at a price, some are free.

### 3.1 FQDN
+ FQDN (**F**ully **Q**alified **D**omain **N**ame)
### 3.2 DNS
+ DNS (**D**omain **N**ame **S**ystem)
### 3.3 Encryption
