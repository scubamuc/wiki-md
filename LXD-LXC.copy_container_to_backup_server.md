# Syncronise/Copy containers between LXD nodes

If an LXD-cluster with three nodes is overkill for you, a cold standby failover can easily be configured and works fine with two nodes. Be sure to configure WOL (Wake on Lan) for the cold standby node and ensure regular (skripted) container syncronisation or do it manually. Note also, container snapshots on the primary node belong to the container and are syncronised to the secondary node. You may want to prevent snapshots from being syncronised for speed and space with the option `--instance-only`.

One caveat of this model is that the controller node holding the primary database is not present. Thus ensure that the containers on the secondary node are stopped as the syncronised containers have identical IP's as those on the primary node which causes network issues. Stopping all containers on startup sounds strange, since this is one of th great advantages of LXD... but for this use case add `@reboot sleep 60; lxc stop --all` to your crontab so that all containers are stopped after cold-starting the secondary LXD-backup server.

Assuming you have two identical servers **LXD1** (primary LXD-server) and **LXD2** (secondary LXD-backup). Both servers should be known to eachother by adding them to remotes respectively. Needless to say that passwordless SSH between both servers should be configured.

#### On production **LXD1** server

Check remote servers:

```
lxc remote list
```

add remote server:

on **LXD1**
```
lxc remote add LXD2 <ip.address.of.server>:8443
```

#### On secondary **LXD2** server

add remote server:

on **LXD2**
```
lxc remote add LXD1 <ip.address.of.server>:8443
```

#### On production **LXD1** server

Copy container to ***LXD2***

```
lxc copy CONTAINER LXD2: --stateless --refresh
```

Check containers on ***LXD2*** server:

```
lxc list LXD2:
```

Copying or syncronising containers between servers can be scripted.
Be sure to *stop* the containers to be syncronised on ***LXD2*** server.

Example script:

```
#!/bin/bash
####################################################################
## Syncronise/copy containers and snapshots from LXD1 to LXD2 server
####################################################################
## Stop all containers on backup-server
  ssh <ip.address.of.LXD2> 'lxc stop --all'
#copy container1
  lxc copy CONTAINER1 LXD2: --instance-only --stateless --refresh ;
#copy container2
  lxc copy CONTAINER2 LXD2: --stateless --refresh ;
exit
```
----

See lxc copy --help

```
lxc copy

Description:
  Copy instances within or in between LXD servers

  Transfer modes (--mode):
   - pull: Target server pulls the data from the source server (source must listen on network)
   - push: Source server pushes the data to the target server (target must listen on network)
   - relay: The CLI connects to both source and server and proxies the data (both source and target must listen on network)

  The pull transfer mode is the default as it is compatible with all LXD versions.

Usage:
  lxc copy [<remote>:]<source>[/<snapshot>] [[<remote>:]<destination>] [flags]

Aliases:
  copy, cp

Flags:
      --allow-inconsistent   Ignore copy errors for volatile files
  -c, --config               Config key/value to apply to the new instance
  -d, --device               New key/value to apply to a specific device
  -e, --ephemeral            Ephemeral instance
      --instance-only        Copy the instance without its snapshots
      --mode                 Transfer mode. One of pull, push or relay (default "pull")
      --no-profiles          Create the instance with no profiles applied
  -p, --profile              Profile to apply to the new instance
      --refresh              Perform an incremental copy
      --stateless            Copy a stateful instance stateless
  -s, --storage              Storage pool name
      --target               Cluster member name
      --target-project       Copy to a project different from the source

Global Flags:
      --debug          Show all debug messages
      --force-local    Force using the local unix socket
  -h, --help           Print help
      --project        Override the source project
  -q, --quiet          Don't show progress information
      --sub-commands   Use with help or --help to view sub-commands
  -v, --verbose        Show all information messages
      --version        Print version number
```
