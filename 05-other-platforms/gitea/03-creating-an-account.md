# Creating a Gitea Account

**Difficulty:** 🟢 Beginner | **Time:** 25 minutes

Unlike GitHub or GitLab, Gitea does not have a central hosted service you sign up to. Every Gitea installation is independent - if you are using Gitea, you are either using an instance someone else runs (your employer, a community-hosted instance like [gitea.com](https://gitea.com)) or running your own. This file covers the user account setup process: registration, securing your account with two-factor authentication, SSH keys, access tokens and OAuth2 sign-in.

---

## Table of Contents

1. [Registration](#registration)
2. [Profile settings](#profile-settings)
3. [Two-factor authentication](#two-factor-authentication)
4. [SSH keys](#ssh-keys)
5. [Access tokens](#access-tokens)
6. [OAuth2 sign-in providers](#oauth2-sign-in-providers)
7. [Email settings](#email-settings)
8. [Account security checklist](#account-security-checklist)
9. [Try It Yourself](#try-it-yourself)
10. [Common Mistakes](#common-mistakes)
11. [Summary](#summary)
12. [Sources](#sources)

---

## Registration

### Standard registration

If the Gitea instance allows public registration:

1. Navigate to the Gitea instance URL
2. Click **Register** in the top right
3. Fill in:
   - **Username**: your chosen username. This appears in all repository URLs: `https://gitea.example.com/{username}/{repo}`. Choose carefully - changing it later is possible but breaks all existing URLs.
   - **Email**: used for notifications and password recovery
   - **Password**: at least 8 characters (admins may have stricter requirements)
4. Complete any CAPTCHA
5. Click **Register Account**
6. Verify your email address if the instance requires it

### Registration restrictions

Gitea administrators can configure various registration restrictions:
- **Require email confirmation**: you must click a link in a verification email before your account is active
- **Email domain allowlist**: only specific email domains can register (e.g. only `@yourcompany.com`)
- **Admin approval required**: new accounts are disabled until an administrator approves them
- **Registration disabled**: no new accounts can be created (invite only or admin-created accounts only)

If registration is restricted, contact the instance administrator for access.

### First login after admin creates your account

If an administrator created your account, you will receive an email with a temporary password (if email is configured). Log in and change your password immediately under your avatar > **Settings** > **Security** > **Change Password**.

---

## Profile settings

Access your profile settings by clicking your avatar in the top right > **Settings**.

### Basic profile

- **Full name**: shown in the interface and on commits made via the web editor
- **Website**: a link displayed on your profile page
- **Description / Bio**: a short text about yourself
- **Location**: shown on your profile
- **Avatar**: upload an image, or Gitea uses a Gravatar based on your email

### Username

Your username appears in all repository and user URLs. Changing it:
1. Go to **Settings** > **Account**
2. Enter your new username and current password
3. Click **Change username**

> [!WARNING]
> Changing your username breaks all existing URLs and Git remote addresses pointing to your repositories. Anyone with a bookmark or Git clone of your repositories needs to update their references. For repositories, update remotes: `git remote set-url origin https://gitea.example.com/new-username/repo`

### Language and theme

1. Go to **Settings** > **Appearance**
2. Choose your preferred UI language
3. Choose a theme: Gitea Auto (follows system setting), Gitea Light, Gitea Dark, or others
4. Choose the editor theme for the web code editor

---

## Two-factor authentication

Two-factor authentication (2FA) adds a second verification step at sign-in. Even if someone knows your password, they cannot sign in without your second factor.

### Enabling 2FA

1. Go to **Settings** > **Security**
2. Under **Two-Factor Authentication**, click **Enroll**
3. Scan the QR code with an authenticator app:
   - **Authy** (iOS/Android/Desktop, cloud backup)
   - **Google Authenticator** (iOS/Android)
   - **Microsoft Authenticator** (iOS/Android)
   - **1Password** (includes TOTP)
   - **Bitwarden** (includes TOTP in premium)
4. Enter the 6-digit code shown in the app to confirm setup
5. **Save your recovery codes** - these are shown once. Store them offline in a secure location (password manager, printed document in a safe).

### Using 2FA at sign-in

After entering your username and password, Gitea asks for your 6-digit TOTP code. Enter the current code from your authenticator app. The code refreshes every 30 seconds.

### Recovery codes

Recovery codes are one-time-use backup codes. If you lose your authenticator app (phone broken, replaced, app deleted), use a recovery code to sign in:

1. At the 2FA prompt, click **Use recovery code**
2. Enter one of your recovery codes
3. After signing in, regenerate new recovery codes immediately (old ones are now partially used)
4. Set up 2FA again with your new device

> [!IMPORTANT]
> Recovery codes are single-use. Generate new ones after using any. Store them somewhere safe and offline. Without recovery codes and your authenticator app, you are locked out of your account and must contact the instance administrator.

### Instance-wide 2FA enforcement

If the administrator has enabled **REQUIRE_SIGNIN_2FA** in app.ini, all users must set up 2FA. If you log in without 2FA configured, Gitea redirects you to the 2FA setup page and prevents access until you complete it.

---

## SSH keys

SSH keys authenticate Git operations over SSH without passwords. Once configured, `git clone`, `git push` and `git pull` work without entering credentials.

### Generating an SSH key pair

🪟 **Windows** (PowerShell or Git Bash):
```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

🍎 **Mac**:
```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

🐧 **Linux**:
```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

Accept the default location (`~/.ssh/id_ed25519`). Add a passphrase to protect the private key.

### Adding the SSH key to Gitea

**Copy the public key:**

🪟 **Windows** (PowerShell):
```powershell
Get-Content ~/.ssh/id_ed25519.pub | clip
```

🍎 **Mac**:
```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

🐧 **Linux**:
```bash
cat ~/.ssh/id_ed25519.pub
```

**Add to Gitea:**
1. Go to **Settings** > **SSH / GPG Keys**
2. Click **Add Key**
3. Give it a label (e.g. "Work laptop", "Home desktop")
4. Paste the public key content
5. Click **Add Key**

### Testing the SSH connection

```bash
ssh -T git@gitea.example.com
```

Successful response:
```
Hi yourusername! You've successfully authenticated, but Gitea does not provide shell access.
```

### Cloning with SSH

Use the SSH clone URL from any repository page (switch from HTTPS to SSH in the Clone dropdown):

```bash
git clone git@gitea.example.com:username/repository.git
```

### Custom SSH port

If the Gitea instance uses a non-standard SSH port (common when the instance SSH conflicts with system SSH), the clone URL includes the port and `~/.ssh/config` may need updating:

```
# ~/.ssh/config
Host gitea.example.com
    Port 2222
    User git
```

Then clone normally:
```bash
git clone git@gitea.example.com:username/repository.git
```

---

## Access tokens

**Access tokens** (also called personal access tokens or API tokens) authenticate REST API requests, HTTPS Git operations and automation scripts.

### Creating an access token

1. Go to **Settings** > **Applications**
2. Under **Manage Access Tokens**, click **Generate Token**
3. Give it a name describing its purpose: "CI pipeline", "Local scripts", "VS Code"
4. Select the **Scopes** (permissions) needed:

| Scope | Read | Write |
|---|---|---|
| **repository** | Clone, read code | Push code, create repos, branches |
| **issue** | View issues | Create, comment, close issues |
| **organization** | View orgs | Manage org settings |
| **user** | View user profile | Update user settings |
| **notification** | View notifications | Mark as read |
| **package** | Download packages | Publish packages |
| **admin** | View admin data | Instance administration |

5. Click **Generate Token**
6. **Copy the token immediately** - it is only shown once

### Using an access token for HTTPS Git

When Git prompts for credentials on HTTPS operations:
- **Username**: your Gitea username
- **Password**: your access token (not your Gitea account password)

**Storing credentials:**

🪟 **Windows** - Git Credential Manager (included with Git for Windows) stores tokens automatically after first use.

🍎 **Mac** - macOS Keychain via Git Credential Manager.

🐧 **Linux**:
```bash
git config --global credential.helper store
# or cache for a session:
git config --global credential.helper 'cache --timeout=3600'
```

### Using an access token for API requests

```bash
# Include the token in the Authorization header
curl -H "Authorization: token YOUR_ACCESS_TOKEN" \
  https://gitea.example.com/api/v1/user

# Or as a query parameter (less secure, avoid for sensitive operations)
curl "https://gitea.example.com/api/v1/user?token=YOUR_ACCESS_TOKEN"
```

### Token expiry

Gitea access tokens do not expire by default. For security, consider rotating tokens periodically. Delete old tokens from **Settings** > **Applications** when they are no longer needed.

---

## OAuth2 sign-in providers

Gitea supports signing in with external OAuth2 providers, allowing users to use their existing GitHub, GitLab, Google or other accounts. This must be configured by the instance administrator.

### Supported providers (built-in)

- GitHub
- GitLab
- Gitea (sign in with another Gitea instance)
- Google
- Facebook
- Discord
- Forgejo
- Microsoft / Azure AD
- Custom OpenID Connect provider

### Signing in with OAuth2

If the administrator has configured OAuth2 providers:
1. On the sign-in page, click the provider button (e.g. "Sign in with GitHub")
2. You are redirected to the provider to authorise access
3. After authorising, you return to Gitea and are signed in

On first OAuth2 sign-in, Gitea creates a new account linked to your OAuth2 identity. Subsequent sign-ins use the same account.

### Linking OAuth2 to an existing account

If you already have a Gitea account and want to link it to an OAuth2 provider:
1. Go to **Settings** > **Security**
2. Under **Linked accounts**, click **Link** next to the provider
3. Authorise on the provider
4. Future sign-ins via that provider use your existing account

---

## Email settings

### Notification preferences

Control which events trigger email notifications:
1. Go to **Settings** > **Notifications**
2. Configure global preferences: which event types send email, whether to be notified about your own actions

### Repository-specific notifications

For each repository, you can set your notification level:
- **Watch**: receive notifications for all activity
- **Not Watching**: only receive notifications when @mentioned or assigned
- **Ignore**: receive no notifications regardless of @mentions

Click the eye icon on any repository page to change the level.

### Email address privacy

If you want to keep your email private in commits made via the web interface, go to **Settings** > **Privacy** and enable **Keep email address private**. Gitea uses a no-reply address for web commits.

---

## Account security checklist

- [ ] Strong unique password set
- [ ] Two-factor authentication enabled with authenticator app
- [ ] Recovery codes saved offline in a secure location
- [ ] SSH key added with ed25519 algorithm
- [ ] SSH key passphrase set
- [ ] Access tokens created with minimum necessary scopes
- [ ] Old or unused access tokens deleted
- [ ] Email address verified
- [ ] OAuth2 providers linked only if needed

---

## Try It Yourself

**Exercise 1 - Create and configure your account**

1. Register on a Gitea instance ([gitea.com](https://gitea.com) for the hosted version or your local instance)
2. Set your display name and a profile avatar
3. Enable 2FA using an authenticator app
4. Save the recovery codes

**Exercise 2 - SSH key setup**

```bash
# Generate key
ssh-keygen -t ed25519 -C "your-email@example.com"

# Copy public key
# Windows: Get-Content ~/.ssh/id_ed25519.pub | clip
# Mac: pbcopy < ~/.ssh/id_ed25519.pub
# Linux: cat ~/.ssh/id_ed25519.pub
```

Add to Gitea under Settings > SSH / GPG Keys, then test:
```bash
ssh -T git@gitea.example.com
```

**Exercise 3 - Create an access token**

1. Go to Settings > Applications
2. Create a token named "Practice token" with repository:read scope
3. Copy the token
4. Test it with the API:

```bash
curl -H "Authorization: token YOUR_TOKEN" \
  https://gitea.example.com/api/v1/user
```

You should see your user details returned as JSON.

**Exercise 4 - Test HTTPS clone with a token**

```bash
# Clone using your token as the password
git clone https://gitea.example.com/your-username/your-repo.git
# Username: your-username
# Password: your-access-token
```

---

## Common Mistakes

**Using your account password for HTTPS Git operations**

Gitea's API and HTTPS Git operations should use access tokens, not your account password. Your account password is for the web interface. Create an access token for HTTPS Git and API use.

**Not saving recovery codes**

Recovery codes are shown once when 2FA is set up. If you lose your authenticator app without saved recovery codes, you are locked out. Save them to a password manager or print them and store securely.

**Creating one access token for all purposes**

One "master" token with all scopes creates maximum risk if it is ever exposed. Create separate tokens for separate purposes with minimum required scopes. A token for reading CI/CD status does not need write access to repositories.

**Not testing SSH after adding the key**

After adding an SSH key to Gitea, always run `ssh -T git@gitea.example.com` to verify the connection works. Common issues: wrong permissions on the private key file, Gitea instance using a non-standard SSH port, public key pasted incorrectly.

**Using RSA keys instead of ed25519**

Ed25519 is the modern, recommended algorithm. RSA keys work but are larger and slower. Generate ed25519 keys for new setups.

---

## Summary

Gitea accounts are local to each instance - there is no central identity. Register using the instance's sign-in page, or sign in with an external OAuth2 provider if the administrator has configured one.

Enable two-factor authentication immediately using an authenticator app. Save recovery codes offline - without them and your authenticator, you are locked out. SSH keys provide passwordless Git authentication; add your ed25519 public key under Settings > SSH / GPG Keys.

Access tokens authenticate HTTPS Git operations and API requests. Create them with minimum necessary scopes under Settings > Applications. Tokens are shown only once - copy immediately. Use your account password only for the web interface, never for API or Git operations.

---

## Sources

- [Gitea: Account setup documentation](https://docs.gitea.com/usage/profile)
- [Gitea: Two-factor authentication](https://docs.gitea.com/usage/two-factor-authentication)
- [Gitea: SSH keys](https://docs.gitea.com/usage/ssh-keys)
- [Gitea: Access tokens](https://docs.gitea.com/usage/access-token)
- [Gitea: OAuth2 sign-in](https://docs.gitea.com/administration/oauth2-provider)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
