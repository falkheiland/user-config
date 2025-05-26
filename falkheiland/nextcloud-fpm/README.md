# Nextcloud-FPM

## App Store

- URL: https://github.com/falkheiland/runtipi-appstore/tree/prod
- ID: falkheiland
- Name: arkanum

## Used by

- [x] Browser
- [x] App

## Authentik Provider

- [x] OAuth2
- [ ] ForwardAuth

## Usage

1. Runtipi
    1. MyApps > Nextcloud-FPM
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
            - Name: `nextcloud-oauth2-provider`
            - Redirect URIs/Origins: `https://nextcloud.example.com/apps/user_oidc/code`
            - **Resulting settings from this procedure are used in step 3.2.1 !**
        2. Applications > Applications
            - Create
            - Name: `nextcloud-oauth2-app`
            - Provider: `nextcloud-oauth2-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
3. nextcloud
    1. Editor
        - add to config.php: `'allow_local_remote_servers' => true,`
    2. nextcloud UI
        - login with `Nextcloud user` from runtipi app settings
        - Apps -> Featured Apps -> Search: `openid`
        - OpenID Connect user backend -> `Download and enable`
        - Administration settings -> Administration -> OpenID Connect
        - Register a new provider
        1. Client configuration
            - **settings from step 2.1.1**
            - Identifier (max 128 characters): authentik
            - Client ID: ep2PXF74...IDxkAVz1j
            - Client secret: V5f4P...jC4ilk
            - Discovery endpoint: https://auth.example.com/application/o/nextcloud/.well-known/openid-configuration
            - Scope: email profile (omit openid if preferred)
4. Editor / CLI
    - rename `app.env.example` to `app.env`
    - configure settings in `app.env`
5. Runtipi
    1. MyApps > Nextcloud-FPM
        - `Start`

## Links

<https://docs.goauthentik.io/integrations/services/nextcloud/#openid-connect-auth>

<https://github.com/nextcloud/user_oidc>

## KB

make the OIDC provider the default login method:

```
/var/www/html # php occ config:app:set --value=0 user_oidc allow_multiple_user_backends
```

list users:

```
/var/www/html # php occ user:list
  - faaf2fd0e865adf0bab9c0a15c6e167a0348946fa19455ef1aabc7e8c9e18b71: John Doe
```

add user to admin group:

```
/var/www/html # php occ group:adduser admin faaf2fd0e865adf0bab9c0a15c6e167a0348946fa19455ef1aabc7e8c9e18b71
```