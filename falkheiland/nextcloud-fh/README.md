# nextcloud-fh

## image repositories

public.ecr.aws/docker/library/nextcloud:31.0.0-fpm-alpine

## occ

example:

```
docker exec -u www-data nextcloud-fh php occ files:scan --all
```



## OAuth2

### authentik

<https://docs.goauthentik.io/integrations/services/nextcloud/#openid-connect-auth>

### nextcloud

Install app "OpenID Connect user backend". Configure Settings -> OpenID Connect

<https://nextcloud.example.com/settings/admin/user_oidc>

User ID mapping: preferred_username

config.php:

```
  'allow_local_remote_servers' => true,
```

Making the OIDC provider the default login method:

```
docker exec -u www-data nextcloud-fh php occ config:app:set --value=0 user_oidc allow_multiple_user_backends
```

