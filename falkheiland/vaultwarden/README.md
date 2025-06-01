# vaultwarden

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
    1. MyApps > vaultwarden
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
            - Create: ...
        3. Applications > Applications
            - Create
            - Name: `vaultwarden-forwardauth-app`
            - Slug: `vaultwarden-forwardauth-app`
            - Provider: `vaultwarden-forwardauth-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
3. Runtipi
    1. MyApps > vaultwarden
        - `Start`
