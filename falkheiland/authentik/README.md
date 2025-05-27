# authentik

## App Store

- URL: https://github.com/falkheiland/runtipi-appstore/tree/prod
- ID: falkheiland

## Used by

- [x] Browser
- [x] App

## Authentik Provider

n.a.

## Usage

1. Runtipi
    1. MyApps > authentik
    - `Stop`
    - Settings
      - [ ] Open port
      - [ ] Expose app on local network
      - [ ] Enable authentication
      - [x] Expose app on the internet
      - `Update`
2. Editor / CLI
    1. app.env.example
        - rename `app.env.example` to `app.env`
    2. app.env
        - configure settings in `app.env`
3. Runtipi
    1. MyApps > authentik
        - `Start`