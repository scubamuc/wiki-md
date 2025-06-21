# How to run a local browser within Nextcloud as an app

very often ports or sites are blocked in your physical location. Sometimes even VPN is blocked preventing acces to your
network. It would be great to access your own Network from within your Nextcloud instance or even open sites in a virtual
browser on your own network without being blocked on the physical network location.

Wouldn't it be nice to run a local network browser session as an app from within Nextcloud? Easy as pie!

* install Docker and Docker-compose
* set reverse proxy host for `browsevm` domain to forward and encrypt HTTP & WSS (Websockets Support) for port 3000 to `https://browsevm.yourdomain.tld`
* create and run a browsevm docker stack

```
---
services:
  firefox:
    image: lscr.io/linuxserver/firefox:latest
    container_name: firefox
    security_opt:
      - seccomp:unconfined #optional
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - FIREFOX_CLI=https://your.startpage.tld
      - TITLE=FirefoxVM
    volumes:
      - /config:/config
    ports:
      - 3000:3000
    shm_size: "1gb"
    restart: unless-stopped
  ```
* add external sites to Nextcloud instance pointing to `https://browsevm.yourdomain.tld`

![grafik](https://github.com/user-attachments/assets/a6012b9b-81f0-4cf7-bf21-5eec0d0f556c)

