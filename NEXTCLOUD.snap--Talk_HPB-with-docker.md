# High Performance Backend for Talk on Nextcloud snap with Docker

## Talk:HPB

This example will require Docker and a [Reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy) for the signalling server, forwarding and encrypting HTTP port 8181 to https://signal.yourdomain.tld

> [!TIP]
> make sure you have a long `secretpasswordkey` (min. 24 chars, better 32 chars)
>
>#### create a long random password for each service;
> 
>
> ![grafik](https://github.com/user-attachments/assets/ba52530d-ed98-4857-a224-fb969be28a8d)
>
>
>1. TURN_SECRET
>
>```bash
>openssl rand -hex 32
>```
>
>2. SIGNALING_SECRET  
>
>```bash
>openssl rand -hex 32
>```
>
>3.  INTERNAL_SECRET
>
>```bash
>openssl rand -hex 32
>```

## Create Docker Stack

1. set reverse proxy signal domain to forward (http & WSS) to the endpoints ip:8181
2. allow inbound bypass for TURN on port 3478 (your.domain.tld:3478) 
   - no encryption necessary for TURN as this will be managed by Nextcloud

```
name: 'hpb'

services:

  nc-talk:
    container_name: talk_hpb
    image: nextcloud/aio-talk:latest
    init: true
    ports:
      - 3478:3478/tcp
      - 3478:3478/udp
      - 8181:8081/tcp
    environment:
      - NC_DOMAIN=cloud.yourdomain.tld
      - TALK_HOST=signal.somedomain.tld
      - TURN_SECRET=secretpassword #this must be a long secretpasswordkey
      - SIGNALING_SECRET=secretpassword #this must be a long secretpasswordkey
      - TZ=Europe/Berlin
      - TALK_PORT=3478
      - INTERNAL_SECRET=secretpassword #this must be a long secretpasswordkey
    restart: unless-stopped

```

![grafik](https://github.com/user-attachments/assets/b5e08e15-8ddc-42cf-bbf8-0292ca551821)

![grafik](https://github.com/user-attachments/assets/228206d8-11c7-47b8-ad02-b65f69214533)

![grafik](https://github.com/user-attachments/assets/254064a4-326f-4cdd-bc01-41298152a61e)

----

## Multiple signal servers

https://github.com/nextcloud/all-in-one/issues/5512#issuecomment-2453549033

### Docker Stack example

```
name: 'hpb'

services:

  nc-talk-1:
    container_name: nc_talk_1
    image: nextcloud/aio-talk:latest
    init: true
    ports:
      - 3478:3478/tcp
      - 3478:3478/udp
      - 8181:8081/tcp
    environment:
      - NC_DOMAIN=cloud.domain1.tld
      - TALK_HOST=signal1.somedomain.tld
      - TURN_SECRET=secret
      - SIGNALING_SECRET=secret
      - TZ=Pacific/Auckland
      - TALK_PORT=3478
      - INTERNAL_SECRET=secret
    restart: unless-stopped

  nc-talk-2:
    container_name: nc_talk_2
    image: nextcloud/aio-talk:latest
    init: true
    ports:
      - 3479:3478/tcp
      - 3479:3478/udp
      - 8281:8081/tcp
    environment:
      - NC_DOMAIN=cloud.domain2.tld
      - TALK_HOST=signal2.somedomain.tld
      - TURN_SECRET=secret
      - SIGNALING_SECRET=secret
      - TZ=Pacific/Auckland
      - TALK_PORT=3478
      - INTERNAL_SECRET=secret
    restart: unless-stopped

```
