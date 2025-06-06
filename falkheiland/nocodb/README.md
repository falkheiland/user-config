# NocoDB

## App Store

- URL: https://github.com/falkheiland/runtipi-appstore/tree/prod
- ID: falkheiland

## Used by

- [x] Browser
- [ ] App

## Authentik Provider

- [ ] OAuth2
- [x] ForwardAuth

## Usage

1. Runtipi
    1. MyApps > NocoDB
    - `Stop`
    - Settings
      - [ ] Open port
      - [ ] Expose app on local network
      - [ ] Enable authentication
      - [x] Expose app on the internet
      - Authentik related settings
      - `Update`
2. Authentik
    1. OAuth2
        1. Applications > Providers
            - Create: OAuth2/OpenID Provider
            - Authorization flow: default-provider-authorization-implicit-consent
            - Name: `nocodb-oauth2-provider`
            - Redirect URIs/Origins: `https://NocoDB.example.com/apps/user_oidc/code`
            - **Resulting settings from this procedure are used in step 3.2.1 !**
        2. Applications > Applications
            - Create
            - Name: `nocodb-oauth2-app`
            - Provider: `nocodb-oauth2-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
        3. Applications > Applications
            - Create
            - Name: `nocodb-forwardauth-app`
            - Slug: `nocodb-forwardauth-app`
            - Provider: `nocodb-forwardauth-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
4. Runtipi
    1. MyApps > NocoDB
        - `Start`
