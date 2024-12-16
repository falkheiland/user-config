# crowdsec

## Firewall Bouncer Configuration

[Crowdsec Docker Compose Guide Part 1: Powerful IPS with Firewall Bouncer](https://www.smarthomebeginner.com/crowdsec-docker-compose-1-fw-bouncer/)

> Note that Firewall Bouncer is to be installed on the Docker host (where runtipi runs) as a native app.


tipi host:

```bash
curl -s https://install.crowdsec.net | sudo sh
sudo apt update
sudo apt install crowdsec-firewall-bouncer-iptables
```

crowdsec container:

```bash
23943bdf7ae1:/# cscli bouncers add crowdsec-bouncer-tipi
API key for 'crowdsec-bouncer-tipi':

   xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

Please keep this key since you will not be able to retrieve it!
```

tipi host:

```bash
sudo vim /etc/crowdsec/bouncers/crowdsec-firewall-bouncer.yaml
```

```
api_url: http://127.0.0.1:8081/
api_key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
disable_ipv6: true
iptables_chains:
  - INPUT
#  - FORWARD
  - DOCKER-USER
```

```bash
sudo systemctl restart crowdsec-firewall-bouncer.service
```