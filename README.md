# user-config

required runtipi version: >= v3.1.1

## misc

stop tipi

```
sudo  ./runtipi-cli stop
```

stop all containers (in case above command hangs )

```
docker stop $(docker ps -a -q)
```

start tipi

```
sudo ./runtipi-cli start
```

restart tipi

```
sudo ./runtipi-cli restart
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

```yml
  runtipi-reverse-proxy:
    volumes:
      - /var/log/traefik/:/var/log/
```

app-data/traefik/traefik.yml:

```yml
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
        docker kill --signal="USR1" runtipi-reverse-proxy
        endscript
}
```

### traefik middleware ipAllowList

As a default, runtipis configuration of traefik makes any app accessible from the internet!
Adding a traefik middleware ipAllowList restricts access to local routers to specified networks only.

app-data/traefik/dynamic/dynamic.yml:

```yml
http:
  middlewares:
    local-ipallowlist:
      ipAllowList:
        sourceRange:
          - "192.168.1.0/24" # local IP Range
          #- "10.0.0.0/8" # 1/3 all private IP ranges
          #- "172.16.0.0/12" # 2/3 all private IP ranges
          #- "192.168.0.0/16" # 3/3 all private IP ranges
```

example runtipi dashboard / api / worker:

user-config/tipi-compose.yml:

```yml
  runtipi:
    labels:
    # ---- Dashboard ----- #
      # Secure
      traefik.http.routers.dashboard-local.middlewares: ipAllowList-local@file

      # ---- Worker ----- #
      # Secure
      traefik.http.routers.worker-local.middlewares: ipAllowList-local@file
      traefik.http.routers.worker-api-local.middlewares: ipAllowList-local@file
```

### traefik-plugin-geoblock


https://traefik.io/blog/using-private-plugins-in-traefik-proxy-2-5/

```bash
~/runtipi/app-data/traefik
mkdir -p plugins-local/src/github.com/nscuro/traefik-plugin-geoblock/
cd plugins-local/src/github.com/nscuro/traefik-plugin-geoblock/
wget https://github.com/nscuro/traefik-plugin-geoblock/releases/download/v0.14.0/traefik-plugin-geoblock-0.14.0.tar.gz
tar -xzvf traefik-plugin-geoblock-0.14.0.tar.gz
```



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
  runtipi-reverse-proxy:
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

### non-tipi-app

if you want to use traefik to expose a non-tipi-app, f.i. the GUI of a switch or a NAS in your internal network:

app-data/traefik/dynamic/dynamic.yml:

```yml
http:
  middlewares:
    default-https-redirect:
      redirectScheme:
        scheme: https
        permanent: true

  routers:
    non-tipi-app:
      entryPoints:
        - "web"
      rule: "Host(`non-tipi-app.example.com`)"
      middlewares:
       - default-https-redirect@file
      service: non-tipi-app
    non-tipi-app-secure:
      entryPoints:
        - "websecure"
      rule: "Host(`non-tipi-app.example.com`)"
      middlewares:
        - plugin-geoblock@file
      tls:
        options: sniStrictFalse@file
        certresolver: myresolver
      service: non-tipi-app

  services:
    non-tipi-app:
      loadBalancer:
        serversTransport: insecuretransport
        servers:
          - url: "https://192.168.0.100:1234"
        passHostHeader: true

tls:
  options:
    sniStrictFalse:
      minVersion: VersionTLS13 #VersionTLS12
      cipherSuites:
        - TLS_AES_128_GCM_SHA256
        - TLS_AES_256_GCM_SHA384
        - TLS_CHACHA20_POLY1305_SHA256
      curvePreferences:
        - CurveP521
        - CurveP384
      sniStrict: false
```

### prometheus

app-data/traefik/traefik.yml:

```yml
metrics:
  prometheus:
    manualRouting: true
    addEntryPointsLabels: true
    addRoutersLabels: true
    addServicesLabels: true
```

### tls options

```yml
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

```yml
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

```yml
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
    default-https-redirect:
      redirectScheme:
        scheme: https
        permanent: true
    basicAuth-default:
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
    local-ipallowlist:
      ipAllowList:
        sourceRange:
          - "192.168.1.0/24" # local IP Range
          #- "10.0.0.0/8" # 1/3 all private IP ranges
          #- "172.16.0.0/12" # 2/3 all private IP ranges
          #- "192.168.0.0/16" # 3/3 all private IP ranges

  routers:
    non-tipi-app:
      entryPoints:
        - "web"
      rule: "Host(`non-tipi-app.example.com`)"
      middlewares:
       - default-https-redirect@file
      service: non-tipi-app
    non-tipi-app-secure:
      entryPoints:
        - "websecure"
      rule: "Host(`non-tipi-app.example.com`)"
      middlewares:
        - plugin-geoblock@file
      tls:
        options: sniStrictFalse@file
        certresolver: myresolver
      service: non-tipi-app

  services:
    non-tipi-app:
      loadBalancer:
        serversTransport: insecuretransport
        servers:
          - url: "https://192.168.0.100:1234"
        passHostHeader: true

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
    sniStrictFalse:
      minVersion: VersionTLS13 #VersionTLS12
      cipherSuites:
        - TLS_AES_128_GCM_SHA256
        - TLS_AES_256_GCM_SHA384
        - TLS_CHACHA20_POLY1305_SHA256
      curvePreferences:
        - CurveP521
        - CurveP384
      sniStrict: false
  certificates:
    - certFile: /etc/traefik/tls/cert.pem
      keyFile: /etc/traefik/tls/key.pem

```