# docker-db-backup

## App Store

- URL: https://github.com/falkheiland/runtipi-appstore/tree/prod
- ID: falkheiland

## Used by

- [ ] Browser
- [ ] App

## Authentik Provider

- [ ] OAuth2
- [ ] ForwardAuth

## Usage

1. Runtipi
    1. MyApps > docker-db-backup
    - `Stop`
    - Settings
      - [ ] Open port
      - [ ] Expose app on local network
      - [ ] Enable authentication
      - [ ] Expose app on the internet
      - `Update`
2. Editor / CLI
    1. docker-compose.yml
        - configure environment settings
    2. app.env.example
        - rename `app.env.example` to `app.env`
    3. app.env
        - configure settings in `app.env`
3. Runtipi
    1. MyApps > docker-db-backup
        - `Start`

## Links

## KB
