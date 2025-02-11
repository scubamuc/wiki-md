# High Performance Backend for Talk on Nextcloud snap with Docker

## Talk:HPB

A High Performance Backend (HPB) requires a signalling service and consists of three components working hand in hand;

1.  STUN service which is part of TURN for discovering device IP's behind NAT or simple single service like `stun.nextcloud.com:443`
2.  TURN service like "coturn" or "eturnal" for discovering and connecting NATed external IP's and controlling WebRTC streams
3.  signalling service like "janus" required for calls and conversations with multiple participants. Without it, all participants have to upload their own video individually for each other participant, which will most likely cause connectivity issues and a high load on participating devices.

Self-hosting all three services is not as daunting as it seems and thanks to the folks at [Nextcloud AIO](https://github.com/nextcloud/all-in-one) is easily installed running their docker image.

This example will require Docker and a Reverse proxy for the signalling server, forwarding and encrypting HTTP port 8181 to `https://signal.mydomain.tld`

* Set reverse proxy host for signal domain to forward http & WSS (Websockets Support) for port 8181
* Allow inbound bypass for TURN on port 3478 (your.domain.tld:3478) in Router/Firewall
  * no encryption necessary for TURN as this will be managed by Nextcloud


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

## Create and run Docker Stack

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
## Example Nextcloud STUN configuration,

![grafik](https://github.com/user-attachments/assets/1909f576-9e88-4d42-80ad-59ab858ede02)

## Example Nextcloud TURN configuration

![grafik](https://github.com/user-attachments/assets/abff3df2-a0fd-4a4f-aeae-ca6b4ad78cbe)

## Example Nextcloud HPB configuration

![grafik](https://github.com/user-attachments/assets/c657a323-2b9b-48a7-8a5d-6ccb66fa8703)

## Example NPM configuration

![grafik](https://github.com/user-attachments/assets/907309b8-076a-4457-b519-eb63381802ff)

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
