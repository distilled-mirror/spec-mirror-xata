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
  Keep the TXT record published for as long as you use single sign-on. Xata checks it daily, and emails your Admins when it first finds the record missing. If the record is still missing at the first daily check after 72 hours, the domain is no longer verified, its identity provider is disabled and SSO is no longer required. Admins get a second email when this happens.
</Warning>

While the domain is unverified, the identity provider stays connected but members cannot sign in through it. They can sign in with the Google and GitHub buttons, or with a password. Members who have only ever signed in through SSO have no password, so they need to select **Forgot Password?** on the sign-in page to set one.

To restore SSO, publish the TXT record shown on the domain card again and select **Check DNS record**. The domain is verified again and its identity provider is enabled, but SSO is not required until you turn **Require SSO for \<domain>** back on.

The record name and value are unique to your organization. A domain can belong to only one Xata organization. If another organization has claimed it, adding it fails, even if that organization's verification has lapsed. Contact support in that case.

Subdomains are not covered by their parent domain, and wildcard domains such as `*.acme.com` are not accepted. If your members use addresses on `eu.acme.com` as well as `acme.com`, add both domains.

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
    3. Under **API permissions**, make sure the application has the delegated Microsoft Graph permissions `openid`, `profile`, `email` and `User.Read`. Xata reads each member's profile from Microsoft Graph.
    4. In Xata, select **Microsoft Entra ID** and enter:
       * **Issuer URL**: `https://login.microsoftonline.com/<tenant-id>/v2.0`, using your tenant ID
       * **Client ID**: the application (client) ID
       * **Client secret**: the secret value

    The shared `common`, `organizations` and `consumers` issuers are refused, because they are not limited to your tenant.

    Xata takes each member's email address from the `mail` attribute of their Entra user, or from their user principal name if `mail` is empty. That address must be on the verified domain, or the sign-in is refused. A Microsoft Entra ID Free tenant is enough.
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

Xata requests the `openid`, `profile` and `email` scopes, plus `User.Read` for Microsoft Entra ID. The client secret is never shown again once saved. To change any provider setting later, select **Edit credentials** and enter the secret again.

Before you require SSO, check that a sign-in through the provider works. Once the provider is connected, the domain card shows a **Test sign-in** link. Open it in a private browser window and sign in with an account on the domain. You land on your account page, which lists the provider under **Connected Accounts**. If the sign-in fails, check your provider settings and select **Edit credentials** to fix them.

<img src="https://mintcdn.com/xata/Pj5PQhsXA8ZN6iw-/images/platform/organization-sso-provider.png?fit=max&auto=format&n=Pj5PQhsXA8ZN6iw-&q=85&s=e803ce754d34d7021076548b41aee6af" alt="Connect an identity provider" className="rounded-lg" width="1184" height="1174" data-path="images/platform/organization-sso-provider.png" />

## Step 3: Require SSO

Turn on **Require SSO for \<domain>** on the domain card. The change takes effect immediately, and the domain shows an **SSO required** badge.

If members on the domain cannot sign in afterwards, turn **Require SSO for \<domain>** off again from a window where you are still signed in. Their previous sign-in methods work again straight away.

<img src="https://mintcdn.com/xata/Pj5PQhsXA8ZN6iw-/images/platform/organization-sso-required.png?fit=max&auto=format&n=Pj5PQhsXA8ZN6iw-&q=85&s=e16821e6f3d9d44351ab6419ffe80b54" alt="Domain with SSO required" className="rounded-lg" width="1440" height="1230" data-path="images/platform/organization-sso-required.png" />

While SSO is required, for every Xata account with an email address on the domain:

* Signing in with a password redirects to your identity provider.
* The **Google** and **GitHub** sign-in buttons redirect to your identity provider.
* Signing up with a password and resetting a password are refused.

This applies to Admins too, and to every account on the domain, whether or not it is already a member of your organization.

<Warning>
  If your identity provider stops accepting sign-ins while SSO is required, for example because the client secret was rotated or expired, nobody on the domain can sign in to Xata, including Admins. Keep the credentials in Xata up to date, and contact support if you are locked out.
</Warning>

You can turn enforcement off again at any time. Members can then sign in with the Google and GitHub buttons, or with a password. Members who have only ever signed in through SSO have no password and need to select **Forgot Password?** to set one.

## Signing in with SSO

Once SSO is required, members enter their work email on the Xata sign-in page and are sent to your identity provider. There is no separate SSO button.

The first time someone signs in through your identity provider, they are added to your organization automatically, without an invitation. If they already have a Xata account with the same email address, they are asked to confirm linking it to your identity provider.

Your identity provider can only sign in email addresses on its own domain. Multi-factor authentication and other sign-in policies are enforced by your identity provider.

Xata does not support SCIM provisioning. Removing someone in your identity provider stops them from signing in again, but does not remove them from your organization or end sessions they already have. Remove departed members from your organization in Xata as well.

## Remove SSO

* **Disconnect** removes the identity provider from a domain. SSO is no longer required, and members on the domain sign in with the Google and GitHub buttons or a password, using **Forgot Password?** if they never set one. The domain stays verified, so you can connect a different provider.
* **Remove domain** releases the domain from your organization. It is only available once no identity provider is connected to the domain.

Both actions ask you to type the domain name to confirm.

## Using the CLI

Every step on this page is also available from the CLI under `xata organization sso`:

```bash theme={null}
xata organization sso domains add acme.com
xata organization sso domains verify acme.com
xata organization sso providers add --domain acme.com --type google --client-id <client-id>
# Find the provider alias with: xata organization sso show
xata organization sso providers test <alias>    # prints the Test sign-in link
xata organization sso providers enforce <alias>
```

`providers add` prompts for the client secret, or reads it from `XATA_SSO_CLIENT_SECRET`, so it stays out of your shell history. See the [CLI reference](/docs/cli/organization#sso) for every command and flag.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
