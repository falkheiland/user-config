# nextcloud-fpm

## image repositories

public.ecr.aws/docker/library/nextcloud:31.0.0-fpm-alpine

## occ

example:

```
docker exec -u www-data nextcloud-fpm php occ files:scan --all
```



## OAuth2

### authentik

<https://docs.goauthentik.io/integrations/services/nextcloud/#openid-connect-auth>

### nextcloud

add to config.php:

```
  'allow_local_remote_servers' => true,
```

install app "OpenID Connect user backend". Configure Settings -> OpenID Connect

<https://nextcloud.example.com/settings/admin/user_oidc>

```
# Error: This app cannot be enabled because it makes the server unstable
# force enable plugin user_oidc
/var/www/html # php occ app:enable user_oidc --force
user_oidc 7.2.0 enabled
```

#User ID mapping: preferred_username


```
# make the OIDC provider the default login method:

/var/www/html # php occ config:app:set --value=0 user_oidc allow_multiple_user_backends

# list users
/var/www/html # php occ user:list
  - faaf2fd0e865adf0bab9c0a15c6e167a0348946fa19455ef1aabc7e8c9e18b71: John Doe

# add user to admin group
/var/www/html # php occ group:adduser admin faaf2fd0e865adf0bab9c0a15c6e167a0348946fa19455ef1aabc7e8c9e18b71
``