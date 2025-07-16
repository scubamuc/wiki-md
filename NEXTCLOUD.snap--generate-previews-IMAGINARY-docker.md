# How to generate image previews with Imaginary in Nextcloud snap with Docker

due to snap confinement the third party app ``previewgenrator` fails and the default Nextcloud preview generator is cumbersome.
However an external preview generation service is easily implemented and configured in Nextcloud snap for fast previews on the fly 
using "Imaginary" with Docker.

## Docker stack

```
services:
  imaginary:
    image: h2non/imaginary
    ports:
      - "8088:8088"
    environment:
      - PORT=8088
    command: -concurrency 50 -enable-url-source
```

## Configure Nextcloud snap

edit config.php manually or issue command in host to enable preview generation
```
sudo nextcloud.occ config:system:set preview_imaginary_url --value="http://127.0.0.1:8088"
```
and
edit config.php manually or issue command in host to enable imaginary
```
sudo nextcloud.occ config:system:set enabledPreviewProviders 0 --value="OC\\Preview\\Imaginary"
``` 
