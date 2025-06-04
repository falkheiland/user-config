# Linkwarden

## App Store

- URL: https://github.com/falkheiland/runtipi-appstore/tree/prod
- ID: falkheiland

## Used by

- [x] Browser
- [ ] App

## Authentik Provider

- [x] OAuth2
- [x] ForwardAuth

## Usage

1. Runtipi
    1. MyApps > Linkwarden
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
            - Name: `linkwarden-oauth2-provider`
            - Redirect URIs/Origins: `https://Linkwarden.example.com/apps/user_oidc/code`
            - **Resulting settings from this procedure are used in step 3.2.1 !**
        2. Applications > Applications
            - Create
            - Name: `linkwarden-oauth2-app`
            - Provider: `linkwarden-oauth2-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
        3. Applications > Applications
            - Create
            - Name: `linkwarden-forwardauth-app`
            - Slug: `linkwarden-forwardauth-app`
            - Provider: `linkwarden-forwardauth-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
4. Runtipi
    1. MyApps > Linkwarden
        - `Start`

## KB


edit: ~/runtipi/app-data/falkheiland/Linkwarden/data/Linkwarden/config/www/.env

```
# Set OIDC to be the authentication method
AUTH_METHOD=oidc

# Control if Linkwarden automatically initiates login via your OIDC system 
# if it's the only authentication method. Prevents the need for the
# user to click the "Login with x" button on the login page.
# Setting this to true enables auto-initiation.
AUTH_AUTO_INITIATE=true #false

# Set the display name to be shown on the login button.
# (Login with <name>)
OIDC_NAME=Authentik #SSO

# Name of the claims(s) to use for the user's display name.
# Can have multiple attributes listed, separated with a '|' in which 
# case those values will be joined with a space.
# Example: OIDC_DISPLAY_NAME_CLAIMS=given_name|family_name
OIDC_DISPLAY_NAME_CLAIMS=name

# OAuth Client ID to access the identity provider
OIDC_CLIENT_ID=3KDvh9...aykaSZB

# OAuth Client Secret to access the identity provider
OIDC_CLIENT_SECRET=4oGL2gu...5ufIIbT5

# Issuer URL
# Must start with 'https://'
OIDC_ISSUER=https://auth.example.com/application/o/linkwarden-oauth2-app/

# The "end session" (RP-initiated logout) URL to call during Linkwarden logout.
# By default this is false which disables RP-initiated logout.
# Setting to "true" will enable logout if found as supported by auto-discovery.
# Otherwise, this can be set as a specific URL endpoint.
OIDC_END_SESSION_ENDPOINT=false

# Enable auto-discovery of endpoints and token keys.
# As per the standard, expects the service to serve a 
# `<issuer>/.well-known/openid-configuration` endpoint.
OIDC_ISSUER_DISCOVER=true
```

- login with oidc
- log out

edit: ~/runtipi/app-data/falkheiland/Linkwarden/data/Linkwarden/config/www/.env

- #AUTH_METHOD=oidc

- login admin@admin.com:password
- settings > user > choose oidc user > user roles: admin > save
- logout

edit: ~/runtipi/app-data/falkheiland/Linkwarden/data/Linkwarden/config/www/.env

- AUTH_METHOD=oidc
- login with OIDC