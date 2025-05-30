# Immich

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
    1. MyApps > Immich
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
            - Name: `immich-oauth2-provider`
            - Redirect URIs/Origins: `https://immich.example.com/apps/user_oidc/code`
            - **Resulting settings from this procedure are used in step 3.2.1 !**
        2. Applications > Applications
            - Create
            - Name: `immich-oauth2-app`
            - Provider: `immich-oauth2-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
        3. Applications > Applications
            - Create
            - Name: `immich-forwardauth-app`
            - Slug: `immich-forwardauth-app`
            - Provider: `immich-forwardauth-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
3. Immich
    1. immich UI
        - login
        - Administration -> Settings -> Authentification Settings -> OAuth
        - Login with OAuth: Enable
        - **settings from step 2.1.1**
        - ISSUER_URL: https://auth.example.com/application/o/immich/
        - CLIENT_ID: ep2PXF74...IDxkAVz1j
        - CLIENT_SECRET: V5f4P...jC4ilk
        - SCOPE: openid email profile
        - MOBILE REDIRECT URI: app.immich:/
4. Runtipi
    1. MyApps > Immich
        - `Start`

## KB

upgrade existing user to admin:

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