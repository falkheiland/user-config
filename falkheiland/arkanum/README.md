# Arkanum

App Store URL: https://github.com/falkheiland/runtipi-appstore/tree/prod

App Store ID: falkheiland

App Name: arkanum

## Usage

1. Runtipi
    1. MyApps > Arkanum
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
            - Name: `arkanum-oauth2-provider`
            - Redirect URIs/Origins: `https://arkanum.example.com/socialite/callback/openid`
            - **Resulting settings from this procedure are used in step 3!**
        2. Applications > Applications
            - Create
            - Name: `arkanum-oauth2-app`
            - Provider: `arkanum-oauth2-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
    2. ForwardAuth
        1. Applications > Providers
            - Create: Proxy Provider
            - Name: `arkanum-forwardauth-provider`
            - Slug: `arkanum`
            - Authorization flow: default-provider-authorization-implicit-consent
            - [x] Forward auth (single application)
            - External host: `https://arkanum.example.com/`
        2. Applications > Applications
            - Create
            - Name: `arkanum-forwardauth-app`
            - Slug: `arkanum-forwardauth-app`
            - Provider: `arkanum-forwardauth-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
        3. Applications > Outposts
            - authentik Embedded Outpost: Edit (icon)
            - add all applications: `>>`
            - `Update`
3. Editor / CLI
    - rename `app.env.example` to `app.env`
    - configure settings in `app.env`
4. Runtipi
    1. MyApps > arkanum
        - `Start`