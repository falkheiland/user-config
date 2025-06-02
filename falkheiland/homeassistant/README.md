# Home Assistant

## App Store

- URL: https://github.com/falkheiland/runtipi-appstore/tree/prod
- ID: falkheiland

## Used by

- [x] Browser
- [x] App

## Authentik Provider

- [ ] OAuth2
- [x] ForwardAuth

## Usage

1. Runtipi
    1. MyApps > Home Assistant
    - `Stop`
    - Settings
      - [ ] Open port
      - [ ] Expose app on local network
      - [ ] Enable authentication
      - [x] Expose app on the internet
      - `Update`
2. Authentik
    1. ForwardAuth
        1. Applications > Providers
            - Create: Proxy Provider
            - Name: `homeassistant-forwardauth-provider`
            - Slug: `homeassistant`
            - Authorization flow: default-provider-authorization-implicit-consent
            - [x] Forward auth (single application)
            - External host: `https://homeassistant.example.com/`
        2. Applications > Applications
            - Create
            - Name: `homeassistant-forwardauth-app`
            - Slug: `homeassistant-forwardauth-app`
            - Provider: `homeassistant-forwardauth-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
        3. Applications > Outposts
            - authentik Embedded Outpost: Edit (icon)
            - add all applications: `>>`
            - `Update`
3. Runtipi
    1. MyApps > Home Assistant
        - `Start`

## Links

## KB
