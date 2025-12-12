## AppAPI HaRP Docker sock proxy in Nextcloud snap

### docker stack

```
name: nc-appapi-harp
services:
    nextcloud-appapi-harp:
        image: ghcr.io/nextcloud/nextcloud-appapi-harp:release
        container_name: appapi-harp
        hostname: appapi-harp
        environment:
            - HP_SHARED_KEY=nc-snap%appapitest
            - NC_INSTANCE_URL=https://xcloud.scubamuc.dedyn.io
        volumes:
            - /var/run/docker.sock:/var/run/docker.sock
        network_mode: bridge
        ports:
            - 8780:8780
            - 8782:8782
        restart: unless-stopped
```

so we're all very excited about **Nextcloud Hub 25 Autumn** running nicely as a snap! 

one of the new features of Hub 25 is AppAPI which lets us install external apps as a docker container in Nextcloud. 

so to make this work; --> did this by the book (https://docs.nextcloud.com/server/latest/admin_manual/exapps_management/DeployConfigurations.html)
* you'll need to install docker on the same host running the snap `sudo apt install docker.io` simple enough!
* now you need to install a docker socket to be able to connect to the local docker instance and to be able to install docker containers from the Nextcloud AppAPI interface: --> issue the following command to install and run the docker socket proxy and the local reverse proxy (HaRP)
```
docker run \
  -e HP_SHARED_KEY="yourpasswordhere" \
  -e NC_INSTANCE_URL="https://127.0.0.1:8080" \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v `pwd`/certs:/certs \
  --name appapi-harp -h appapi-harp \
  --restart unless-stopped \
  -p 8780:8780 \
  -p 8782:8782 \
  -d ghcr.io/nextcloud/nextcloud-appapi-harp:release
```
* you'll be able to register a new AppAPI connection in Nextcloud Administration settings --> AppApi --> "+ Register Daemon":

<img width="599" height="734" alt="Image" src="https://github.com/user-attachments/assets/6a659514-fd4d-4233-8f67-deee1a3226ba" />

and successful connection tes:

<img width="311" height="85" alt="Image" src="https://github.com/user-attachments/assets/1542f5d0-2d83-4428-b8c2-84ebd613eb37" />

followed by a deployment test:

<img width="587" height="694" alt="Image" src="https://github.com/user-attachments/assets/fec584eb-1f27-4292-a702-4046100e8dc9" />

only to fail miserably 😵

@nextcloud-snap/developers what am I doing wrong?
the logs aren't very helpful to me:
```
[app_api] Error: Error executing occ command. Return code: 1, stdout: An unhandled exception has been thrown:
OCP\HintException: [0]: Memcache OC\Memcache\Redis not available for local cache (Is the matching PHP module installed and enabled?)
, stderr: 
	POST /index.php/apps/app_api/daemons/my_docker_socket_proxy/test_deploy
	from 2.205.233.223 by scubamuc at Dec 12, 2025, 3:59:40 PM
```
