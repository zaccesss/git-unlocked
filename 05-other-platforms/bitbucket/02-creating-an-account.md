# Creating a Bitbucket Account

**Difficulty:** 🟢 Beginner | **Time:** 30 minutes

Your Bitbucket account is an Atlassian account. That distinction matters from the start: when you sign up for Bitbucket, you are not just creating a Bitbucket account - you are creating an identity that works across the entire Atlassian product suite. The same login gives you access to Jira, Confluence, Trello, Statuspage and every other Atlassian cloud product. This shared identity is one of Bitbucket's core advantages for teams that use multiple Atlassian tools.

This file walks you through the complete account setup process: signing up, securing your account with two-factor authentication, adding SSH keys, creating API tokens (the replacement for the deprecated app passwords), and understanding the free tier limits so you know what you are working with before you start building.

---

## Table of Contents

1. [Before you sign up](#before-you-sign-up)
2. [Creating your Atlassian account](#creating-your-atlassian-account)
3. [Choosing a workspace slug](#choosing-a-workspace-slug)
4. [Verifying your email address](#verifying-your-email-address)
5. [Setting up two-factor authentication](#setting-up-two-factor-authentication)
6. [Adding SSH keys](#adding-ssh-keys)
7. [API tokens: the replacement for app passwords](#api-tokens-the-replacement-for-app-passwords)
8. [Understanding the free tier limits](#understanding-the-free-tier-limits)
9. [Profile and account settings](#profile-and-account-settings)
10. [Atlassian Guard and SSO](#atlassian-guard-and-sso)
11. [Account security checklist](#account-security-checklist)
12. [Try It Yourself](#try-it-yourself)
13. [Common Mistakes](#common-mistakes)
14. [Summary](#summary)
15. [Sources](#sources)

---

## Before you sign up

**What you need:**

- An email address you will keep long term - not a university address that expires, not a work address you lose when you change jobs
- A chosen workspace name (read the workspace slug section before deciding)
- A phone or authenticator app for two-factor authentication

**Which email to use:**

Use a personal email address. Your Atlassian account will outlast any employer or institution. If your primary address is a work or university email, add it as a secondary address later for any verification purposes, but keep a personal email as your primary. Losing access to your primary email address makes account recovery complicated.

**Atlassian account vs Bitbucket account:**

There is no such thing as a "Bitbucket account" that is separate from an Atlassian account. When you sign in to Bitbucket, you sign in through Atlassian's identity platform (id.atlassian.com). Your profile, password, two-factor authentication settings and security preferences all live at the Atlassian level, not the Bitbucket level. This means a change to your Atlassian password affects all Atlassian products you use simultaneously.

---

## Creating your Atlassian account

### Signing up via Bitbucket

1. Go to **bitbucket.org**
2. Click **Get it free** in the top right
3. You will be redirected to id.atlassian.com
4. Enter your email address and click **Continue**
5. Atlassian checks whether an account already exists for that email
   - If yes, it prompts you to sign in
   - If no, it prompts you to create a new account
6. Enter your full name and a strong password
7. Complete any CAPTCHA or bot-detection step
8. Click **Sign up**

### Signing up with Google or Microsoft

You can also create an Atlassian account using an existing Google or Microsoft account. Click **Continue with Google** or **Continue with Microsoft** on the sign-up page. This links your Atlassian identity to your Google or Microsoft account - you sign in using that provider rather than an Atlassian password. This is convenient but creates a dependency: if you ever lose access to your Google or Microsoft account, you also lose access to Atlassian.

> [!TIP]
> If you sign up via Google or Microsoft and later want to add a direct Atlassian password, you can do this in your Atlassian account security settings. Having a password as a fallback alongside social login adds resilience.

### What happens after sign-up

After creating your account you land on the Bitbucket workspace creation screen. Atlassian walks you through:

1. Creating your first workspace (give it a name and a slug)
2. Optionally connecting to Jira
3. Optionally inviting teammates
4. A brief onboarding tour

You can skip the onboarding tour and come back to these settings at any time. The important step is the workspace slug - read the next section before committing to one.

---

## Choosing a workspace slug

A **workspace** is the top-level container for your repositories in Bitbucket. Every repository lives inside a workspace. The workspace has a **slug** - a short identifier that appears in all your repository URLs.

If your workspace slug is `myteam`, all your repositories will live at:

```
https://bitbucket.org/myteam/repository-name
```

**The slug is not easy to change.** Unlike a display name, changing a workspace slug breaks all existing repository URLs, any Git remotes pointing to those URLs, CI/CD configurations, webhooks and third-party integrations. Change it after you have repositories and you will need to update every reference to every URL.

**Rules for workspace slugs:**

- 2 to 50 characters
- Lowercase letters, numbers and hyphens only
- Cannot start or end with a hyphen
- Must be unique across all of Bitbucket

**Personal workspaces:**

When you first sign up, Bitbucket creates a personal workspace with a slug derived from your name or email. You can rename the slug during onboarding. For individual developers, the slug is often your name or a handle you use consistently: `isaac-adjei`, `zacess`, `johndoe`.

**Team workspaces:**

If you are setting up Bitbucket for a company or team, create a separate workspace for the organisation. Do not use your personal workspace for company repositories. Give the team workspace a clear, professional slug: `acme-corp`, `myteam-dev`. You can create multiple workspaces under a single Atlassian account.

> [!IMPORTANT]
> Choose your workspace slug carefully. For a personal workspace, use something you are comfortable with appearing in public URLs. For a team workspace, use the organisation name. Once you have repositories inside, changing the slug requires updating every Git remote in every local clone of every repository.

---

## Verifying your email address

Bitbucket sends a verification email immediately after sign-up. Click the link in that email to verify your address. Until you verify, some features are restricted.

If the verification email does not arrive:

1. Check your spam or junk folder
2. In Bitbucket, go to **Personal settings** > **Email addresses**
3. Click **Resend verification email** next to your address
4. Make sure your email provider is not blocking emails from atlassian.com

You can add multiple email addresses to your Atlassian account. One address is marked as primary - this is the address Atlassian uses for account recovery, security notifications and billing. All added addresses can be used for commit notifications and can be associated with commits.

> [!NOTE]
> When you make commits from the command line, Git embeds the email address from your `git config --global user.email` setting into the commit. If that address matches one of your verified Atlassian email addresses, Bitbucket links the commit to your account. If it does not match, the commit appears as from an unrecognised author.

---

## Setting up two-factor authentication

Two-factor authentication (2FA) adds a second verification step when you sign in. Even if someone gets your password, they cannot sign in without also having your second factor. On Bitbucket, 2FA is optional for personal accounts but can be enforced at the workspace level by administrators.

**How to enable 2FA:**

1. Go to **[id.atlassian.com](https://id.atlassian.com)** (Atlassian's account management site)
2. Click **Security** in the left navigation
3. Under **Two-step verification**, click **Set up two-step verification**
4. Choose your second factor method

### 2FA methods available

**Authenticator app (recommended)**

An authenticator app generates time-based one-time passwords (TOTP) that refresh every 30 seconds. Popular apps:

- 🪟🍎🐧 **Authy** - works on Windows, Mac, iOS and Android; backs up codes to the cloud
- 🍎🐧 **1Password** - password manager with built-in TOTP
- 🪟🍎 **Microsoft Authenticator** - works on iOS and Android
- 🍎🐧 **Google Authenticator** - iOS and Android, no cloud backup

Setup process:

1. Choose **Authenticator app** as your method
2. Scan the QR code with your authenticator app
3. Enter the 6-digit code shown in the app to confirm it is working
4. Save your recovery codes (see below)

**SMS verification**

Atlassian can send a one-time code to your phone number via SMS. This is less secure than an authenticator app because SMS can be intercepted via SIM-swapping attacks. Use it only if you cannot use an authenticator app.

**Security keys (WebAuthn/FIDO2)**

Hardware security keys (like YubiKey) and device-based passkeys provide the strongest 2FA. They use public-key cryptography and are phishing-resistant - a fake website cannot capture a hardware key response because the response is bound to the exact domain. Atlassian supports WebAuthn security keys on supported browsers.

### Recovery codes

When you enable 2FA, Atlassian generates a set of single-use recovery codes. These are for emergencies: if you lose your phone or authenticator app, a recovery code lets you sign in and reset your 2FA settings.

**Save your recovery codes immediately.** Store them somewhere secure and offline - a password manager, a printed document in a safe place, or an encrypted file. Do not store them in the same cloud service you use for everything else.

> [!WARNING]
> If you lose your 2FA device and do not have recovery codes, recovering your account requires contacting Atlassian support and proving your identity. This process can take days. The recovery codes exist specifically to prevent this situation - save them.

---

## Adding SSH keys

SSH keys let you authenticate to Bitbucket without entering a password for every Git operation. Once set up, `git push` and `git pull` work silently using your key. This is the recommended authentication method for command-line Git work.

### How SSH authentication works

When you add an SSH public key to Bitbucket, you are telling Bitbucket: "Trust connections from whoever has the matching private key." Your private key lives on your machine and never leaves it. Your public key is what you paste into Bitbucket. When you connect, Git and Bitbucket perform a cryptographic handshake to verify you hold the private key without transmitting it.

### Generating an SSH key pair

If you do not already have an SSH key, generate one. Open a terminal:

🪟 **Windows** (PowerShell or Git Bash):

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

🍎 **Mac** (Terminal):

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

🐧 **Linux** (Bash):

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

The command is identical across all three operating systems. When prompted:

- **File location**: press Enter to accept the default (`~/.ssh/id_ed25519`)
- **Passphrase**: enter a passphrase to protect the key (optional but recommended). The passphrase encrypts the private key file - if someone steals the file, they still cannot use it without the passphrase.

This creates two files:

- `~/.ssh/id_ed25519` - your private key (never share this)
- `~/.ssh/id_ed25519.pub` - your public key (this is what you add to Bitbucket)

> [!NOTE]
> `ed25519` is a modern elliptic-curve algorithm that is faster and more secure than the older `rsa` algorithm. If you are on a very old system that does not support ed25519, use `ssh-keygen -t rsa -b 4096 -C "your-email@example.com"` instead.

### Adding the public key to Bitbucket

**Copy your public key:**

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
# then copy the output manually, or:
xclip -selection clipboard < ~/.ssh/id_ed25519.pub
```

**Add to Bitbucket:**

1. Go to **bitbucket.org**
2. Click your avatar (bottom left) > **Personal settings**
3. Click **SSH keys** in the left menu
4. Click **Add key**
5. Give the key a label (e.g. "Work laptop", "Home desktop")
6. Paste your public key into the Key field
7. Click **Add key**

### Testing the SSH connection

```bash
ssh -T git@bitbucket.org
```

A successful response looks like:

```
logged in as yourusername.

You can use git or hg to connect to Bitbucket. Shell access is disabled.
```

If you see a host authenticity warning on first connection, type `yes` to add Bitbucket to your known hosts. This is normal.

### Cloning with SSH

Once your key is added, use SSH clone URLs instead of HTTPS:

```bash
# SSH (no password required after setup)
git clone git@bitbucket.org:workspace/repository.git

# HTTPS (requires API token for authentication)
git clone https://bitbucket.org/workspace/repository.git
```

---

## API tokens: the replacement for app passwords

**API tokens are the way to authenticate to Bitbucket programmatically from June 2026 onwards.** App passwords are deprecated - new ones cannot be created (blocked September 2025) and existing ones stop working on 9 June 2026. If you are reading older documentation or tutorials that mention app passwords, replace "app password" with "API token" in your mental model.

### What API tokens are for

API tokens are used when:

- Cloning or pushing over HTTPS from the command line
- Authenticating in CI/CD pipelines that use HTTPS
- Accessing the Bitbucket REST API from scripts or tools
- Authenticating third-party integrations that use HTTPS rather than SSH

If you use SSH for Git operations, you do not need an API token for those operations. API tokens and SSH keys serve different purposes: SSH keys authenticate Git protocol connections, API tokens authenticate HTTPS connections and REST API calls.

### Creating an API token

1. Go to **bitbucket.org** and sign in
2. Click your avatar > **Personal settings**
3. Click **API tokens** in the left menu
4. Click **Create token**
5. Give the token a descriptive label: "CI pipeline", "Local dev", "Deploy script"
6. Set an expiry date (optional but recommended - choose based on how long you need the token)
7. Select the permissions the token needs (see below)
8. Click **Create**
9. **Copy the token immediately** - it is only shown once. If you lose it, you must delete it and create a new one.

### Token permissions

API tokens have granular permission scopes:

| Scope                          | What it allows                      |
| ------------------------------ | ----------------------------------- |
| **Repositories: Read**         | Clone repositories, read code       |
| **Repositories: Write**        | Push commits, create branches       |
| **Repositories: Admin**        | Manage repository settings          |
| **Pull requests: Read**        | View pull requests                  |
| **Pull requests: Write**       | Create and comment on pull requests |
| **Issues: Read**               | View issues                         |
| **Issues: Write**              | Create and update issues            |
| **Pipelines: Read**            | View pipeline status                |
| **Pipelines: Write**           | Trigger pipelines                   |
| **Account: Read**              | Read account information            |
| **Workspace membership: Read** | View workspace members              |

Always grant the minimum permissions needed. A token used for CI/CD that only needs to clone and push does not need Account or Issue permissions.

### Using an API token for HTTPS Git operations

When Git asks for your password on an HTTPS operation, use the API token as the password:

```bash
git clone https://bitbucket.org/workspace/repo.git
# Username: your-atlassian-email@example.com
# Password: your-api-token (not your Atlassian password)
```

To avoid entering credentials every time, store them in Git's credential manager:

🪟 **Windows** - Git Credential Manager is included with Git for Windows and stores tokens in Windows Credential Manager automatically.

🍎 **Mac** - Git Credential Manager stores tokens in the macOS Keychain automatically.

🐧 **Linux**:

```bash
git config --global credential.helper store
# or use the memory cache for a session:
git config --global credential.helper 'cache --timeout=3600'
```

> [!TIP]
> For CI/CD pipelines, store your API token as a secure environment variable or secret in your pipeline configuration. Never hardcode tokens in repository files. Bitbucket Pipelines has a built-in secure variable storage - use it.

### Managing tokens

You can view all your active tokens in **Personal settings** > **API tokens**. Each token shows its label, creation date, expiry date and last-used date. You can revoke any token immediately by clicking the bin icon next to it.

Revoke tokens when:

- A project or integration they were created for is finished
- You suspect a token has been exposed or compromised
- An employee who used the token leaves the organisation

---

## Understanding the free tier limits

Bitbucket's free plan is genuinely useful for small teams but has specific limits worth understanding before you build workflows around it.

### User limit

**Free: up to 5 users per workspace.** This is a hard limit. If your workspace has 5 users and you invite a sixth, Bitbucket will prompt you to upgrade to Standard or Premium. The 5-user count includes workspace members - not viewers or external collaborators in the same sense as GitHub.

GitHub's free plan has unlimited collaborators on public repositories and unlimited collaborators on private repositories for organisations (with some feature restrictions). Bitbucket's 5-user limit is more restrictive but the Jira integration and other features may justify the trade-off for Atlassian-native teams.

### Pipeline minutes

**Free: 50 Bitbucket Pipelines minutes per month.** This resets on the first of each month. 50 minutes is enough for light experimentation but not for active development workflows. A typical test suite that takes 5 minutes to run would exhaust the free allocation in 10 runs.

If you are doing serious CI/CD work on a free plan, consider:

- Using self-hosted runners (free runners, 100 per workspace on V5)
- Optimising your pipeline to run faster
- Upgrading to Standard ($3.65/user/month) for 2,500 minutes

### Storage

**Free: 1 GB total workspace storage.** This includes repository data and LFS objects. For most development work with text code, 1 GB is enough. For repositories with large binary files (game assets, datasets, design files), it will fill up quickly. Standard provides 5 GB LFS storage, Premium provides 10 GB.

### Repository limit

The free plan supports **unlimited repositories** in a workspace. There is no cap on the number of repositories you can create. Private repositories are included - you do not need to pay to keep code private, unlike GitHub's historical model (though GitHub now also offers free private repos).

### Features not on the free plan

| Feature                                 | Minimum plan                    |
| --------------------------------------- | ------------------------------- |
| AI pull request descriptions (Rovo Dev) | Standard                        |
| Bitbucket Packages (container registry) | Standard                        |
| IP allowlisting                         | Premium                         |
| Enforced merge checks                   | Premium                         |
| Required builds before merge            | Premium                         |
| 99.9% uptime SLA                        | Premium                         |
| Atlassian Guard (SSO)                   | Guard add-on (separate pricing) |

---

## Profile and account settings

### Atlassian account profile

Your Atlassian profile is managed at **[id.atlassian.com](https://id.atlassian.com)**, not within Bitbucket directly. From there you can:

- Change your display name
- Update your avatar
- Change your email address
- Manage linked accounts (Google, Microsoft)
- View your active sessions
- Download your data

### Bitbucket-specific settings

Within Bitbucket itself (under your avatar > **Personal settings**), you can configure:

- **Email notifications** - which events trigger email alerts (PR reviews, comments, pipeline failures)
- **Integrations** - connect third-party tools to your Bitbucket account
- **SSH keys** - manage your SSH public keys
- **API tokens** - create and manage authentication tokens
- **Privacy settings** - control what is visible on your public profile

### Display name vs username vs email

Atlassian has three identifiers to be aware of:

- **Display name** - shown in the UI next to your avatar. Can be changed freely.
- **Workspace slug** - appears in repository URLs. Hard to change safely once repositories exist.
- **Email address** - used to log in and matches commits to your account. Can be updated but the old address should remain associated for commit history.

---

## Atlassian Guard and SSO

If you are setting up Bitbucket for an organisation rather than personal use, you will eventually encounter Atlassian Guard.

**Atlassian Guard** (formerly Atlassian Access) is a separate paid product that adds enterprise identity management to Atlassian Cloud products including Bitbucket. Guard Standard costs **$4.20 per user per month** on top of Bitbucket pricing.

Guard Standard provides:

- **Single sign-on (SSO)** via SAML 2.0 - your team signs in through your identity provider (Okta, Azure AD, Google Workspace) rather than managing Atlassian passwords separately
- **SCIM provisioning** - automatically create and deactivate Atlassian accounts when employees join or leave, synced from your identity provider
- **Authentication policies** - enforce 2FA, set password strength requirements, control which sign-in methods are allowed
- **API token controls** - restrict or audit API token usage across the organisation
- **Session duration controls** - force re-authentication after a set period

Guard Premium (~$8.18/user/month) adds data classification, anomaly detection and SIEM integrations.

> [!NOTE]
> SSO for Bitbucket requires a Guard subscription. This is often a surprise for organisations comparing Bitbucket to GitHub or GitLab, where enterprise SSO is bundled with higher-tier plans. Budget for Guard separately when calculating the total cost of Bitbucket for an enterprise team.

For personal accounts and small teams not using enterprise identity providers, Guard is not necessary. The standard Atlassian account system with 2FA is sufficient.

---

## Account security checklist

Work through this checklist after setting up your account:

- [ ] Email address verified
- [ ] Two-factor authentication enabled with an authenticator app
- [ ] Recovery codes saved in a secure location offline
- [ ] SSH key added for command-line Git operations
- [ ] API token created (if needed for HTTPS or API access) with minimum necessary permissions
- [ ] Token expiry date set
- [ ] Workspace slug chosen thoughtfully - not based on a temporary role or employer
- [ ] No app passwords in use (migrate any existing ones to API tokens before June 2026)
- [ ] Notification preferences configured to reduce noise
- [ ] Third-party apps reviewed and unnecessary ones removed

---

## Try It Yourself

**Exercise 1 - Create and secure your account**

1. Go to bitbucket.org and create an Atlassian account
2. Verify your email address
3. Enable two-factor authentication using an authenticator app
4. Save your recovery codes somewhere safe

**Exercise 2 - Generate and add an SSH key**

🪟 Windows (Git Bash or PowerShell):

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
Get-Content ~/.ssh/id_ed25519.pub | clip
```

🍎 Mac:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
pbcopy < ~/.ssh/id_ed25519.pub
```

🐧 Linux:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
cat ~/.ssh/id_ed25519.pub
```

Add the copied key to Bitbucket under **Personal settings** > **SSH keys**.

Then test it:

```bash
ssh -T git@bitbucket.org
```

**Exercise 3 - Create an API token**

1. Go to **Personal settings** > **API tokens**
2. Create a token called "Learning" with Repositories: Read permission
3. Copy the token and store it somewhere safe
4. Try using it to clone a public repository via HTTPS

**Exercise 4 - Explore workspace settings**

1. Click your workspace name in the left sidebar
2. Go to **Workspace settings**
3. Explore the Members, Groups and Permissions sections
4. Note the difference between workspace-level settings and repository-level settings

---

## Common Mistakes

**Using an app password after September 2025**

New app passwords cannot be created from September 2025. If you are following an older tutorial that tells you to create an app password, it means API token. The functionality is the same - only the name and the security model have changed.

**Storing the API token insecurely**

API tokens are shown only once when created. If you close the window without copying it, you cannot retrieve it - you must delete it and create a new one. Never store tokens in code, commit messages or plaintext files in your repository. Use environment variables, a password manager or Bitbucket Pipelines' secure variables.

**Not setting an expiry on API tokens**

Tokens with no expiry last until you manually revoke them. If a token is forgotten, it becomes a permanent credential that could be misused. Set expiry dates appropriate to the use case: a token for a short project can expire in 30 days; a long-running CI/CD integration might use 90 days or 1 year, with a calendar reminder to rotate it.

**Confusing the Atlassian account with a Bitbucket account**

Settings like your password, 2FA and linked accounts live at id.atlassian.com, not inside Bitbucket. When you search for "change password" inside Bitbucket, it redirects you to Atlassian's account management site. This is by design but surprises new users.

**Using a work or university email as the primary**

Your Atlassian account and its associated workspace data are tied to your primary email. If you lose access to that email (employer blocks ex-employee accounts, university email expires after graduation), recovery is possible but slow and requires Atlassian support. Use a personal email as primary.

**Not setting up SSH and relying only on HTTPS**

HTTPS with an API token works, but SSH is more convenient for day-to-day development. Without SSH, every `git push` and `git pull` requires your credentials to be stored or re-entered. Set up SSH once and Git operations become transparent.

**Forgetting to save recovery codes**

This is the most common 2FA mistake across all platforms. Recovery codes exist for emergencies. If you lose your 2FA device without having saved the codes, you are locked out until you can prove your identity to Atlassian support. Save them.

---

## Summary

Bitbucket accounts are Atlassian accounts - one identity across all Atlassian products. Sign up at [bitbucket.org](https://bitbucket.org), verify your email, and enable two-factor authentication immediately using an authenticator app. Save your recovery codes offline.

SSH keys are the preferred method for command-line Git operations. Generate an ed25519 key pair, add the public key to Bitbucket under Personal settings and test the connection with `ssh -T git@bitbucket.org`.

API tokens replace the deprecated app passwords entirely from June 2026. Create tokens with the minimum permissions needed, set expiry dates and store them securely using environment variables or a password manager - never in code.

The free plan supports up to 5 workspace users, 50 Pipelines minutes per month and 1 GB total storage with unlimited private repositories. Enterprise teams needing SSO require Atlassian Guard as a separate paid product at $4.20 per user per month.

---

## Sources

- [Atlassian: Bitbucket Cloud app password deprecation](https://www.atlassian.com/blog/bitbucket/bitbucket-cloud-transitions-to-api-tokens-enhancing-security-with-app-password-deprecation)
- [Atlassian: Bitbucket pricing](https://www.atlassian.com/software/bitbucket/pricing)
- [Atlassian: SSH keys documentation](https://support.atlassian.com/bitbucket-cloud/docs/set-up-personal-ssh-keys/)
- [Atlassian: API tokens](https://support.atlassian.com/bitbucket-cloud/docs/create-a-repository-access-token/)
- [Atlassian: Two-step verification](https://support.atlassian.com/manage-atlassian-account/docs/enable-two-step-verification/)
- [Atlassian: What is Atlassian Guard](https://www.atlassian.com/software/access)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
