# Nextcloud Talk recording backend with docker

```
services:
  nextcloud-talk-recording:
    image: ghcr.io/nextcloud-releases/aio-talk-recording:latest
    init: true
    ports:
      - "1234:1234"
    environment:
      - NC_DOMAIN=your.domain.tld
      - TZ=Europe/Berlin
      - RECORDING_SECRET=<longsecretpassphrase>
      - INTERNAL_SECRET=<longsectretpassphrase>
    shm_size: 2147483648
    restart: unless-stopped
    volumes:
      - nextcloud-talk-recordings:/nextcloud/recording
volumes:
  nextcloud-talk-recordings:
```
