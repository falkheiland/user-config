# ntfy

## added

- container user
- timezone
- localtime
- app cache
- app user
- app attachments
- basic auth

## docs

<https://docs.ntfy.sh/config/>

### user

#### traefik

app-data/traefik/dynamic/dynamic.yml

```yml
http:
  middlewares:
    # Note: when used in docker-compose.yml all dollar signs in the hash need to be doubled for escaping.
    # To create a user:password pair, the following command can be used:
    # echo $(htpasswd -nb user password) | sed -e s/\\$/\\$\\$/g
    #
    # Also note that dollar signs should NOT be doubled when they not evaluated (e.g. Ansible docker_container module).
    basicAuth-default:
      basicAuth:
        users:
          - "username:$apr1$f.TtPFg.$KS4TRp7n7Nn8Q56guHf.h/"
```
middlewares: basicAuth-default@file

#### container

attach shell

```
ntfy user add --role=admin username    # Add admin user username
```

