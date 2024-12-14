# runtipi (traefik)

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

