# How to configure collabora CODE with docker for Nextcloud snap

This example will require [Docker](https://www.docker.com/) and a [Reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy) for the CODE server.

Install Docker on host. Create a DNS for subdomain like `office.mydomain.xyz`. Create a proxy host for **https** on port 9980 on reverse proxy pointing to that host and get the domain name encrypted.

## Docker Stack:

```
name: 'code'

services:
  collabora:
    image: collabora/code
    container_name: collabora
    environment:
      - domain=cloud.mydomain.tld  # Replace with your actual domain # disable when using aliasgroups
      # - aliasgroup1=https://cloud.mydomain.tld:443,https://cloud\\.mydomain\\.tld:443 # enable for aliasgroup1
      # - aliasgroup2=https://cloud.otherdomain.tld:443,https://cloud\\.otherdomain\\.tld:443 # enable for aliasgroup2
      # - aliasgroup3=https://cloud.somedomain.tld:443,https://cloud\\.somedomain\\.tld:443 # enable for aliasgroup3
      - username=admin
      - password=********         # Replace with a strong password
      - extra_params=--o:ssl.enable=true
      - dictionaries=en_US,de_DE
    ports:
      - "9980:9980"
    restart: always
```
> [!TIP]
> `- domain` automatic for first/single domain
>
> `- aliasgroup` iterating 1,2,3 for multiple domains IMPORTANT: keep `\\`
>
> `- dictionaries` add or remove dictionaries comma seperated
>
> WOPI is not necessary when defining aliasgroups
>


## Collabora Docker options (multiple nextcloud clients)

Each `aliasgroup` represents the allowed "client" domain, which will prevent unregistered clients from accessing the CODE server. Thus using aliasgroups resolves the issue of allowed WOPI client IP's.

> [!TIP]
> disable the `- domain=` environment (by commenting `#`) when using `- aliasgroup=`, 
> enable `- aliasgroup` by uncommenting `#`
>
> be aware of the domain formatting conventions when defining WOPI clients using `\\` as separator before `.`

```
# - domain=cloud.mydomain.tld  # Replace with your actual domain # disable when using aliasgroups
- aliasgroup1=https://cloud.mydomain.tld:443,https://cloud\\.mydomain\\.tld:443
- aliasgroup2=https://cloud.otherdomain.tld:443,https://cloud\\.otherdomain\\.tld:443
- aliasgroup3=https://cloud.somedomain.tld:443,https://cloud\\.somedomain\\.tld:443
```

## Dictionaries

```
-e ‘dictionaries=de_DE,en_GB,en_US’
```

Nextcloud Office Statistics and Admin interface:

```
https://office.mydomain.xyz/browser/dist/admin/admin.html
```

## Reverse proxy manager config example 

Be aware that you are forwarding **https** and **Websockets support** (WSS) only!

![grafik](https://github.com/user-attachments/assets/5dd028ce-bf08-4c7c-8826-8005fce591a8)

Be aware **HSTS** is disabled for your certificate

![grafik](https://github.com/user-attachments/assets/eab4d71c-7848-433a-acd4-7ffaca35a55c)

----
