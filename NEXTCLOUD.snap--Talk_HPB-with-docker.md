# Talk:HPB for Nextcloud snap with Docket

## Create Docker Stack

### set reverse proxy signal domain to forward (http & WSS) to the endpoints ip1:8181
### allow inbound bypass for 3478:ip1:3478

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
    read_only: true
    tmpfs:
      - /var/log/supervisord
      - /var/run/supervisord
      - /opt/eturnal/run
      - /conf
      - /tmp
```
