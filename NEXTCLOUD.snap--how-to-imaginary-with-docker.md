# How to use imaginary with Nextcloud snap using docker

create and run a docker stack for **imaginary** using [Nextcloud AIO image](https://github.com/nextcloud/all-in-one/blob/0e10cfd20b5c102d406f9b8f646563342403ae89/manual-install/latest.yml#L391-L416)

```
  nextcloud-aio-imaginary:
    image: nextcloud/aio-imaginary:latest
    user: "65534"
    init: true
    healthcheck:
      start_period: 0s
      interval: 30s
      timeout: 30s
      start_interval: 5s
      retries: 3
    expose:
      - "9000"
    environment:
      - TZ=${TIMEZONE}
      - IMAGINARY_SECRET
    restart: unless-stopped
```

[edit config.php](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php) and add, see [Nextcloud documentation](https://docs.nextcloud.com/server/latest/admin_manual/installation/server_tuning.html#previews)

```
<?php
'enabledPreviewProviders' => [
    'OC\Preview\MP3',
    'OC\Preview\TXT',
    'OC\Preview\MarkDown',
    'OC\Preview\OpenDocument',
    'OC\Preview\Krita',
    'OC\Preview\Imaginary',
],
'preview_imaginary_url' => 'http://<url of imaginary>',
```
