# user-configs

## Table of contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Getting started](#getting-started)
- [Documentation](#documentation)
- [License](#license)
- [ToDos](#todos)

## Overview

[Runtipi](https://runtipi.io) offers the opportunity to customize the configuration of
[served apps](https://runtipi.io/docs/guides/customize-app-config) and the used
[reverse proxy](https://runtipi.io/docs/guides/customize-compose-and-traefik).

Runtipi is aimed at the use behind and via a VPN and or tunnel.
This repo contains a opinionated user-config that aims at having Runtipi:
- being used with a direct connection to the internet
- having advanced configuration like SSO/MFA, IP restrictions etc.

## Prerequisites

- working [Runtipi](https://runtipi.io/docs/getting-started/installation) installation (tested with v3.7.4)
- installed [Authentik](https://github.com/goauthentik/authentik) app (tested with 2024.10.5)
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
mkdir -p traefik/shared
mkdir -p traefik/plugins-local/src/github.com/nscuro/traefik-plugin-geoblock/
cd traefik/plugins-local/src/github.com/nscuro/traefik-plugin-geoblock/
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
tree -a user-config/
user-config/
...
├── authentik
│   ├── app.env.example
│   └── docker-compose.yml
...
├── crowdsec
│   └── docker-compose.yml
...
├── .git
│   ├── branches
...
├── .gitignore
...
├── tipi-compose.env.example
├── tipi-compose.yml
├── traefik
│   └── etc
│       ├── dynamic
│       │   ├── dynamic.yml
│       │   └── .gitkeep
│       ├── tls
│       │   └── .gitkeep
│       └── traefik.yml
...
# Find all env example files and rename them for use in runtipi
find ./user-config -type f -name "*.example" | while read file; do
    env_file="${file%.example}"
    mv "$file" "$env_file"
    echo "created env file $env_file"
done
# results (excerpt)
created env file ./user-config/freshrss/app.env
...
created env file ./user-config/tipi-compose.env
...

```

Open and edit each of the files from the result above in an editor of your choice.

## Getting started

```bash
sudo ./runtipi-cli start
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

## Documentation

- [Runtipi](./traefik/) (incl. traefik)
- Apps
  - [2FAuth](./2fauth)

***tbc***

## License

This project is licensed under the MIT License.

## ToDos

### Network segregation

- have separate networks for front- and backends
- with [docker version v28+](https://github.com/moby/moby/pull/48936) there will be the option to set gateway priorities for attached networks