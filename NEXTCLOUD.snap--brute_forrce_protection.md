# Brute-force protection

Nextcloud snap features brute-force protection app which will block an ip-address or disable a user after too many failed login attempts. 

## Unblock an ip-address issuing command on host:
```
sudo nextcloud.occ security:bruteforce:reset <ip-address>
```

or 

## Re-enable a disabled user by issuing command on host:
```
sudo nextcloud.occ user:enable <name of user>
```
