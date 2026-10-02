> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Single sign-on

> Require your organization's members to sign in through your own identity provider

Single sign-on (SSO) lets members of your organization sign in to Xata through your company's identity provider instead of with a
password or social logins. You prove you own an email domain, connect the identity provider for that domain, and can then
require everyone with an address on it to sign in through that provider.

<Note>
  Single sign-on is a paid add-on. Please contact support to enable it for your organization.
</Note>

## Supported identity providers

Xata connects to identity providers over OpenID Connect (OIDC):

* **Google Workspace**: members sign in with their Workspace account. Google restricts sign-in to your domain.
* **Microsoft Entra ID**: members sign in with their Entra account, restricted to your own tenant.
* **OpenID Connect**: any provider that publishes an OpenID Connect discovery document, such as Okta or Auth0.

SAML is not supported out of the box. Contact us if your identity provider only supports SAML.

## How it works

SSO is configured per email domain. Each domain goes through three steps:

1. **Verify the domain.** Publish a DNS TXT record to prove your organization controls it.
2. **Connect an identity provider.** Register Xata with your provider and enter the credentials it issues.
3. **Require SSO.** Turn on enforcement so every account on the domain signs in through your provider.

Nothing changes for anyone until you complete the third step. You can add more than one domain, and each domain has its own identity provider.

Only organization [Admins](/docs/platform/organization-roles) can view and change single sign-on settings. To open them, select **Organization Settings** in the sidebar, then **Single sign-on**.

## Step 1: Verify your domain

1. Under **Add an email domain**, enter the domain your members' email addresses end in, for example `acme.com`, and select **Add domain**.
2. Xata shows a TXT record for the domain. Add it at your DNS provider exactly as shown:
   * **Record name**: `<label>._xata-sso.<your domain>`, for example `3f9a1c0d7e2b._xata-sso.acme.com`
   * **Record type**: `TXT`
   * **Record value**: `xata-domain-verification=<value>`
3. Select **Check DNS record**. DNS changes can take up to a few hours to spread, so if the record is not found yet, check again later.

Once the record is found, the domain is marked **Verified**.

<img src="https://mintcdn.com/xata/Pj5PQhsXA8ZN6iw-/images/platform/organization-sso-domain.png?fit=max&auto=format&n=Pj5PQhsXA8ZN6iw-&q=85&s=4a0bf2f50eed714b9e3b50c90e419711" alt="Domain awaiting DNS verification" className="rounded-lg" width="1440" height="900" data-path="images/platform/organization-sso-domain.png" />

<Warning>
  Keep the TXT record published for as long as you use single sign-on. Xata checks it daily. If the record has been missing for 72 hours, the domain is no longer verified, its identity provider is unlinked from it and SSO is no longer required, so members can sign in with a password again.
</Warning>

The record name and value are unique to your organization. A domain can belong to only one Xata organization; if another organization already verified it, adding it fails and you should contact support.

Subdomains are not covered by their parent domain. If your members use addresses on `eu.acme.com` as well as `acme.com`, add both domains.

## Step 2: Connect your identity provider

The domain card shows a **Redirect URI** as soon as you add the domain, so you can register it with your identity provider while DNS spreads. It has the form:

```
https://auth.xata.io/realms/xata/broker/<provider-id>/endpoint
```

Each domain has its own redirect URI, so copy it from the domain you are setting up.

Once the domain is verified, choose the provider type and enter its details.

<Tabs>
  <Tab title="Google Workspace">
    1. In the [Google Cloud console](https://console.cloud.google.com/auth/clients), create an OAuth 2.0 Client ID of type **Web application**.
    2. Add the redirect URI from Xata under **Authorized redirect URIs**.
    3. In Xata, select **Google Workspace** and paste the **Client ID** and **Client secret**.
  </Tab>

  <Tab title="Microsoft Entra ID">
    1. In the Microsoft Entra admin center, register an application and add the redirect URI from Xata as a **Web** redirect URI.
    2. Add a client secret under **Certificates & secrets**.
    3. In Xata, select **Microsoft Entra ID** and enter:
       * **Issuer URL**: `https://login.microsoftonline.com/<tenant-id>/v2.0`, using your tenant ID
       * **Client ID**: the application (client) ID
       * **Client secret**: the secret value

    The `common` and `organizations` issuers are refused, because they admit every Entra tenant rather than only yours.
  </Tab>

  <Tab title="OpenID Connect">
    1. In your provider, for example Okta or Auth0, register a web application and add the redirect URI from Xata as its sign-in redirect URI.
    2. In Xata, select **OpenID Connect** and enter:
       * **Issuer URL**: your provider's issuer, for example `https://acme.okta.com`. It must use `https://`.
       * **Client ID** and **Client secret** from the application.

    Xata reads the endpoints from the issuer's `/.well-known/openid-configuration` document, so you do not enter them by hand.
  </Tab>
</Tabs>

Select **Connect** followed by the provider name, for example **Connect Google Workspace**, to save. Xata checks the client ID and secret against your provider before saving, and rejects them if the provider does.

Xata requests the `openid`, `profile` and `email` scopes. The client secret is never shown again once saved. To change any provider setting later, select **Edit credentials** and enter the secret again.

<img src="https://mintcdn.com/xata/Pj5PQhsXA8ZN6iw-/images/platform/organization-sso-provider.png?fit=max&auto=format&n=Pj5PQhsXA8ZN6iw-&q=85&s=e803ce754d34d7021076548b41aee6af" alt="Connect an identity provider" className="rounded-lg" width="1184" height="1174" data-path="images/platform/organization-sso-provider.png" />

## Step 3: Require SSO

Turn on **Require SSO for \<domain>** on the domain card. The change takes effect immediately, and the domain shows an **SSO required** badge.

<img src="https://mintcdn.com/xata/Pj5PQhsXA8ZN6iw-/images/platform/organization-sso-required.png?fit=max&auto=format&n=Pj5PQhsXA8ZN6iw-&q=85&s=e16821e6f3d9d44351ab6419ffe80b54" alt="Domain with SSO required" className="rounded-lg" width="1440" height="1230" data-path="images/platform/organization-sso-required.png" />

While SSO is required, for every Xata account with an email address on the domain:

* Signing in with a password redirects to your identity provider.
* The **Google** and **GitHub** sign-in buttons redirect to your identity provider.
* Signing up with a password and resetting a password are refused.

This applies to Admins too, and to every account on the domain, whether or not it is already a member of your organization.

<Warning>
  If your identity provider stops accepting sign-ins while SSO is required, for example because the client secret was rotated or expired, nobody on the domain can sign in to Xata, including Admins. Keep the credentials in Xata up to date, and contact support if you are locked out.
</Warning>

You can turn enforcement off again at any time. Members can then sign in with a password or the Google and GitHub buttons again.

## Signing in with SSO

Once SSO is required, members enter their work email on the Xata sign-in page and are sent to your identity provider. There is no separate SSO button.

The first time someone signs in through your identity provider, they are added to your organization automatically, without an invitation. If they already have a Xata account with the same email address, they are asked to confirm linking it to your identity provider.

Your identity provider can only sign in email addresses on its own domain. Multi-factor authentication and other sign-in policies are enforced by your identity provider.

Xata does not support SCIM provisioning. Removing someone in your identity provider stops them from signing in again, but does not remove them from your organization or end sessions they already have. Remove departed members from your organization in Xata as well.

## Remove SSO

* **Disconnect** removes the identity provider from a domain. SSO is no longer required, and members on the domain sign in with a password or the Google and GitHub buttons again. The domain stays verified, so you can connect a different provider.
* **Remove domain** releases the domain from your organization. It is only available once no identity provider is connected to the domain.

Both actions ask you to type the domain name to confirm.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
