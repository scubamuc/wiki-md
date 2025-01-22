# Nextcloud additional SMTP settings for connecting DMZ to Exchange SMTP

## Allow self signed SMTP by adding ` StreamBuffer.php `

### Nextcloud is in DMZ connecting to an Exchange server through a NAT loopback requiring SSL with self signed certificate.

Edit config.php and add following section:

```
"mail_smtpstreamoptions" => array(
'ssl' => array(
    'allow_self_signed' => true,
    'verify_peer' => false,
    'verify_peer_name' => false
  )
),
```

https://help.nextcloud.com/t/additional-settings-email-configuration-solved/22070/17
