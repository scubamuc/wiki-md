# CODE collabora on Docker (Portainer)

Install Docker Portainer on host. Create a DNS entry for subdomain `office.mydomain.xyz`. Create a proxy host for port 9980 on reverse proxy pointing to that host and get the domain name encrypted.

## Docker Portainer Stack:

Create a Portainer Stack.

```
version: '3.9'

services:
  collabora:
    image: collabora/code
    container_name: collabora
    environment:
      - domain=cloud.mydomain.com  # Replace with your actual domain
      - username=admin
      - password=********         # Replace with a strong password
      - extra_params=--o:ssl.enable=true
      - dictionaries=en_US,de_DE
    ports:
      - "9980:9980"
    restart: always
```

   

## Collabora Docker option (multiple nextcloud clients)

Replace `domain=xcloud.mydomain.com` with `aliasgroup1=https://cloud.mydomain.io:443,https://cloud\\.mydomain\\.io:443` followed by next domain iterating aliasgroup 1...3

```
- aliasgroup1=https://cloud.mydomain.io:443,https://cloud\\.mydomain\\.io:443
- aliasgroup2=https://xcloud.mydomain.io:443,https://xcloud\\.mydomain\\.io:443
- aliasgroup3=https://cloud.mydomain.com:443,https://cloud\\.mydomain\\.com:443
```

## Dictionaries

```
-e ‘dictionaries=eu de_DE en_GB’
```

View statistics and administration interface in your browser:

```
https://office.mydomain.xyz/browser/dist/admin/admin.html
```
