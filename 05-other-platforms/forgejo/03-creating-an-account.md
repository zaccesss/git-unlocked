# Creating a Forgejo Account

**Difficulty:** 🟢 Beginner | **Time:** 25 minutes

Forgejo account setup works identically to Gitea for local accounts, SSH keys and access tokens. The meaningful difference is SAML: Forgejo includes SAML 2.0 SSO in the free edition, allowing sign-in with enterprise identity providers like Okta, Azure AD, Keycloak and OneLogin without paying for an enterprise licence. This file covers the complete account setup, including SAML for teams using enterprise SSO.

---

## Table of Contents

1. [Registration](#registration)
2. [Profile settings](#profile-settings)
3. [Two-factor authentication](#two-factor-authentication)
4. [SSH keys](#ssh-keys)
5. [Access tokens](#access-tokens)
6. [SAML sign-in (Forgejo exclusive)](#saml-sign-in-forgejo-exclusive)
7. [OAuth2 sign-in providers](#oauth2-sign-in-providers)
8. [Account security checklist](#account-security-checklist)
9. [Try It Yourself](#try-it-yourself)
10. [Common Mistakes](#common-mistakes)
11. [Summary](#summary)
12. [Sources](#sources)

---

## Registration

### Standard registration

1. Navigate to the Forgejo instance URL
2. Click **Register**
3. Fill in username, email and password
4. Verify email if required by the instance
5. Sign in

Codeberg (the largest public Forgejo instance) at [codeberg.org](https://codeberg.org) provides free registration for open source projects.

### Registration restrictions

Administrators can configure:
- **Require email confirmation**: must click verification link before account is active
- **Email domain allowlist**: only `@yourcompany.com` addresses can register
- **Admin approval required**: accounts are inactive until an administrator approves
- **Registration disabled**: invite-only or admin-created accounts only

---

## Profile settings

Access settings via your avatar (top right) > **Settings**.

**Basic profile:**
- Full name, website, bio, location and avatar
- Avatar uses Gravatar based on email if no custom image is set

**Username change:**
- Avatar > **Settings** > **Account** > enter new username and current password
- Changing username breaks all existing repository URLs and Git remotes

**Theme and language:**
- Avatar > **Settings** > **Appearance**
- Forgejo ships the same themes as Gitea: Auto, Light, Dark and others

---

## Two-factor authentication

### Enabling 2FA

1. Avatar > **Settings** > **Security**
2. Under **Two-Factor Authentication**, click **Enroll**
3. Scan the QR code with an authenticator app (Authy, Google Authenticator, Microsoft Authenticator, 1Password, Bitwarden)
4. Enter the 6-digit code to confirm
5. **Save your recovery codes** - shown once, store offline securely

### Recovery codes

Recovery codes are one-time-use backup codes for when you lose access to your authenticator app. Store them in a password manager or printed in a secure location. Use one at the 2FA prompt if needed, then regenerate a new set immediately.

> [!WARNING]
> Without recovery codes and your authenticator app, you are locked out and must contact the instance administrator to regain access.

### Instance-wide 2FA enforcement

Administrators can require all users to have 2FA enabled via `REQUIRE_SIGNIN_2FA = true` in app.ini. Users without 2FA are redirected to setup on next login.

---

## SSH keys

SSH keys allow passwordless Git operations. After setup, `git clone`, `git push` and `git pull` work without entering credentials.

### Generating an SSH key pair

🐧 **Linux**:
```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

🍎 **Mac**:
```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

Accept the default location (`~/.ssh/id_ed25519`). Add a passphrase.

### Adding the public key to Forgejo

**Copy the public key:**

🐧 **Linux**:
```bash
cat ~/.ssh/id_ed25519.pub
# copy the output
```

🍎 **Mac**:
```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

**Add to Forgejo:**
1. Avatar > **Settings** > **SSH / GPG Keys**
2. Click **Add Key**
3. Enter a label (e.g. "Work laptop")
4. Paste the public key
5. Click **Add Key**

### Testing the connection

```bash
ssh -T git@forgejo.example.com
```

Expected response:
```
Hi yourusername! You've successfully authenticated, but Forgejo does not provide shell access.
```

### Clone via SSH

From any repository's Clone dropdown, select SSH:

```bash
git clone git@forgejo.example.com:username/repository.git
```

### Custom SSH port

If the instance uses a non-standard SSH port, update `~/.ssh/config`:

```
Host forgejo.example.com
    Port 2222
    User git
```

---

## Access tokens

Access tokens authenticate API requests and HTTPS Git operations. They replace your account password for programmatic access.

### Creating a token

1. Avatar > **Settings** > **Applications**
2. Under **Manage Access Tokens**, click **Generate Token**
3. Give it a descriptive name: `CI pipeline`, `Local scripts`, `VS Code`
4. Select scopes:

| Scope | Read | Write |
|---|---|---|
| **repository** | Clone and read code | Push code, manage repos |
| **issue** | View issues | Create, update, close |
| **organization** | View orgs | Manage org settings |
| **user** | View profile | Update profile |
| **package** | Download packages | Publish packages |
| **admin** | View admin data | Instance administration |

5. Click **Generate Token**
6. **Copy immediately** - shown only once

### Using a token for HTTPS Git

```bash
git clone https://forgejo.example.com/username/repo.git
# Username: your username
# Password: your access token
```

**Store credentials to avoid repeated prompts:**

🐧 **Linux**:
```bash
git config --global credential.helper store
```

🍎 **Mac**: Git Credential Manager stores in macOS Keychain automatically after first use.

### Token for API calls

```bash
curl -H "Authorization: token YOUR_ACCESS_TOKEN" \
  https://forgejo.example.com/api/v1/user
```

---

## SAML sign-in (Forgejo exclusive)

SAML 2.0 SSO is included in Forgejo's free community edition. This is the most significant authentication feature that differentiates Forgejo from Gitea community edition (where SAML requires the paid Enterprise tier).

### SAML overview

SAML (Security Assertion Markup Language) allows your identity provider (Okta, Azure AD, Keycloak, OneLogin, Google Workspace, etc.) to handle authentication. Users sign in through their corporate identity provider, which tells Forgejo "this user is authenticated." Users never enter a Forgejo-specific password.

Benefits:
- Single sign-on with corporate credentials
- Centralised user lifecycle management (deprovisioning on exit)
- MFA policies from the identity provider apply automatically
- Compliance-friendly audit trail

### Configuring SAML (admin task)

An administrator must set up the SAML connection. The configuration lives in `app.ini`:

```ini
[saml]
ENABLED            = true
SECURITY_KEY_FILE  = /etc/forgejo/saml-key.pem
SECURITY_CERT_FILE = /etc/forgejo/saml-cert.pem
IDP_METADATA_URL   = https://your-idp.example.com/saml/metadata
; Or use a metadata XML file:
; IDP_METADATA_FILE = /etc/forgejo/idp-metadata.xml
NAME_ID_FORMAT    = urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress
ATTRIBUTE_NAME    = uid
ATTRIBUTE_EMAIL   = email
ATTRIBUTE_FIRSTNAME = firstName
ATTRIBUTE_LASTNAME  = lastName
```

Generate the Forgejo SAML key and certificate:

```bash
openssl req -x509 -newkey rsa:4096 -keyout /etc/forgejo/saml-key.pem \
  -out /etc/forgejo/saml-cert.pem -days 3650 -nodes \
  -subj "/CN=forgejo.example.com"
```

The **Service Provider metadata URL** (needed to configure your IdP) is:
```
https://forgejo.example.com/user/saml/metadata
```

The **Assertion Consumer Service URL** (where the IdP sends responses) is:
```
https://forgejo.example.com/user/saml/acs
```

### Configuring your identity provider

**Okta:**
1. Create a new SAML 2.0 application
2. Set Single Sign On URL: `https://forgejo.example.com/user/saml/acs`
3. Set Audience URI: `https://forgejo.example.com/user/saml/metadata`
4. Map attributes: `email`, `firstName`, `lastName`
5. Download the IdP metadata XML and provide it to Forgejo

**Azure AD:**
1. Register an Enterprise Application
2. Set the Reply URL: `https://forgejo.example.com/user/saml/acs`
3. Set the Identifier: `https://forgejo.example.com/user/saml/metadata`
4. Copy the Federation Metadata Document URL for `IDP_METADATA_URL`

**Keycloak:**
1. Create a new Client with SAML protocol
2. Set the Client ID to `https://forgejo.example.com/user/saml/metadata`
3. Set the Valid Redirect URIs to include `https://forgejo.example.com/user/saml/acs`
4. Use the realm's SAML metadata URL for `IDP_METADATA_URL`

### Signing in with SAML

Once configured, the Forgejo sign-in page shows a SAML sign-in button alongside the local login form. Clicking it redirects to your identity provider for authentication, then back to Forgejo.

On first SAML sign-in, Forgejo creates a new account linked to the SAML identity. Subsequent sign-ins use the same account.

---

## OAuth2 sign-in providers

In addition to SAML, Forgejo supports OAuth2 for sign-in with external accounts. Administrators configure OAuth2 providers in **Site Administration** > **Authentication Sources**.

**Supported providers (built-in):**
- GitHub
- GitLab
- Forgejo (sign in with another Forgejo instance)
- Gitea
- Google
- Facebook
- Discord
- Microsoft / Azure AD
- Custom OpenID Connect provider

**Signing in with OAuth2:**
1. Click the provider button on the sign-in page
2. Authorise on the provider
3. Return to Forgejo signed in

---

## Account security checklist

- [ ] Strong unique password set (or using SAML/OAuth2)
- [ ] 2FA enabled with authenticator app
- [ ] Recovery codes saved offline securely
- [ ] SSH key added (ed25519 algorithm)
- [ ] SSH key has a passphrase
- [ ] Access tokens created with minimum required scopes
- [ ] Old or unused tokens deleted
- [ ] Email verified (if required by instance)
- [ ] SAML configured and tested if enterprise SSO is needed

---

## Try It Yourself

**Exercise 1 - Create an account on Codeberg**

1. Go to [codeberg.org](https://codeberg.org)
2. Click **Register** and create a free account
3. Set a profile picture and bio
4. Enable 2FA under Settings > Security

**Exercise 2 - Add an SSH key**

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"

# Mac:
pbcopy < ~/.ssh/id_ed25519.pub
# Linux:
cat ~/.ssh/id_ed25519.pub
```

Add to Codeberg under Settings > SSH / GPG Keys, then test:
```bash
ssh -T git@codeberg.org
```

**Exercise 3 - Create and test an access token**

1. Settings > Applications > Generate Token
2. Name: "Practice token", scope: repository:read
3. Copy the token

Test with the API:
```bash
curl -H "Authorization: token YOUR_TOKEN" \
  https://codeberg.org/api/v1/user | python3 -m json.tool
```

**Exercise 4 - Explore SAML settings (admin only)**

If you are administering a Forgejo instance:
1. Go to Site Administration > Authentication Sources
2. Click Add Authentication Source
3. Note the SAML option (not available in Gitea community edition)
4. Review the configuration fields

---

## Common Mistakes

**Using account password for HTTPS Git or API calls**

Always use access tokens, not your account password, for Git operations over HTTPS and API requests. Your password is for the web interface only.

**Not saving recovery codes after enabling 2FA**

Recovery codes are shown once. If you lose your authenticator app without saved recovery codes, you need administrator intervention to regain access. Save them now.

**Granting broad token scopes**

Create tokens with only the permissions needed. A token for reading repository contents does not need `admin` scope. Minimum scope = minimum risk if the token is exposed.

**Configuring SAML without testing thoroughly**

SAML configuration errors can lock everyone out if the IdP integration is broken. Always test SAML sign-in in an incognito window while remaining logged in as admin in another session, so you can fix problems without being locked out.

**Not updating Git remotes after moving from Gitea to Forgejo**

If you migrated from a Gitea instance to Forgejo, all existing `git remote` URLs in local repositories still point to the old server. Update with: `git remote set-url origin https://forgejo.example.com/username/repo.git`

---

## Summary

Forgejo account setup is identical to Gitea for local accounts, SSH keys and access tokens. The meaningful addition is SAML 2.0 authentication, included free in Forgejo's community edition.

SAML allows sign-in via enterprise identity providers (Okta, Azure AD, Keycloak). The administrator configures the connection in `app.ini` with the IdP metadata URL and attribute mappings. Users then see a SAML button on the sign-in page.

Enable 2FA with an authenticator app and save recovery codes offline. Add SSH public keys under Settings > SSH / GPG Keys for passwordless Git operations. Create access tokens with minimum required scopes for API and HTTPS Git authentication.

---

## Sources

- [Forgejo: SAML documentation](https://forgejo.org/docs/latest/admin/saml/)
- [Forgejo: SSH keys](https://forgejo.org/docs/latest/user/ssh/)
- [Forgejo: Access tokens](https://forgejo.org/docs/latest/user/access-token/)
- [Forgejo: Two-factor authentication](https://forgejo.org/docs/latest/user/2fa/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
