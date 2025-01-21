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

https://help.nextcloud.com/t/additional-settings-email-configuration-solved/22070/17

```
"mail_smtpstreamoptions" => array(
'ssl' => array(
    'allow_self_signed' => true,
    'verify_peer' => false,
    'verify_peer_name' => false
  )
),
```
