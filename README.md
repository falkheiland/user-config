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

```
user@tipi:~/runtipi/user-config$ tree -L 5 a /home/user/runtipi/app-data/traefik
a  [error opening dir]
/home/user/runtipi/app-data/traefik
├── dynamic
│   └── dynamic.yml
├── plugins
│   └── src
│       └── github.com
│           └── nscuro
│               └── traefik-plugin-geoblock
├── shared
│   └── acme.json
├── tls
│   ├── cert.pem
│   ├── tipi.example.com.txt
│   ├── key.pem
│   └── tipi.lan.txt
└── traefik.yml
```

### logrotate

user-config/tipi-compose.yml:

```
  tipi-reverse-proxy:
    volumes:
      - /var/log/traefik/:/var/log/
```

app-data/traefik/traefik.yml:

```
log:
  filePath: "/var/log/traefik.log"
  level: ERROR

accessLog:
  filePath: "/var/log/access.log"
```
sudo mkdir /var/log/traefik
sudo vim /etc/logrotate.d/traefik
```

/etc/logrotate.d/traefik:

```
/var/log/traefik/*.log {
        daily
        compress
        delaycompress
        logrotate 7
        postrotate
        docker kill --signal="USR1" tipi-reverse-proxy
        endscript
}
```

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

### prometheus

app-data/traefik/traefik.yml:

```
metrics:
  prometheus:
    manualRouting: true
    addEntryPointsLabels: true
    addRoutersLabels: true
    addServicesLabels: true
```

### tls options

```
tls:
  options:
    default:
      minVersion: VersionTLS13
      cipherSuites:
        - TLS_AES_128_GCM_SHA256
        - TLS_AES_256_GCM_SHA384
        - TLS_CHACHA20_POLY1305_SHA256
      curvePreferences:
        - CurveP521
        - CurveP384
      sniStrict: true
```