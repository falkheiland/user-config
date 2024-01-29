# uptime-kuma

## added

- localtime
- forwardAuth - middlewares: authentik@file

## docs

<https://github.com/louislam/uptime-kuma/wiki>

### forwardAuth

#### traefik

app-data/traefik/dynamic/dynamic.yml

```yml
http:
  middlewares:
    authentik:
      forwardAuth:
        address: http://authentik:9000/outpost.goauthentik.io/auth/traefik
        trustForwardHeader: true
        authResponseHeaders:
          - X-authentik-username
          - X-authentik-groups
          - X-authentik-email
          - X-authentik-name
          - X-authentik-uid
          - X-authentik-jwt
          - X-authentik-meta-jwks
          - X-authentik-meta-outpost
          - X-authentik-meta-provider
          - X-authentik-meta-app
          - X-authentik-meta-version
```
