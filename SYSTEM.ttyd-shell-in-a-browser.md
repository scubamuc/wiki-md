# TTYD shell in a browser
see project README https://github.com/tsl0922/ttyd

see [ttyd-wiki](https://github.com/tsl0922/ttyd/wiki)

## Install TTYD as a snap

- Install ttyd from snapstore:  `sudo snap install ttyd --classic`
- Run quick test on host, issue command: `ttyd bash`
- Access shell in browser `http://localhost:7681`

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

### Example
```
ttyd -t fontSize=16 -p 8290 -O -W -m 1 -c <user>:<password> bash
```
* start ttyd with sane options:  
  * `-p` use `port:8290` or any other  
  * `-W` writeable  
  * `-O` check origin  
  * `-m 1` allow only 1 client  
  * `-c` request credentials on connect (replace <user>:<password> with your own **secure** credentials)
  * `-t fontSize=16` ⟶ change font size to 16
 
> [!CAUTION]
> Never make this available to the outside world without HTTPS encryption and **secure** credentials

> [!NOTE]
> For HTTPS encryption use a reverse proxy to forward host http on port 8290 to https, blocking common exploits, enabling websockets support and activating access lists!

### Start-up automatically on boot-up and execute as user

> [!NOTE]
> There are different methods to get TTYD to start on boot-up
> 1. [systemd service](https://linuxvox.com/blog/automatically-run-a-program-on-startup-under-linux-ubuntu/#method-1-using-systemd-recommended-for-services)
> 2. [systemd user service](https://linuxvox.com/blog/automatically-run-a-program-on-startup-under-linux-ubuntu/#optional-using-systemd-user-services-no-root-required)
> 3. Root crontab script start-up as user (*authors preference, example below*)

#### Create Root crontab script to start-up as user

* create a bash script `StartTTYD.sh` in user /bin directory:
`nano ~/bin/StartTTYD.sh`
```bash
  #!/bin/bash
##############################################################
# start ttyd #
##############################################################
ttyd -t fontSize=16 -p 8290 -O -W -m 1 -c user:'password' bash
```
* make script executable `chmod +x ~/bin/StartTTYD.sh`
* edit root crontab: `sudo crontab -e`
* add crontab `@reboot su - <USER> /home/<USER>/bin/StartTTYD.sh` (be aware, replace <USER> with your username, full path is required)
