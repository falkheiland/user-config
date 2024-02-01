# user-config

## misc

stop tipi

```
 ./runtipi-cli stop
```

stop all containers (in case above command hangs )

```
docker stop $(docker ps -a -q)
```

start tipi

```
 ./runtipi-cli start
```

## traefik

### traefik-plugin-geoblock

https://github.com/nscuro/traefik-plugin-geoblock/releases/

manual installation required:

```
mkdir -p app-data/traefik/plugins/src/github.com/nscuro/traefik-plugin-geoblock

cd app-data/traefik/plugins/src/github.com/nscuro/traefik-plugin-geoblock

wget https://github.com/nscuro/traefik-plugin-geoblock/releases/download/v0.13.0/traefik-plugin-geoblock-0.13.0.tar.gz

tar -zxvf traefik-plugin-geoblock-0.13.0.tar.gz

rm traefik-plugin-geoblock-0.13.0.tar.gz
```
user-config/tipi-compose.yml:

```yml
version: '3.7'

services:
  tipi-reverse-proxy:
    volumes:
      - ./app-data/traefik:/root/.config
      - ./app-data/traefik/shared:/shared
      - ./app-data/traefik/plugins:/plugins-local
```
app-data/traefik/traefik.yml:

```yml
experimental:
  plugins:
    traefik-plugin-geoblock:
      moduleName: "github.com/nscuro/traefik-plugin-geoblock"
      version: "v0.13.0"
```

app-data/traefik/dynamic/dynamic.yml:

```yml
http:
  middlewares:
    geoblock:
      plugin:
        traefik-plugin-geoblock:
          # Enable this plugin?
          enabled: true
          # Path to ip2location database file
          #databaseFilePath: /plugins-storage/sources/gop-1769341418/src/github.com/nscuro/traefik-plugin-geoblock/IP2LOCATION-LITE-DB1.IPV6.BIN
          databaseFilePath:  /plugins-local/src/github.com/nscuro/traefik-plugin-geoblock/IP2LOCATION-LITE-DB1.IPV6.BIN
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