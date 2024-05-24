# Nextcloud snap, step by step

## 1. Usage and requirements
You have decided to set up your own [Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap) as a safe and secure home for your data, **that's great!**

Before getting started be aware of what you expect from your Nextcloud instance and what system requirements have to be met to fulfill your needs. 
Aside from the [installation requirements](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Installation-requirements) there are various aspects you might consider;
+ number of users
+ storage & space requirements
+ power consuption & efficiency
+ network & connectivity
+ backup & redundancy
+ etc. ...

Plan your setup. Do some research, read the [docs](https://github.com/nextcloud-snap/nextcloud-snap) and the [Wiki](https://github.com/nextcloud-snap/nextcloud-snap/wiki).

Continue with [**Installation**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/install-Nextcloud-snap)

## 2. Network
Assuming a home network where the host running Nextcloud snap acquires a static **IPv4** address from DHCP/Router and the required ports **80** and **443** are enabled and internet facing, 
the routers public **IPv4** address must be available via DNS (**D**omain **N**ame **S**ystem) request. 

+ static host **IPv4** from DHCP/Router
+ enabled Ports **80** and **443** for **IPv4** address
+ DNS provider connecting to public **IPv4** address

## 3. Domain name and DNS
While some folks own a TLD (**T**op **L**evel **D**omain) and will create a subdomain pointing to the host, like `cloud.mydomain.com`, other folks may need a DNS provider to get a *domain name* pointing to the routers' public **IPv4** address like `cloud.mydomain.mydnsprovider.xyz`. That will be the address you enter into the browser to reach your Nextcloud instance.

There are plenty DNS providers out there to choose from. Some come at a price, some are free. Often you will have a choice of domain names, sometimes you have to take what is available. Do some research and make the right choice for you.

A *domain name* is a requirement for getting an SSL Certificate for HTTPS encryption. 

## 4. HTTPS encryption with Lets Encrypt 

The only thing we recommend you do up front is enable HTTPS. Nextcloud snap **includes** a **service** for **automated HTTPS encryption** using Lets Encrypt, or self-signed certificates. Run `nextcloud.enable-https -h` for more information.

**Enable Lets Encrypt in Nextcloud snap:**

+ issue command in host shell

```
    sudo nextcloud.enable-https lets-encrypt
```

+ Enter email address and *domain name* to get your SSL certificate

Continue to [**configure Nextcloud snap**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap)
