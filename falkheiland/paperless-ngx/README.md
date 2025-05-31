# Paperless-ngx

## App Store

- URL: https://github.com/falkheiland/runtipi-appstore/tree/prod
- ID: falkheiland

## Used by

- [x] Browser
- [x] App

## Authentik Provider

- [x] OAuth2
- [x] ForwardAuth

## Usage

1. Runtipi
    1. MyApps > Paperless-ngx
    - `Stop`
    - Settings
      - [ ] Open port
      - [ ] Expose app on local network
      - [ ] Enable authentication
      - [x] Expose app on the internet
      - `Update`
2. Authentik
    1. OAuth2
        1. Applications > Providers
            - Create: OAuth2/OpenID Provider
            - Authorization flow: default-provider-authorization-implicit-consent
            - Name: `Paperless-ngx-oauth2-provider`
            - Redirect URIs/Origins: `https://Paperless-ngx.example.com/apps/user_oidc/code`
            - **Resulting settings from this procedure are used in step 3.2.1 !**
        2. Applications > Applications
            - Create
            - Name: `Paperless-ngx-oauth2-app`
            - Provider: `Paperless-ngx-oauth2-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
        3. Applications > Applications
            - Create
            - Name: `Paperless-ngx-forwardauth-app`
            - Slug: `Paperless-ngx-forwardauth-app`
            - Provider: `Paperless-ngx-forwardauth-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
3. Paperless-ngx
    1. user-config: edit data in app.env
4. Runtipi
    1. MyApps > Paperless-ngx
        - `Start`

## KB

upgrade existing user to admin:

```
docker exec -it Paperless-ngx bash
```

```
root@f20bca915582:/# psql Paperless-ngx tipi
psql (14.10 (Debian 14.10-1.pgdg120+1))
Type "help" for help.

Paperless-ngx=# SELECT * FROM USERS;
Paperless-ngx=# UPDATE users SET "isAdmin"='t' WHERE email='newadmin@example.com';
UPDATE 1
Paperless-ngx=#
```