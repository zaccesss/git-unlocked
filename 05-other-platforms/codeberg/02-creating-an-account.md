# Creating a Codeberg Account

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

Signing up for Codeberg is straightforward: a username, an email and a password. Because Codeberg runs Forgejo, the account setup - SSH keys, access tokens, 2FA - is identical to Forgejo and very similar to GitHub. This file covers the complete setup, Codeberg-specific considerations and the federated identity that distinguishes Codeberg accounts from accounts on commercial platforms.

---

## Table of Contents

1. [Registration](#registration)
2. [Profile and settings](#profile-and-settings)
3. [Two-factor authentication](#two-factor-authentication)
4. [SSH keys](#ssh-keys)
5. [Access tokens](#access-tokens)
6. [Federated identity](#federated-identity)
7. [Codeberg-specific account considerations](#codeberg-specific-account-considerations)
8. [Try It Yourself](#try-it-yourself)
9. [Common Mistakes](#common-mistakes)
10. [Summary](#summary)
11. [Sources](#sources)

---

## Registration

### Creating your account

1. Go to [codeberg.org](https://codeberg.org)
2. Click **Register** in the top right
3. Fill in:
   - **Username**: appears in all your repository URLs (`codeberg.org/username/repo`). Choose thoughtfully - changing it later is possible but breaks all existing URLs and remotes.
   - **Email**: used for notifications and password recovery. Codeberg does not share or sell this.
   - **Password**: at minimum 8 characters. Use a password manager.
4. Complete the CAPTCHA (Codeberg uses hCaptcha, which is privacy-respecting compared to Google reCAPTCHA)
5. Click **Register Account**
6. Check your inbox for a confirmation email and click the verification link

Your account is now active.

### Username guidelines

Codeberg's username rules:
- Letters, numbers, hyphens and underscores only
- Cannot start with a hyphen
- Not case-sensitive in URLs but preserved as entered for display

Since Codeberg supports ActivityPub federation, your username becomes part of your federated identity: `@username@codeberg.org`. Choose a username you are happy to use as a public identity in the Fediverse.

---

## Profile and settings

### Basic profile

Avatar > **Settings**:

- **Full name**: shown on your profile and in commit history when making web-based edits
- **Website**: linked from your profile
- **Description**: a short bio
- **Location**: shown publicly on your profile

### Changing username

Avatar > **Settings** > **Account** > enter new username and current password.

> [!WARNING]
> Changing your username breaks all existing repository URLs and any Git remote that points to your repositories. All `git remote` references in local repositories will need updating with `git remote set-url`. Announce the change if others use your repositories.

### Theme and language

Avatar > **Settings** > **Appearance**:
- **Theme**: Codeberg Auto (follows system), Light, Dark and others
- **Language**: Codeberg's interface is available in many languages

---

## Two-factor authentication

Enable 2FA immediately after creating your account. It is the single most effective security improvement you can make.

### Setting up TOTP

1. Avatar > **Settings** > **Security**
2. Under **Two-Factor Authentication**, click **Enroll**
3. Scan the QR code with an authenticator app:
   - **Aegis** (Android, open source, recommended for privacy)
   - **Authy** (iOS/Android, cloud backup)
   - **Google Authenticator** (iOS/Android)
   - **1Password** or **Bitwarden** (if you use a password manager with TOTP)
4. Enter the 6-digit code from the app to confirm
5. **Save your recovery codes** - shown once, store them offline

### Recovery codes

Recovery codes are one-time-use backup codes for when your authenticator app is unavailable (lost phone, new device). Store them in:
- A password manager
- An encrypted note
- Printed and kept in a secure physical location

Without recovery codes and your authenticator, you are locked out of your account permanently - Codeberg support may not be able to help recover accounts without 2FA proof.

---

## SSH keys

SSH keys enable passwordless Git operations. Once set up, cloning, pushing and pulling work without credential prompts.

### Generating a key pair

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

Accept the default path (`~/.ssh/id_ed25519`). Add a passphrase.

### Adding your public key to Codeberg

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
# copy the output
```

**Add to Codeberg:**
1. Avatar > **Settings** > **SSH / GPG Keys**
2. Click **Add Key**
3. Enter a label (e.g. "Laptop", "Desktop")
4. Paste the public key
5. Click **Add Key**

### Testing the connection

```bash
ssh -T git@codeberg.org
```

Expected response:
```
Hi yourusername! You've successfully authenticated, but Codeberg does not provide shell access.
```

### Cloning via SSH

From any repository's Clone dropdown, select SSH:

```bash
git clone git@codeberg.org:username/repository.git
```

---

## Access tokens

Access tokens authenticate the Codeberg REST API and HTTPS Git operations.

### Creating a token

1. Avatar > **Settings** > **Applications**
2. Under **Manage Access Tokens**, click **Generate Token**
3. Give it a name: `CI`, `Local scripts`, `VS Code extension`
4. Select scopes (use minimum necessary):
   - `repository` read/write for Git operations
   - `issue` read/write for issue management
   - `package` read/write for package registry
   - `admin` for instance administration (rarely needed)
5. Click **Generate Token**
6. **Copy immediately** - the token is shown only once

### Using a token for HTTPS Git

```bash
git clone https://codeberg.org/username/repository.git
# Username: your Codeberg username
# Password: your access token (not your account password)
```

### Using a token for API calls

```bash
curl -H "Authorization: token YOUR_TOKEN" \
  https://codeberg.org/api/v1/user
```

---

## Federated identity

Codeberg's most distinctive account feature is its ActivityPub federated identity. Because Codeberg runs Forgejo with federation enabled, every Codeberg account has a federated identity visible across the Fediverse.

### Your federated identity

Your identity is: `@yourusername@codeberg.org`

This appears on your Codeberg profile page. It means:
- Mastodon users can search for and follow your Codeberg activity
- Other Forgejo/Codeberg instances can interact with your repositories
- Your identity is not tied to any single corporation's platform

### What the federation currently does

In 2026, federation from Codeberg allows:
- Other Forgejo instances to follow your public repositories and receive notifications of new releases and commits
- Discovery of your Codeberg identity from Mastodon and other ActivityPub platforms
- Basic cross-instance notification delivery

Cross-instance forking and PR submission are in development (as described in the `forgejo/` folder).

### Privacy and federation

Federation means your public activity may be relayed to other instances. Private repositories are not federated. Your username and public actions (commits to public repos, public issues) are part of your federated identity.

If you prefer not to have your Codeberg activity discoverable via federation, use only private repositories, though your account itself remains discoverable by username.

---

## Codeberg-specific account considerations

### Email privacy

Codeberg's privacy policy commits to not sharing your email with third parties. Your email is used for:
- Account verification
- Notifications you configure
- Password recovery

It is not used for advertising or sold to partners.

### Account limits

Codeberg's current limits for free accounts:
- **Repositories**: no stated hard limit, subject to fair use
- **Storage per repository**: no stated hard limit, subject to fair use
- **Private repositories**: available for personal accounts; organisation private repos require financial support of Codeberg
- **CI/CD minutes**: Woodpecker CI on Codeberg has resource limits encouraging self-hosted runners for heavy users

Check [codeberg.org/Codeberg/Community](https://codeberg.org/Codeberg/Community) for current policies, as these evolve as the platform grows.

### Supporting Codeberg

If you use Codeberg, consider supporting it financially. The platform runs on donations. Instructions at [codeberg.org/Codeberg/Community/wiki/Donate](https://codeberg.org/Codeberg/Community/wiki/Donate). Payment options include PayPal, SEPA bank transfer and GitHub Sponsors (for the Codeberg e.V. account).

---

## Try It Yourself

**Exercise 1 - Complete account setup**

1. Register at [codeberg.org](https://codeberg.org)
2. Verify your email
3. Set a profile picture and bio
4. Enable 2FA with an authenticator app - save the recovery codes

**Exercise 2 - Set up SSH**

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"

# Copy public key (use your OS method from above)
# Add to Codeberg: Settings > SSH / GPG Keys > Add Key

# Test:
ssh -T git@codeberg.org
```

**Exercise 3 - Create and test an access token**

1. Settings > Applications > Generate Token
2. Name: "Practice token", scope: repository:read
3. Test the API:

```bash
curl -H "Authorization: token YOUR_TOKEN" \
  https://codeberg.org/api/v1/user | python3 -m json.tool
```

**Exercise 4 - Find your federated identity**

1. Go to your Codeberg profile
2. Note the ActivityPub link (`@username@codeberg.org`)
3. Try searching for `@yourusername@codeberg.org` on a Mastodon instance (e.g. mastodon.social)
4. You should be discoverable

---

## Common Mistakes

**Using your account password for HTTPS Git operations**

Use an access token as the password for HTTPS Git clones and API calls. Your account password is only for web sign-in.

**Not saving 2FA recovery codes**

Recovery codes are shown once when you set up 2FA. Without them and your authenticator app, account recovery is not guaranteed. Save them securely before closing the setup window.

**Choosing a username without considering the federated identity**

Your username becomes `@username@codeberg.org` in the Fediverse. Choose something you are comfortable with as a public identity, not just a throwaway handle.

**Committing with a private email in public repositories**

If you configure Git with your real email (`git config --global user.email`), that email appears in all commit metadata in public repositories. Codeberg offers a no-reply address option if you want privacy. Enable it under Settings > Privacy > keep email address private, which provides a `username@users.noreply.codeberg.org` address for web-based commits.

---

## Summary

Codeberg accounts are free with no stated hard limits. Register at [codeberg.org](https://codeberg.org), verify your email and set up 2FA immediately using an authenticator app. Save recovery codes offline.

SSH keys enable passwordless Git operations - add your ed25519 public key under Settings > SSH / GPG Keys. Test the connection with `ssh -T git@codeberg.org`.

Access tokens authenticate API calls and HTTPS Git operations. Create them under Settings > Applications with minimum required scopes.

Every Codeberg account has a federated ActivityPub identity (`@username@codeberg.org`), discoverable across Mastodon and other Forgejo instances. Your public repository activity may be relayed to followers on other instances.

---

## Sources

- [Codeberg: Account documentation](https://codeberg.org/Codeberg/Community/wiki)
- [Codeberg: Privacy policy](https://codeberg.org/Codeberg/org/src/branch/main/PrivacyPolicy.md)
- [Codeberg: Donate](https://codeberg.org/Codeberg/Community/wiki/Donate)
- [Forgejo: SSH keys](https://forgejo.org/docs/latest/user/ssh/)
- [Forgejo: Access tokens](https://forgejo.org/docs/latest/user/access-token/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
