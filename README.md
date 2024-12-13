# user-configs

## Table of contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Documentation](#documentation)
- [License](#license)

## Overview

[Runtipi](https://runtipi.io) offers the oppurtunity to customize the configuration of
[served apps](https://runtipi.io/docs/guides/customize-app-config) and the used
[reverse proxy](https://runtipi.io/docs/guides/customize-compose-and-traefik).

Runtipi s aimed at the use behind and via a VPN and or tunnel.
This repo contains a opinionated user-config that aims at having Runtipi:
- being used with a direct connection to the internet
- having advanced onfiguration like SSO/MFA, IP restrictiones etc.

## Prerequisites

- a working [Runtipi](https://runtipi.io/docs/getting-started/installation) installation.

It is also a good measure to follow up on OS hardening.
This highly depends on the used host OS and the hosting itself and is not part of this repo.

Since this comes up a lot, here are still 2 things related to security, when Rutipi is used directly on the internet.:
- [ufw on ubuntu](https://github.com/chaifeng/ufw-docker?tab=readme-ov-file#problem):
  > But when Docker is installed, Docker bypass the UFW rules and the published ports can be accessed from outside
  - follow the steps from the link above
  - use your instance behind an additional firewall / nat router.
- Runtipi offers the ability to expose services to the local domain. If this local domain is spoofed and is set to the public IP, than those services are exposed!
  - use a traefik middleware that restricts the access to the effected traefik routers to local ip addresses

## Installation

```bash
cd runtipi
git pull git@github.com:falkheiland/user-config.git
```
it is recommended to fork that repo to your own account and pull from there.
This way you can work on and use your own configuration.

## Documentation

- [Runtipi](#runtipi)
- Apps
  - [2FAuth](./2fauth/README.md)

### Runtipi


## License