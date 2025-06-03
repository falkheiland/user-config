# Jellyfin

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
    1. MyApps > Jellyfin
        - `Stop`
        - Settings
        - [ ] Open port
        - [ ] Expose app on local network
        - [ ] Enable authentication
        - [x] Expose app on the internet
        - `Update`
2. Runtipi
    1. MyApps > Jellyfin
        - `Start`
3. Authentik
    1. Forward-Auth
        1. Applications > Providers
            - Create: Proxy Provider
            - Name: `jellyfin-forwardauth-provider`
            - Authorization flow: default-provider-authorization-implicit-consent
            - Forward auth (single application)
            - External host: `https://jellyfin.example.com`
        2. Applications > Applications
            - Create
            - Name: `jellyfin-forwardauth-app`
            - Slug: `jellyfin-forwardauth-app`
            - Provider: `jellyfin-forwardauth-provider`
            - Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
        3. Outposts
            - Edit
            - `>>` (add all) 
    2. OAuth2
        1. Applications > Providers
            - Create: OAuth2/OpenID Provider
            - Name: `jellyfin-oauth2-provider`
            - Authorization flow: default-provider-authorization-implicit-consent
            - Note: `Client ID`
            - Note: `Client Secret`
            - Redirect URIs/Origins:
                - Strict: `https://freshrss.example.com/i/oidc/`
                - Strict: `https://freshrss.example.com:443/i/oidc/`
        2. Applications > Applications
            - Create
            - Name: `jellyfin-oauth2-app`
            - Slug: `jellyfin`
            - Provider: `jellyfin-oauth2-provider`
            - Launch Url: `https://jellyfin.example.com/sso/OID/start/authentik` 
            - Open in new tab
            - download [icon (png)](https://selfh.st/icons/)
            - Icon: Browse (downloaded icon)
4. Jellyfin
    1. Jellyfin UI
        - login
        - Profile > Dashboard
            - Catalog > Settings
                - `+` (add)
                    - Repository Name: `jellyfin-plugin-sso`
                    - Repository URL: `https://raw.githubusercontent.com/9p4/jellyfin-plugin-sso/manifest-release/manifest.json`
                    - `Save`
            - Catalog > Authentication Provider > SSO Authentication: Install
5. Tipi
    1. MyApps > Jellyfin
        - `Restart`
6. Jellyfin
    1. Jellyfin UI
        - login
        - Profile > Dashboard
            - Catalog > Authentication Provider > SSO Authentication: Settings
                - Name of OID Provider: `authentik`
                - OID Endpoint: : `https://auth.example.com/application/o/jellyfin/.well-known/openid-configuration`
                - OpenID Client ID: Enter `Client ID`
                - OID Secret: Enter `Client Secret`
                -  Enabled: [x]
                -  Enable Authorization by Plugin: [x]
                -  Enable All Folders: [x]
                - `Save`
            - General > Branding
                - Login disclaimer:
                    ```
                    <form action="https://jellyfin.example.com/sso/OID/start/authentik">
                    <button class="raised block emby-button button-submit">
                        Sign in with SSO
                    </button>
                    </form>
                    ```
                - Custom CSS code: 
                    ```
                    a.raised.emby-button {
                    padding: 0.9em 1em;
                    color: inherit !important;
                    }

                    .disclaimerContainer {
                    display: block;
                    }
                    ```
                - Save
        - logout
        - `Sign in with SSO`

## KB

SSO redirect issue: ` Redirect URI Error The request fails due to a missing, invalid, or mismatching redirection UR(redirect_uri).`:

<https://github.com/9p4/jellyfin-plugin-sso/issues/264>

add `<SchemeOverride>https</SchemeOverride>` to ~/runtipi/app-data/falkheiland/jellyfin/data/config/data/plugins/configurations/SSO-Auth.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<PluginConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <SamlConfigs />
  <OidConfigs>
    <item>
      <key>
        <string>authentik</string>
      </key>
      <value>
        <PluginConfiguration>
            ...
          <SchemeOverride>https</SchemeOverride>
            ...
        </PluginConfiguration>
      </value>
    </item>
  </OidConfigs>
</PluginConfiguration>
```