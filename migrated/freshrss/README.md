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

copy app.env.example to app.env and edit the values

## docs

<https://freshrss.github.io/FreshRSS/en/>


### OIDC

<https://goauthentik.io/integrations/services/freshrss/>

<https://freshrss.github.io/FreshRSS/en/admins/16_OpenID-Connect.html>

<https://freshrss.github.io/FreshRSS/en/admins/16_OpenID-Connect-Authentik.html>

### FeedMe

<https://freshrss.example.net/api/greader.php>