# Install RSS news reader for Nextcloud snap with docker

[FreshRSS](https://github.com/FreshRSS/FreshRSS/tree/edge/Docker) is a lightweight self-hosted RSS feed aggregator which integrates perfectly into Nextcloud and is simple to install and configure. 

<p align="center" width="100%">
    <img width="25%" src="https://github.com/user-attachments/assets/a2767f8d-2c6e-4ce5-9e0b-528731ba2632" alt="first start">
</p>

This example will require [Docker](https://www.docker.com/) and a [Reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy) for the news server, forwarding and encrypting HTTP port 80 to `https://mews.mydomain.tld`.

## create & run docker stack

```
version: "2.1"
services:
  freshrss:
    image: lscr.io/linuxserver/freshrss:latest
    container_name: freshrss
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Berlin
      - CRON_MIN=1,31
    volumes:
      - /path/to/data:/config
    ports:
      - 80:80
    restart: unless-stopped
```

Add your news server to Nextcloud "external sites" and enjoy your daily news feeds in your Nextcloud.
