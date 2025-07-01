# user-configs

This is the repository for my Custom Tipi user-config.

## Table of contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Getting started](#getting-started)
- [Documentation](#documentation)
- [Contribution](#contribution)
- [Contact](#contact)
- [License](#license)

## Overview

[Runtipi](https://runtipi.io) (also referred to just tipi) offers the opportunity to customize the configuration of
[served apps](https://runtipi.io/docs/guides/customize-app-config) and the used
[traefik reverse proxy](https://runtipi.io/docs/guides/customize-compose-and-traefik).

Runtipi is aimed at the use behind and via a VPN and or tunnel.
This repo contains a opinionated user-config that aims at having Runtipi:
- being used with a direct connection to the internet behind a NAT router
- having advanced configuration like SSO/MFA, IP restrictions etc.

Placeholders used in this guide, app specific descriptions and in *.example files:
- example.com: actual domain name
- 192.168.1.0/24: actual private ip address range

## Prerequisites

- working [Runtipi](https://runtipi.io/docs/getting-started/installation) installation (tested with v4.1.0)
- installed [Authentik](https://github.com/goauthentik/authentik) app (tested with 2025.4.1)
- installed [crowdsec](https://github.com/crowdsecurity/crowdsec) app and a [account](https://www.crowdsec.net/)
- a domain managed via [Cloudflare](https://cloudflare.com) DNS with a wildcard entry (ie *.example.com) pointing at your  public IP address


It is also a good measure to follow up on OS hardening.
This highly depends on the used host OS and the hosting itself and is not part of this repo.

Since this comes up a lot, here are still 2 things related to security, when Runtipi is used directly on the internet:
- [ufw on ubuntu](https://github.com/chaifeng/ufw-docker?tab=readme-ov-file#problem):
  > But when Docker is installed, Docker bypass the UFW rules and the published ports can be accessed from outside
  - follow the steps from the link above
  - use your instance behind an additional firewall / nat router.
- Runtipi offers the ability to expose services to the local domain.
If this local domain is spoofed and is set to the public IP, than those services are exposed!
  - use a traefik middleware that restricts the access to the effected traefik routers to local ip addresses

## Installation

- stop all apps in the runtipi GUI
- open the bash on your server and follow these steps:

```bash
# !!! this assumes runtipi was installed in your home directory.
# change dir to the runtipi dir
cd ~/runtipi
# stop runtipi
./runtipi-cli stop
# stop all containers (in case above command hangs )
docker stop $(docker ps -a -q)
# change dir to the app-data
cd app-data
# create dir structure for traefik app-data and install traefik plugin geoblock
mkdir -p app-data/traefik/shared
mkdir -p app-data/traefik/plugins-local/src/github.com/nscuro/traefik-plugin-geoblock/
cd app-data/traefik/plugins-local/src/github.com/nscuro/traefik-plugin-geoblock/
wget https://github.com/nscuro/traefik-plugin-geoblock/releases/download/v0.14.0/traefik-plugin-geoblock-0.14.0.tar.gz
tar -xzvf traefik-plugin-geoblock-0.14.0.tar.gz
rm traefik-plugin-geoblock-0.14.0.tar.gz
cd ~/runtipi
# display the traefik app-data dir structure
tree -L 5 -a app-data/traefik
app-data/traefik
├── plugins-local
│   └── src
│       └── github.com
│           └── nscuro
│               └── traefik-plugin-geoblock
└── shared
# clone the repo
# !!! it is recommended to fork that repo to your own account and clone from there.
# !!! this way you can work on and use your own configuration.
git clone git@github.com:falkheiland/user-config.git
# display the user-config dir structure (excerpt)
# tree -a -d -L 2 user-config/
user-config/
├── _archive
...
├── falkheiland
│   ├── arkanum
...
├── falkheiland-dev
├── .git
...
├── migrated
│   ├── 2fauth
...
│   └── wekan
└── traefik
    └── etc
...
# Find all env example files and rename them for use in runtipi
find ./user-config -type f -name "*.example" | while read file; do
    env_file="${file%.example}"
    mv "$file" "$env_file"
    echo "created env file $env_file"
done
# results (excerpt)
created env file ./user-config/falkheiland/authentik/app.env
...
created env file ./user-config/tipi-compose.env
...

```

Open and edit each of the files from the result above in an editor of your choice. The `./user-config/tipi-compose.env` contains the necessary env vars for the traefik reverse proxy - it is essential to edit this properly.

## Getting started

```bash
sudo ./runtipi-cli start --env-file user-config/tipi-compose.env
```

- open the traefik dashboard in your browser `http://<runtipi-IP>:8080/dashboard/#/`
  - there should be no errors shown for Routers, Services and Middlewares
  - if there are errors, fix them.
- open the runtipi GUI
- start the crowdsec app
- start the Authentik app
- open the Authentik GUI and make settings according to the README of each (used) app in the repo
- start each app after making above settings
- test the app

## Service

```bash
sudo vim /etc/systemd/system/tipi.service
```

```
[Unit]
Description=Runtipi service
Requires=docker.service multi-user.target
After=docker.service network-online.target

[Service]
Restart=always
RemainAfterExit=yes
WorkingDirectory=/home/user/runtipi
ExecStart=/home/user/runtipi/runtipi-cli start --env-file /home/user/runtipi/user-config/tipi-compose.env
ExecStop=/home/user/runtipi/runtipi-cli stop

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable tipi.service
sudo systemctl start tipi.service
sudo systemctl status tipi.service
journalctl -u tipi.service -f
```

## Tipps

start tipi app without tipi

```
docker compose --env-file app-data/crowdsec/app.env --env-file user-config/crowdsec/app.env --project-name crowdsec -f apps/crowdsec/docker-compose.yml -f repos/c5e7315954cfe5ab1eb1bf360ebada23b6a406ae66ae1e997854ad823f29aa7d/apps/docker-compose.common.yml --file user-config/crowdsec/docker-compose.yml up --detach --force-recreate --remove-orphans
```

## Documentation

- Apps
  - falkheiland
    - [2FAuth](./falkheiland/2fauth/)
    - [Arkanum](./falkheiland/arkanum/)
    - [Authentik](./falkheiland/authentik/)
    - [Bookstack](./falkheiland/bookstack/)
    - [Crowdsec](./falkheiland/crowdsec/)
    - [Cup](./falkheiland/cup/)
    - [DDNS-Updater-CF](./falkheiland/ddns-updater-cf/)
    - [docker-db-backup](./falkheiland/docker-db-backup/)
    - [Dozzle](./falkheiland/dozzle/)
    - [Freshrss-OIDC](./falkheiland/freshrss-oidc/)
    - [Home Assistant](./falkheiland/homeassistant/)
    - [Immich](./falkheiland/immich/)
    - [IT-Tools](./falkheiland/it-tools/)
    - [Jellyfin](./falkheiland/jellyfin/)
    - [Joplin Server](./falkheiland/joplin/)
    - [Linkwarden](./falkheiland/linkwarden/)
    - [MeTube](./falkheiland/metube/)
    - [Moodist](./falkheiland/moodist/)
    - [Navidrome](./falkheiland/navidrome/)
    - [Nextcloud-FPM](./falkheiland/nextcloud-fpm/)
    - [NocoDB](./falkheiland/nocodb/)
    - [Paperless-ngx](./falkheiland/paperless-ngx/)
    - [Pinchflat](./falkheiland/pinchflat/)
    - [SearXNG](./falkheiland/searxng/)
    - [Uptime Kuma](./falkheiland/uptime-kuma/)
    - [Vaultwarden](./falkheiland/vaultwarden/)
    - [Wallos](./falkheiland/wallos/)
    - [Wekan](./falkheiland/wekan/)

***tbc***

## Contribution

Issues and PRs are welcome.

## Contact

use channel `user-configs` on the [Runtipi Discord](https://discord.gg/Bu9qEPnHsc).

## License

This project is licensed under the MIT License.
