# TTYD shell in a browser (in your Nextcloud)

**Links**
*  [Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap)
*  [TTYD GitHub](https://github.com/tsl0922/ttyd)
   * [TTYD-wiki](https://github.com/tsl0922/ttyd/wiki)

In some situations you may need to access your local shell remotely only to find you're caught behind restrictive corporate firewalls where you're unable to VPN or SSH into your network, having only HTTPS available you're certainly stuck, unless you are able to connect to your shell over HTTPS in Nextcloud within a browser.

There are a couple of web-shell tools available, like [shellinabox](https://github.com/shellinabox/shellinabox) (development ceased ages ago) [WEtty](https://github.com/butlerx/wetty) (Snap discontinued) and [TTYD](https://github.com/tsl0922/ttyd). While SIAB was my go-to tool, there were security concerns. Being a snap user involved in the [Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap) community, I required a simple quick secure snap setup, to get up and running in minutes and as an added bonus, integrate shell access into my Nextcloud snap instance.

## Install TTYD as a snap

* Install ttyd from snapstore:  `sudo snap install ttyd --classic`
  - Run quick local test on host, issue command: `ttyd bash`
  - Access shell in local browser `http://localhost:7681`

### Options

USAGE:
    `ttyd [options] <command> [<arguments...>]`

``` 
    -p, --port              Port to listen (default: 7681, use `0` for random port)
    -i, --interface         Network interface to bind (eg: eth0), or UNIX domain socket path (eg: /var/run/ttyd.sock)
    -U, --socket-owner      User owner of the UNIX domain socket file, when enabled (eg: user:group)
    -c, --credential        Credential for basic authentication (format: username:password)
    -H, --auth-header       HTTP Header name for auth proxy, this will configure ttyd to let a HTTP reverse proxy handle authentication
    -u, --uid               User id to run with
    -g, --gid               Group id to run with
    -s, --signal            Signal to send to the command when exit it (default: 1, SIGHUP)
    -w, --cwd               Working directory to be set for the child program
    -a, --url-arg           Allow client to send command line arguments in URL (eg: http://localhost:7681?arg=foo&arg=bar)
    -W, --writable          Allow clients to write to the TTY (readonly by default)
    -t, --client-option     Send option to client (format: key=value), repeat to add more options
    -T, --terminal-type     Terminal type to report, default: xterm-256color
    -O, --check-origin      Do not allow websocket connection from different origin
    -m, --max-clients       Maximum clients to support (default: 0, no limit)
    -o, --once              Accept only one client and exit on disconnection
    -q, --exit-no-conn      Exit on all clients disconnection
    -B, --browser           Open terminal with the default system browser
    -I, --index             Custom index.html path
    -b, --base-path         Expected base path for requests coming from a reverse proxy (eg: /mounted/here, max length: 128)
    -P, --ping-interval     Websocket ping interval(sec) (default: 5)
    -6, --ipv6              Enable IPv6 support
    -S, --ssl               Enable SSL
    -C, --ssl-cert          SSL certificate file path
    -K, --ssl-key           SSL key file path
    -A, --ssl-ca            SSL CA file path for client certificate verification
    -d, --debug             Set log level (default: 7)
    -v, --version           Print the version and exit
    -h, --help              Print this text and exit
```

### Example configuration (good security)

* start ttyd with options:  
  * `-p` use non-default e.g `port:8290` or any other  
  * `-W` writeable  
  * `-O` check origin  
  * `-m 1` allow only 1 client  
  * `-c` request (basic auth) credentials on connect (replace `<user>`:`<password>` with your own **secure** credentials)
    * **NOTE**: these are HTTP access-user (`.htaccess`) credentials and not *system-user* credentials!
  * `-t fontSize=16` ⟶ change font size to 16 for better reading

Issue command in shell:
```
ttyd -t fontSize=16 -p 8290 -O -W -m 1 -c <user>:<password> bash
```
Added shell password request, issue command in shell:
```
ttyd -t fontSize=16 -p 8290 -O -W -m 1 -c <user>:<password> su - <sysuser>
```

> [!CAUTION]
> ### Security first
> Never make your shell available to the outside world without HTTPS encryption and **secure** credentials!
> * best security --> allow **only local access**
>   * optional --> use **VPN** for **local access**
> * good security --> use `-c <user>:<password>` for `.htaccess` request credentials!
>   * optional --> use reverse proxy access lists!
>   * optional --> connect as "dummy-user" and switch user on connect `su - myuser`!
>   * optional --> use a reverse proxy to enable/disable the host on demand!

> [!NOTE]
> ### SSL Encryption (HTTPS)
> For HTTPS encryption use a reverse proxy to forward host http on port 8290 to https, blocking common exploits, enabling websockets support and activating access lists!
> Enable / disable the host on demand!

### Start-up automatically on boot-up and execute as user

> [!TIP]
> There are different methods to get TTYD to start on boot-up
> 1. [systemd service](https://linuxvox.com/blog/automatically-run-a-program-on-startup-under-linux-ubuntu/#method-1-using-systemd-recommended-for-services), as root (*bad idea!*)
> 2. [systemd user service](https://linuxvox.com/blog/automatically-run-a-program-on-startup-under-linux-ubuntu/#optional-using-systemd-user-services-no-root-required), as user (*complicated to setup!*)
> 3. Root cronjob start-up as user (*authors preference, example below*)
> 4. Root cronjob to execute a script to start-up as user (*example below*)

#### Create root cronjob to start-up as system user

* edit root crontab: `sudo crontab -e`
* add crontab `@reboot ttyd -t fontSize=16 -p 8290 -W -O -m 1 -c <user>:<password> su - <sysuser>`
  * be aware, replace `<user>` with htaccess-user name and `<sysuser>` with your system user name

```bash
  @reboot ttyd -t fontSize=16 -p 8290 -W -O -m 1 -c <user>:<password> su - <sysuser>
```

#### Create root cronjob to execute a user-script to start-up as user

* create a bash script `StartTTYD.sh` in user /bin directory:
`nano ~/bin/StartTTYD.sh`
```bash
  #!/bin/bash
##############################################################
# start ttyd #
##############################################################
## for added security, request user password in shell
ttyd -t fontSize=16 -p 8290 -O -W -m 1 -c user:'password' su - user
## simple start-up in shell
# ttyd -t fontSize=16 -p 8290 -O -W -m 1 -c user:'password' bash
## for single shell command at start-up
# ttyd -t fontSize=16 -p 8290 -O -W -m 1 -c user:'password' top
```
* make script executable `chmod +x ~/bin/StartTTYD.sh`
* edit root crontab: `sudo crontab -e`
* add crontab `@reboot su - <USER> /home/<USER>/bin/StartTTYD.sh` (be aware, replace `<USER>` with your system username, full path is required)


----
----

# TTYD web-shell in your Nextcloud

1.  Install the [External sites app](https://apps.nextcloud.com/apps/external) in Nextcloud:
    * Add your web-shell to external sites
<p align="center" width="100%">
    <img width=50% alt="grafik" src="https://github.com/user-attachments/assets/6357620f-8c6b-4bf6-a877-86f71fe2f75a" />
</p>

2.  Access your web-shell from Nextcloud
    * Enter credentials
<p align="center" width="100%">
    <img width=50% alt="grafik" src="https://github.com/user-attachments/assets/0139239e-ede6-4baf-bf6c-c5fd3f594ec6" />
</p>

3.  Use web-shell in your Nextcloud instance
<p align="center" width="100%">
    <img width=50% alt="grafik" src="https://github.com/user-attachments/assets/a6bc396e-8920-459d-b3ef-e5905e840bbb" />
</p>

