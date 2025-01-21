# Problems sending Mail, Nextcloud SMTP settings for sending Mail from DMZ NC to Exchange

## Allow Selfsigned SMTP

## Nextcloud is in the DMZ connecting to the Exchange server through a NAT loopback

```
  ),
  'mail_smtpstreamoptions' => 
  array (
    'ssl' => 
    array (
      'allow_self_signed' => true,
      'verify_peer' => false,
      'verify_peer_name' => false,
    ),
```
