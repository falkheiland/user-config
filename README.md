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
user@tipi:~/runtipi/user-config$ tree -L 5 -a /home/user/runtipi/app-data/traefik
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
      - ./app-data/traefik:/etc/traefik
      - ./app-data/traefik/shared:/shared
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
          databaseFilePath:  /etc/traefik/plugins/src/github.com/nscuro/traefik-plugin-geoblock/IP2LOCATION-LITE-DB1.IPV6.BIN
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

## complete traefik.yml

```
api:
  dashboard: true
  insecure: true
  debug: false

providers:
  docker:
    endpoint: 'unix:///var/run/docker.sock'
    watch: true
    exposedByDefault: false
  file:
    directory: /etc/traefik/dynamic
    watch: true

entryPoints:
  web:
    address: ':80'

  websecure:
    address: ':443'
    http:
      tls:
        certResolver: myresolver

certificatesResolvers:
  myresolver:
    acme:
      email: user@example.com
      storage: /shared/acme.json
      httpChallenge:
        entryPoint: web

log:
  filePath: "/var/log/traefik.log"
  level: ERROR #DEBUG

accessLog:
  filePath: "/var/log/access.log"

metrics:
  prometheus:
    manualRouting: true
    addEntryPointsLabels: true
    addRoutersLabels: true
    addServicesLabels: true

experimental:
  plugins:
    traefik-plugin-geoblock:
      moduleName: "github.com/nscuro/traefik-plugin-geoblock"
      version: "v0.13.0"
```

## complete dynamic.yml

```
http:
  serversTransports:
    insecuretransport:
      insecureSkipVerify: true
  middlewares:
    # Note: when used in docker-compose.yml all dollar signs in the hash need to be doubled for escaping.
    # To create a user:password pair, the following command can be used:
    # echo $(htpasswd -nb user password) | sed -e s/\\$/\\$\\$/g
    #
    # Also note that dollar signs should NOT be doubled when they not evaluated (e.g. Ansible docker_container module).
    default-auth:
      basicAuth:
        users:
          - "user:$apr1$f.TtPBq.$KSde9T457Nn8Q9hUuHf.k/"
    secHeaders:
      headers:
        browserXssFilter: true
        contentTypeNosniff: true
        frameDeny: true
        stsIncludeSubdomains: true
        stsPreload: true
        stsSeconds: 31536000
        customRequestHeaders:
          X-Frame-Options: "SAMEORIGIN"
        customFrameOptionsValue: "SAMEORIGIN"
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

tls:
  options:
    default:
      minVersion: VersionTLS13 #VersionTLS12
      cipherSuites:
        - TLS_AES_128_GCM_SHA256
        - TLS_AES_256_GCM_SHA384
        - TLS_CHACHA20_POLY1305_SHA256
      curvePreferences:
        - CurveP521
        - CurveP384
      sniStrict: true
  certificates:
    - certFile: /root/.config/tls/cert.pem
      keyFile: /root/.config/tls/key.pem

```