# immich

## upgrade existing user to admin

```
docker exec -it immich bash
```

```
root@f20bca915582:/# psql immich tipi
psql (14.10 (Debian 14.10-1.pgdg120+1))
Type "help" for help.

immich=# SELECT * FROM USERS;
immich=# UPDATE users SET "isAdmin"='t' WHERE email='newadmin@example.com';
UPDATE 1
immich=#
```