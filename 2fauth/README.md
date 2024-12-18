# 2FAuth

- dynamic config

## Exposure

|IP:Port (IP)|Local domain (Loc)|Domain (Dom)|
|-|-|-|
| | |x |

## Settings

||Generic|IP:Port (IP)|Local domain (Loc)|Domain (Dom)|Domain (DomL)|
|-|-|-|-|-|-|
|Timezone|Runtipi| | | | |
|URLs|to (Dom)|||||
|ForwardAuth||||x||
|OAuth2||||x|x|

## Usage

1. Runtipi
    1. MyApps > 2FAuth
    - `Stop`
    - `Settings`
      - [ ] Open port
      - [ ] Expose app on local network
      - [x] Expose app on the internet
      - `Update`
2. Authentik
    1. OAuth2
        1. Applications > Providers
            - Create: OAuth2/OpenID Provider
            - Authorization flow: default-provider-authorization-implicit-consent
            - Name: `2fauth-oauth2-provider`
            - Redirect URIs/Origins: `https://2fauth.local.example.com/socialite/callback/openid`
            - **Resulting settings from this procedure are used in step 3!**
        2. Applications > Applications
            - Create
            - Name: `2fauth-oauth2-app`
            - Provider: `2fauth-oauth2-provider`
            - UI settings: Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
    2. ForwardAuth
        1. Applications > Providers
            - Create: Proxy Provider
            - Name: `2fauth-forwardauth-provider`
            - Slug: `2fauth`
            - Authorization flow: default-provider-authorization-implicit-consent
            - [x] Forward auth (single application)
            - External host: `https://2fauth.local.example.com/`
        2. Applications > Applications
            - Create
            - Name: `2fauth-forwardauth-app`
            - Slug: `2fauth-forwardauth-app`
            - Provider: `2fauth-forwardauth-provider`
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
    1. MyApps > 2FAuth
        - `Start`