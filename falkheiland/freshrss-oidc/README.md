# FreshRSS-OIDC

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
    1. MyApps > FreshRSS-OIDC
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
            - Create: Proxy Provider
            - Name: `nextcloud-oauth2-provider`
            - Authorization flow: default-provider-authorization-implicit-consent
            - Forward auth (single application)
            - External host: `https://freshrss.example.com`
            - Unauthenticated Paths: `^/api/.*`
        2. Applications > Applications
            - Create
            - Name: `freshrss-forwardauth-app`
            - Slug: `freshrss-forwardauth-app`
            - Provider: `freshrss-forwardauth-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
3. Runtipi
    1. MyApps > FreshRSS-OIDC
        - `Start`

## Links

## KB
