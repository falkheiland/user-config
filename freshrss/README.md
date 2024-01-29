# freshrss

using the original image, since linuxserver one, does not support headers used for OIDC.

https://hub.docker.com/r/freshrss/freshrss


## added

- original freshrss/freshrss image
- timezone
- cron
- oidc
- persistent volumes
- middleware for headers

# app.env

```
TIMEZONE=Europe/Berlin
OIDC_PROVIDER_METADATA_URL=https://auth.example.com/application/o/freshrss/.well-known/openid-configuration
OIDC_CLIENT_ID=1KEIHJfCsXmf56GDuOICazBlNKOK8rfGHtM4HCFO
OIDC_CLIENT_SECRET=5DPQS4JCVWHnsY1JTejh56Z9RmRTj9AIM3hK7c2uwGfla5ylfehr9k5IOHIXbGzRcezo98hR4aICyHuzg2a30NUaNmjGhJMxH29iGal88BzcDzYfXCyDBUMi87W1Q6zF
```


## docs

<https://freshrss.github.io/FreshRSS/en/>


### OIDC

<https://goauthentik.io/integrations/services/freshrss/>

<https://freshrss.github.io/FreshRSS/en/admins/16_OpenID-Connect.html>

<https://freshrss.github.io/FreshRSS/en/admins/16_OpenID-Connect-Authentik.html>

### FeedMe

<https://freshrss.example.net/api/greader.php>