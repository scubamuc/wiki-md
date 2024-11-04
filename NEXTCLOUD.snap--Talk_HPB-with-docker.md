# High Performance Backend for Talk on Nextcloud snap with Docker

## Talk:HPB

This example will require Docker and a [Reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy) for the signalling server, forwarding and encrypting HTTP port 8181 to https://signal.yourdomain.tld

## Create Docker Stack

1. set reverse proxy signal domain to forward (http & WSS) to the endpoints ip:8181
2. allow inbound bypass for TURN on port 3478 (your.domain.tld:3478) 
   - no encryption necessary for TURN as this will be managed by Nextcloud

> [!TIP]
> make sure you have a looong `secretpassword`

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
      - TURN_SECRET=secretpassword #this must be a long password
      - SIGNALING_SECRET=secretpassword #this must be a long password
      - TZ=Europe/Berlin
      - TALK_PORT=3478
      - INTERNAL_SECRET=secretpassword #this must be a long password
    restart: unless-stopped
    read_only: true
    tmpfs:
      - /var/log/supervisord
      - /var/run/supervisord
      - /opt/eturnal/run
      - /conf
      - /tmp
```

![grafik](https://github.com/user-attachments/assets/b5e08e15-8ddc-42cf-bbf8-0292ca551821)

![grafik](https://github.com/user-attachments/assets/228206d8-11c7-47b8-ad02-b65f69214533)

![grafik](https://github.com/user-attachments/assets/254064a4-326f-4cdd-bc01-41298152a61e)
