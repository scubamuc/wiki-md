
Some Nextcloud snap settings may need to be done directly in the `config.php` configuration file. While the default configurations are mostly fine, it may be necessary to fine tune Nextcloud snap.

> [!TIP]
> **Nextcloud documentation**
> 
> [Nextcloud configuration parameters](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/config_sample_php_parameters.html#configuration-parameters)

## Nextcloud snap, editing configuration file

The configuration file can be edited manually. 

> [!IMPORTANT]
> Take care to preserve the syntax and special characters, as incorrect formatting or misplaced characters and spaces may render your Nextcloud unusable. So be sure to **backup** the original `config.php` file **before editing**.

* **Backup** `config.php`
```
sudo cp /var/snap/nextcloud/current/nextcloud/config/config.php config.php.bak
``` 

* **Edit** `config.php` 
```
sudo nano /var/snap/nextcloud/current/nextcloud/config/config.php 
```

## Setting **trusted domains**

Set trusted domains (iterating values 0, 1, 2...):
```bash
sudo nextcloud.occ config:system:set trusted_domains 0 --value=cloud.yourdomain.xyz
```
Adding multiple trusted domains manually in `config.php`:

* edit `config.php` 
```bash
sudo nano /var/snap/nextcloud/current/nextcloud/config/config.php 
``` 
and add/edit section
```
 'trusted_domains' =>
  array (
    0 => 'cloud.yourdomain.xyz',
    1 => 'cloud.yourotherdomain.zxy',
    2 => 'localhost',
    3 => 'nextcloud.local',
  ),
```
> [!CAUTION]
> Be aware that the values for `trusted_domains` should be domain names (see [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)). IP addresses are not domains! See [hosts & FQDN configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Hosts-and-FQDN-for-Nextcloud-snap#fqdn-fully-qualified-domain-name).

## Setting trusted proxies

Adding trusted proxies manually in `config.php`:

edit `config.php` and add/edit section

    sudo nano /var/snap/nextcloud/current/nextcloud/config/config.php 

```
  'trusted_proxies' => 
  array (
    0 => 'your.reverse.proxy.ip',
    1 => 'your.other.proxy.ip',
   ),
```  
or issue command on host (iterating values 0, 1, 2...):

    sudo nextcloud.occ config:system:set trusted_proxies 0 --value="your.reverse.proxy.ip"

>[!CAUTION]
> Be aware that these values should be IPv4 or IPv6 addresses in [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) notation see [reverse proxy documentation](https://docs.nextcloud.com/server/stable/admin_manual/configuration_server/reverse_proxy_configuration.html)

## Setting overwrite parameters

[Nextcloud overwrite parameters](https://docs.nextcloud.com/server/27/admin_manual/configuration_server/reverse_proxy_configuration.html#overwrite-parameters) such as `overwriteprotocol` and `overwritehost` are two configs that absolutely depend on your reverse proxy setup.
 
Be aware that some devices require an additional setting in `config.php` if your Nextcloud snap instance is behind a [reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy#nginx-optional-custom-path-location-for-reverse-proxy).

For internet facing Nextcloud snap that handles encryption itself or a pass-through reverse proxy is present it shouldn't be necessary to set `overwriteprotocol` and `overwritehost`. 

edit `config.php` and add/edit section

```
    'overwriteprotocol' => 'https',
```
```
    'overwritehost' => 'cloud.yourdomain.xyz',
```

or issue command:

```
sudo nextcloud.occ config:system:set overwriteprotocol --value="https"

```

```
sudo nextcloud.occ config:system:set overwritehost --value="cloud.yourdomain.xyz"

```

## Setting default maintenance window
Set default maintenance window time 01:00 (1 a.m.)

edit `config.php` and add/edit line in `config.php`
```
'maintenance_window_start' => 1,
```
or issue command on host:

    sudo nextcloud.occ config:system:set maintenance_window_start --value="1"


## Setting default phone region
[country codes](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/config_sample_php_parameters.html#user-experience)

edit `config.php` and add/edit line in `config.php`
```
'default_phone_region' => 'US',
```
or issue command on host:

    sudo nextcloud.occ config:system:set default_phone_region --value="US"


## Setting default language
[country codes](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/config_sample_php_parameters.html#user-experience)

edit `config.php` and add/edit line in `config.php`
```
'default_language' => 'en',
```
or issue command on host:

    sudo nextcloud.occ config:system:set default_language --value="en"

## Setting default locale
[country codes](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/config_sample_php_parameters.html#user-experience)

edit `config.php` and add/edit line in `config.php`
```
'default_locale' => 'en_US',
```
or issue command on host:

    sudo nextcloud.occ config:system:set default_locale --value="en_US"


## Setting default landing app

edit `config.php` and add/edit line in `config.php`

>default value is `""` (empty)

```
  'defaultapp' => '<appname>',

```
or issue command on host:

    sudo nextcloud.occ config:system:set defaultapp --value="<appname>"

example: `sudo nextcloud.occ config:system:set defaultapp --value="mail"`

or

<p align="centre" width="100%">
    <img width="25%" src="https://github.com/user-attachments/assets/7ce6a2b4-02b5-4b79-b899-70ca84d3e380" alt="landing app">
</p>



## Setting default folder for shares

edit `config.php` and add/edit line in `config.php`

```
'share_folder' => '/SHARE_with_others',
```
or issue command on host:
```
sudo nextcloud.occ config:system:set share_folder --value="/SHARE_with_others"
```

## Setting default folder for shared with me

edit `config.php` and add/edit line in `config.php`

```
'share_folder' => '/SHARED_with_me',
```
or issue command on host:
```
sudo nextcloud.occ config:system:set share_folder --value="/SHARED_with_me"
```

## Setting trashbin retention

> [!NOTE]
> The default setting keeps files and folders in the trashbin for 30 days and automatically deletes anytime after that if space is required. 
>
> add/edit line in `config.php` and add `trashbin_retention_obligation`
> ```
> 'trashbin_retention_obligation' => 'auto',
> ```
> to **enable** the background job issue command:
> ```
> sudo nextcloud.occ config:app:delete files_trashbin background_job_expire_trash
> ```

**Example**: keep files and folders in the trash bin for at least 90 days and delete when exceeds 120 days 

edit `config.php` and add/edit line in `config.php`

```
'trashbin_retention_obligation' => '90, 120',
```
or issue command on host:
```
sudo nextcloud.occ config:system:set trashbin_retention_obligation --value="90, 120"
```
to **enable** the background job issue command:
```
sudo nextcloud.occ config:app:delete files_trashbin background_job_expire_trash
```
to **disable** the background job issue command:
```
sudo nextcloud.occ config:app:set --value=no files_trashbin background_job_expire_trash
```
see docs: https://docs.nextcloud.com/server/latest/admin_manual/configuration_files/trashbin_configuration.html

> [!TIP]
> Be aware that files may not be deleted automatically if space is available. 
> To cleanup the trashbin manually run:
> ```
> sudo nextcloud.occ trashbin:cleanup --all-users
> ```
> also note, there is a difference between trashbin and groupfolders-trashbin. To cleanup groupfolders-trashbin run:
> ```
> sudo nextcloud.occ groupfolders:trashbin:cleanup -f 
> ```

## **Skeleton** for default Nextcloud documents, files and folders etc.

Default documents, files and folders for new users may be placed in a "skeleton" directory. Thus new users will find documents files an folders in their Nextcloud upon first login.
Create a skeleton directory ` /var/snap/nextcloud/common/skeleton `
```
sudo mkdir /var/snap/nextcloud/common/skeleton
```
Add your default files folders here. You may also add group folders here. See [Group folders app](https://apps.nextcloud.com/apps/groupfolders)

edit `config.php` and add skeleton directory path
```
'skeletondirectory' => '/var/snap/nextcloud/common/skeleton',
```

or issue command on host:
```
sudo nextcloud.occ config:system:set skeletondirectory --value="/var/snap/nextcloud/common/skeleton"
```

## Remove signup link

edit `config.php` and add/edit line in `config.php`

```
'simpleSignUpLink.shown' => false,

```
or issue command on host:
```
sudo nextcloud.occ config:system:set simpleSignUpLink.shown --value="false"
```

## Disable reset password link

  If your user backend does not allow password resets (e.g. when it's a
  read-only user backend like LDAP), disable password reset link

edit `config.php` and add/edit line in `config.php`

```
'lost_password_link' => 'disabled',

```
or issue command on host:
```
sudo nextcloud.occ config:system:set lost_password_link --value="disabled"
```
you can specify a custom link, where the user is redirected to, when clicking the "reset password" link after a failed login-attempt

  `'lost_password_link' => 'https://example.org/link/to/password/reset',`
