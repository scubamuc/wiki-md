# How to generate image previews with Imaginary in Nextcloud snap with Docker

due to snap confinement the third party app ``previewgenrator` fails and the default Nextcloud preview generator is cumbersome.
However an external preview generation service is easily implemented and configured in Nextcloud snap for fast previews on the fly 
using "Imaginary" with Docker.

## Docker stack

```
services:
  imaginary:
    image: ghcr.io/nextcloud-releases/imaginary:latest
    # image: h2non/imaginary
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
### example preview array of file types
```
  'enabledPreviewProviders' =>
  array (
    0 => 'OC\\Preview\\Imaginary',
    1 => 'OC\\Preview\\TXT',
    2 => 'OC\\Preview\\PDF',
    3 => 'OC\\Preview\\OpenDocument',
    4 => 'OC\\Preview\\MSOfficeDoc',
    5 => 'OC\\Preview\\MarkDown',
    6 => 'OC\\Preview\\MP3',
    7 => 'OC\\Preview\\MP4',
    8 => 'OC\\Preview\\AVI',
    9 => 'OC\\Preview\\Movie',
    10 => 'OC\\Preview\\MKV',
    11 => 'OC\\Preview\\XCF',
    12 => 'OC\\Preview\\BMP',
    13 => 'OC\\Preview\\GIF',
    14 => 'OC\\Preview\\JPG',
    15 => 'OC\\Preview\\JPEG',
    16 => 'OC\\Preview\\SVG',
  ),
  'preview_imaginary_url' => 'http://192.168.2.16:8088',
```
