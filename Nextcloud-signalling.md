# Setup Nextcloud Talk with TURN server

https://help.nextcloud.com/t/howto-setup-nextcloud-talk-with-turn-server/30794

https://www.metered.ca/tools/openrelay/

![grafik (2).png](.attachments.24884509/grafik%20%282%29.png)

https://github.com/strukturag/nextcloud-spreed-signaling/blob/master/docker/README.md

https://github.com/strukturag/nextcloud-spreed-signaling/blob/master/docker/docker-compose.yml

## How-to's

https://markus-blog.de/index.php/2020/11/20/how-to-run-nextcloud-talk-high-performance-backend-with-stun-turnserver-on-ubuntu-with-docker-compose/

## STUN

stun:meet-jit-si-turnrelay.jitsi.net:443

# signalling docker stack 

```
services:
  spreedbackend:
    build:
      context: ..
      dockerfile: docker/server/Dockerfile
      platforms:
        - "linux/amd64"
    volumes:
      - ./server.conf:/config/server.conf
    network_mode: host
    restart: unless-stopped
    depends_on:
      - nats
      - janus
      - coturn
  nats:
    image: nats:2.2.1
    volumes:
      - ./gnatsd.conf:/config/gnatsd.conf
    command: ["-c", "/config/gnatsd.conf"]
    network_mode: host
    restart: unless-stopped
  janus:
    build: janus
    command: ["janus", "--full-trickle"]
    network_mode: host
    restart: unless-stopped
  coturn:
    image: coturn/coturn:latest
    network_mode: host
    #
    # Update command parameters as necessary.
    #
    # See https://github.com/coturn/coturn/blob/master/README.turnserver for
    # available options.
    command:
      - "--realm"
      - "nextcloud.domain.invalid"
      - "--static-auth-secret"
      - "static_secret_same_in_server_conf"
      - "--no-stdout-log"
      - "--log-file"
      - "stdout"
      - "--stale-nonce=600"
      - "--use-auth-secret"
      - "--lt-cred-mech"
      - "--fingerprint"
      - "--no-software-attribute"
      - "--no-multicast-peers"
    restart: unless-stopped
```
