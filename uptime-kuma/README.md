# uptime-kuma

## added

- localtime
- forwardAuth - middlewares: authentik@file
- geoblock - middlewares: geoblok@file

## docs

<https://github.com/louislam/uptime-kuma/wiki>

### forwardAuth

#### traefik

app-data/traefik/traefik.yml

```yml
experimental:
  plugins:
    traefik-plugin-geoblock:
      moduleName: "github.com/nscuro/traefik-plugin-geoblock"
      version: "v0.13.0"
```


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
    geoblock:
      plugin:
        traefik-plugin-geoblock:
          # Enable this plugin?
          enabled: true
          # Path to ip2location database file
          databaseFilePath: /plugins-storage/sources/gop-1769341418/src/github.com/nscuro/traefik-plugin-geoblock/IP2LOCATION-LITE-DB1.IPV6.BIN
          # Whitelist of countries to allow (ISO 3166-1 alpha-2)
          allowedCountries: [ "DE" ]
          # Blocklist of countries to block (ISO 3166-1 alpha-2)
          # blockedCountries: [ "RU" ]
          # Default allow indicates that if an IP is in neither block list nor allow lists, it should be allowed.
          defaultAllow: false
          # Allow requests from private / internal networks?
          allowPrivate: true
          # HTTP status code to return for disallowed requests (default: 403)
          disallowedStatusCode: 204
          # Add CIDR to be whitelisted, even if in a non-allowed country
          # allowedIPBlocks: ["66.249.64.0/19"]
          # Add CIDR to be blacklisted, even if in an allowed country or IP block
          # blockedIPBlocks: ["66.249.64.5/32"]
```
