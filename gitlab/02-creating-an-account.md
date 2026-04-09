# Creating a GitLab Account

**Difficulty:** 🟢 Beginner | **Time:** 30 minutes

Before you can push code, open merge requests, run pipelines or collaborate with a team on GitLab, you need an account. This file walks you through creating and securing your account properly from the start: signing up on GitLab.com, setting up two-factor authentication with multiple methods including the new passkeys, generating and adding SSH keys on Windows, Mac and Linux, creating personal access tokens for API and tooling access, and understanding exactly what the free tier gives you and where its limits are.

Getting this right at the start saves significant headaches later. An account without 2FA is a security risk. An account without SSH keys means typing your password for every push and pull. An account with no understanding of free tier limits leads to surprises mid-project.

---

## Table of Contents

- [Before you start](#before-you-start)
- [Signing up on GitLab.com](#signing-up-on-gitlabcom)
- [Username rules and choosing well](#username-rules-and-choosing-well)
- [Email verification and identity checks](#email-verification-and-identity-checks)
- [Setting up your profile](#setting-up-your-profile)
- [Two-factor authentication](#two-factor-authentication)
- [Passkeys - passwordless sign-in](#passkeys---passwordless-sign-in)
- [SSH keys](#ssh-keys)
- [Personal access tokens](#personal-access-tokens)
- [Understanding the free tier](#understanding-the-free-tier)
- [The credit card requirement for shared runners](#the-credit-card-requirement-for-shared-runners)
- [Signing in on self-managed GitLab](#signing-in-on-self-managed-gitlab)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Before You Start

This file covers **GitLab.com** - the cloud-hosted service at `https://gitlab.com`. If your organisation runs its own GitLab installation (self-managed), the signup process is different and controlled by your administrator. The self-managed signin section near the end of this file covers the relevant differences.

You will need:
- An email address you control and can receive mail at
- A secure password (at least 8 characters; GitLab enforces this)
- A phone or authenticator app if you plan to set up 2FA (strongly recommended)

---

## Signing Up on GitLab.com

Navigate to `https://gitlab.com/users/sign_up`. The signup form asks for:

- **First name** and **Last name**: your real name or a display name. This appears on commits, comments and merge requests.
- **Username**: your unique identifier on GitLab. This forms part of your profile URL and your project URLs.
- **Email**: must be accessible - you will receive a confirmation link.
- **Password**: minimum 8 characters. GitLab checks against known breached passwords.

After clicking **Register now**, GitLab sends a confirmation email. Click the link in that email to verify your address. Your account is not fully active until verification is complete.

> [!NOTE]
> You can also sign up using an existing account from GitHub, Google, Bitbucket, Salesforce or a SAML provider if your organisation uses one. The "Continue with GitHub" and similar buttons on the sign-in page trigger OAuth-based authentication.

After verification, GitLab asks a short onboarding questionnaire: your role, team size, what you are trying to do. This is optional and you can skip or answer quickly - it is used to personalise the initial experience and does not affect your account in any technical way.

---

## Username Rules and Choosing Well

Your username matters more than it might seem. It appears in:
- Your profile URL: `https://gitlab.com/yourusername`
- Every project URL you create: `https://gitlab.com/yourusername/project-name`
- Every commit you make (combined with your Git `user.name` setting)
- Every mention of you in issues and merge requests: `@yourusername`
- The registry path for any container images you publish

**Technical rules:**
- 2 to 255 characters
- Only non-accented letters, digits, underscores (`_`), hyphens (`-`) and dots (`.`)
- Cannot start with `_`, `-` or `.`
- Cannot end with `.` or reserved file extensions like `.git`, `.atom`
- Cannot contain consecutive dots (`.`) or a dot followed by an underscore
- Cannot contain emoji

**Practical advice:**
- Use something consistent with your GitHub username if you have one - many developers use the same handle everywhere
- Lowercase is conventional
- Avoid dots in usernames if possible - they can cause confusion with domain names and some tooling
- Your username can be changed later (Avatar → Edit profile → Username) but this breaks existing links, webhooks and any scripts that hardcode your project URLs. Choose carefully.

---

## Email Verification and Identity Checks

### Email confirmation

GitLab requires email verification before you can use your account. The confirmation email is sent immediately after registration. If it does not arrive within a few minutes, check your spam folder. You can request a new confirmation email from the sign-in page.

You can add additional verified email addresses to your account later: Avatar → Edit profile → Email. Secondary emails can be used for notifications, signing in and for Git commits (if you commit using a secondary address, GitLab links those commits to your account).

> [!CAUTION]
> Secondary email addresses that are added but not verified within 3 days are automatically removed. If you add a work email, verify it immediately.

### Identity verification on GitLab.com

GitLab.com applies **risk-based identity verification** - additional checks triggered when the platform determines that an account poses a risk of abuse. You may be asked to verify via phone number or credit card when:

- Creating a new account that matches patterns associated with abuse
- Enabling shared runners (CI/CD execution) on the Free tier
- Performing certain actions that could generate significant compute cost

These checks are not applied to every user. If you are triggered for verification:

**Phone verification**: enter your phone number, receive an SMS code, enter it to verify. Your phone number is used for verification only and is not displayed on your profile.

**Credit card verification**: GitLab places a $1 authorisation charge that is immediately reversed. No money transfers. GitLab does not store card details. This is specifically an anti-abuse mechanism for the shared runners - cryptocurrency miners and spammers would otherwise use free CI/CD compute at scale.

---

## Setting Up Your Profile

After signing in, go to: **Avatar (top-right) → Edit profile**

### Main settings

- **Full name**: what people see on your profile and in merge request reviews. Does not have to be your legal name.
- **Pronouns**: displayed on your profile (max 50 characters).
- **Name pronunciation**: text guide to saying your name (max 255 characters). Displayed as tooltip on your name.
- **Bio**: short description of yourself (max 255 characters).
- **Website**: link to your personal site, portfolio or any URL.

### Avatar

Click your current avatar to upload a photo. GitLab also uses Gravatar (an avatar service based on email hash) if no avatar is set. SVG files are not supported.

### Status

Click your avatar → **Set status** to set a short status message visible to other users. Options:

- **Emoji**: choose one to display next to your name
- **Message**: up to 100 characters
- **Busy**: tells others you are not available for reviews or questions
- **Clear after**: automatically resets your status after 30 minutes, 3 hours, 8 hours, 24 hours or a custom time

### Social and contact links

Under the **Main settings** section you can add links to: Discord, Bluesky, GitHub, LinkedIn, Mastodon (with verified account badge since GitLab 18.8), ORCID (for researchers), X (formerly Twitter).

### Privacy settings

- **Private profile**: checking this hides your activity, groups, followers and following from the public. Your name and username remain visible.
- **Private contributions**: hides contribution activity on your profile even if your profile itself is public.

### Profile README

To add a longer description and showcase to your profile, create a **public project** with the same name as your username. For example, if your username is `zaccess`, create a project at `gitlab.com/zaccess/zaccess`. Any `README.md` in that project appears on your profile page automatically.

---

## Two-Factor Authentication

Two-factor authentication (2FA) adds a second verification step beyond your password. Even if someone steals your password, they cannot sign in without the second factor. **Enable 2FA immediately on any account you plan to use seriously.** Some organisations and projects require it.

Access 2FA settings at: **Avatar → Edit profile → Access → Password and authentication**

### Method 1: TOTP (Time-based One-Time Password) - recommended

TOTP generates a new 6-digit code every 30 seconds. You enter this code alongside your password when signing in.

**Compatible apps (all free):**
- **Aegis Authenticator** (Android, open source) - recommended for privacy
- **Google Authenticator** (Android, iOS)
- **Microsoft Authenticator** (Android, iOS)
- **Authy** (Android, iOS, desktop)
- **1Password** and **Bitwarden** (password managers with built-in TOTP)

**Setup steps:**

1. Avatar → Edit profile → Access → Password and authentication
2. Under **Two-factor authentication**, click **Enable two-factor authentication**
3. Open your authenticator app and scan the QR code, or manually enter the key shown below it
4. Enter the 6-digit code currently shown in your app to confirm it is set up correctly
5. Click **Register with two-factor app**

GitLab immediately shows your **recovery codes** - 10 one-time-use codes for emergency access. **Download or print these immediately and store them somewhere safe that is not your phone.** Each code can only be used once. If you lose your phone and have no recovery codes, you are locked out permanently unless your account admin can intervene.

> [!CAUTION]
> Recovery codes are shown exactly once. If you close the dialog without saving them, you cannot retrieve them. Generate new ones at any time from: Avatar → Edit profile → Access → Password and authentication → "Generate new recovery codes".

### Method 2: WebAuthn hardware security keys

Hardware keys (such as YubiKey, Google Titan Key or Feitian) provide the strongest 2FA. They require physical possession of the key to sign in.

**Setup steps (requires TOTP first, or since GitLab 17.6, can be standalone):**

1. Avatar → Edit profile → Access → Password and authentication
2. Scroll to **WebAuthn devices** section
3. Click **Register new device**
4. Enter a name for the key (e.g. "YubiKey work")
5. When prompted, insert the key and touch it
6. GitLab registers the key's public key and stores nothing that could be used to clone it

You can register multiple hardware keys. If you lose one, remove it from your account and the others still work.

### Method 3: Passkeys

Passkeys are covered in detail in the next section. They can also function as a 2FA method.

---

## Passkeys - Passwordless Sign-in

Passkeys are a modern authentication standard (WebAuthn-based, FIDO2 compliant) that replaces passwords with cryptographic key pairs. Your private key is stored securely on your device (hardware security element, biometric chip or platform authenticator). The public key is stored on GitLab. Sign-in requires proving physical possession of the device and, usually, biometric verification or PIN.

Passkeys became **generally available (GA) in GitLab 18.9** (February 2026) and are highlighted as a key feature of GitLab 18.10.

### What passkeys give you

- **Phishing resistance**: passkeys are bound to the exact domain they are registered for. A fake login page cannot harvest a passkey credential.
- **No password to steal**: there is no shared secret that lives on a server. Even if GitLab's database were compromised, your passkey cannot be extracted.
- **Passwordless flow**: sign in with a fingerprint, face scan or device PIN - no typing required.
- **Cross-device**: passkeys can sync across your devices through your platform (iCloud Keychain on Apple, Google Password Manager on Android/Chrome, Windows Hello on Windows).

### Compatible devices and platforms

| Platform | Authentication method |
|---|---|
| iPhone / iPad (iOS 16+) | Face ID, Touch ID, Passkey sync via iCloud |
| Android 9+ | Fingerprint, face recognition, Google Password Manager sync |
| macOS | Touch ID, Face ID, iCloud Keychain |
| Windows 10/11 | Windows Hello (PIN, fingerprint, face) |
| Linux | Hardware security keys (FIDO2), browser-stored passkeys |
| Hardware keys | YubiKey 5 series, Feitian, Google Titan |

Browsers: Chrome 109+, Safari 16+, Firefox 122+, Edge 109+ all support passkeys.

### Setting up a passkey

1. Avatar → Edit profile → Access → Password and authentication
2. Scroll to **Passkey sign-in** section
3. Click **Add passkey**
4. Enter a name for this passkey (e.g. "MacBook Touch ID", "iPhone Face ID")
5. Your browser or device prompts you to authenticate - use fingerprint, face or PIN
6. GitLab confirms the passkey has been registered

You can register multiple passkeys (different devices, hardware keys). Each appears in your passkey list with a name you assigned.

### Signing in with a passkey

On the GitLab sign-in page:
1. Under the username/password form, find **Or sign in with** or **other sign-in options**
2. Click **Sign in with a passkey**
3. Your device prompts for biometric or PIN verification
4. You are signed in - no password needed

> [!TIP]
> If you add a passkey as your primary authentication, you can remove your password entirely. This is optional - passkeys and passwords can coexist. For the strongest security, use a passkey (phishing-resistant) plus a TOTP app for backup.

---

## SSH Keys

SSH keys let you push, pull and clone repositories without typing your username and password every time. They work by generating a key pair: a public key (which you share with GitLab) and a private key (which stays on your machine). When you connect, GitLab sends a cryptographic challenge that only your private key can answer.

### Generate an SSH key pair

The recommended algorithm is **Ed25519** - faster and more secure than the older RSA type.

🍎🐧:
```bash
# Generate an Ed25519 key
ssh-keygen -t ed25519 -C "your_email@example.com"

# When prompted for a file location, press Enter to accept the default
# ~/.ssh/id_ed25519 (private key)
# ~/.ssh/id_ed25519.pub (public key - this is what you share)

# When prompted for a passphrase, set one for extra security
# (you will only need to type it when the SSH agent is not running)
```

🪟 (Git Bash or WSL):
```bash
# Same command works in Git Bash
ssh-keygen -t ed25519 -C "your_email@example.com"

# Files created in:
# C:\Users\YourName\.ssh\id_ed25519
# C:\Users\YourName\.ssh\id_ed25519.pub
```

> [!NOTE]
> If you are on a system where Ed25519 is not supported (very rare), use RSA with a large key size: `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`. The `-b 4096` specifies 4096-bit key length.

### Add your key to the SSH agent

The SSH agent holds your private key in memory so you do not need to enter your passphrase repeatedly.

🍎🐧:
```bash
# Start the agent
eval "$(ssh-agent -s)"

# Add your key
ssh-add ~/.ssh/id_ed25519
```

🪟 (PowerShell as Administrator, Windows 10+):
```powershell
# Enable the OpenSSH Agent service
Set-Service -Name ssh-agent -StartupType Automatic
Start-Service ssh-agent

# Add your key
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```

🍎 (macOS - add to Keychain for persistence across reboots):
```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

### Copy your public key

🍎🐧:
```bash
cat ~/.ssh/id_ed25519.pub
# Outputs something like:
# ssh-ed25519 AAAAC3NzaC1... your_email@example.com
```

🪟 (PowerShell):
```powershell
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub
```

Copy the entire output line including the `ssh-ed25519` at the start and your email at the end.

### Add the public key to GitLab

1. Avatar → Edit profile → Access → SSH keys
2. Click **Add new key**
3. Paste your public key into the **Key** field
4. **Title**: give it a recognisable name (e.g. "MacBook Pro 2025", "Windows work laptop")
5. **Usage type**: Authentication & Signing is the recommended default. Authentication allows push/pull. Signing allows commit signing (see [git/20-signing-commits.md](../git/20-signing-commits.md)).
6. **Expiration date**: optional. Setting an expiry date is good security practice for keys on shared or temporary machines. Leave blank for personal machines you control.
7. Click **Add key**

### Test the connection

```bash
ssh -T git@gitlab.com
```

Expected response:
```
Welcome to GitLab, @yourusername!
```

If you see a host verification prompt (`The authenticity of host 'gitlab.com' can't be established`), type `yes` to accept and continue. This happens only once per machine.

If the connection times out or refuses, check: is the SSH agent running? Is the key added (`ssh-add -l`)? Is port 22 open on your network?

### Using SSH vs HTTPS

When you clone a repository, GitLab gives you two URL options:

- **SSH**: `git@gitlab.com:namespace/project.git` - uses your SSH key, no password prompts after setup
- **HTTPS**: `https://gitlab.com/namespace/project.git` - requires a personal access token or password (password-based authentication for Git was removed in 2021)

SSH is recommended for regular use. HTTPS is simpler for one-off clones on a machine you do not own, or in environments where SSH is blocked.

### Supported key types

GitLab accepts: `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`, `ecdsa-sha2-nistp521`, `ssh-ed25519`, `sk-ecdsa-sha2-nistp256@openssh.com` (hardware-backed ECDSA), `sk-ssh-ed25519@openssh.com` (hardware-backed Ed25519, recommended for hardware keys).

> [!TIP]
> To generate a hardware-backed SSH key (stored on a YubiKey rather than on disk), use: `ssh-keygen -t ecdsa-sk -C "your_email@example.com"` or `ssh-keygen -t ed25519-sk -C "your_email@example.com"`. The private key is generated inside the hardware key and never leaves it.

---

## Personal Access Tokens

Personal access tokens (PATs) are authentication credentials for use with the GitLab API, Git-over-HTTPS and third-party tools like `glab` (the GitLab CLI), IDEs and CI/CD scripts. They are not used for signing into the web interface - that uses your password or passkey.

### Creating a token

1. Avatar → Edit profile → Access tokens
2. Click **Add new token**
3. Fill in:
   - **Token name**: something that identifies what it is used for and where (e.g. "glab CLI - work laptop", "VS Code GitLab extension")
   - **Description** (optional): more detail about the token's purpose
   - **Expiration date**: required. Maximum 365 days from creation. Set this as short as practical - tokens are credentials and shorter lifetimes reduce the window for misuse.
   - **Scopes**: tick only what this token needs. Do not use `api` scope for a token that only needs `read_repository`.
4. Click **Create personal access token**
5. **Copy the token value immediately** - it is shown only once. After you navigate away, it cannot be retrieved.

> [!CAUTION]
> GitLab shows the token value only at the moment of creation. There is no "view token" button later. If you lose it, delete the token and create a new one.

### Token scopes

| Scope | What it allows |
|---|---|
| `api` | Full read/write access to the API, all projects, groups, registries and Git-over-HTTPS. The most powerful scope. |
| `read_api` | Read-only API access to everything `api` covers. |
| `read_user` | Read your own profile information and authenticated user data. |
| `create_runner` | Register new GitLab Runners to projects or groups. |
| `manage_runner` | Manage (pause, delete, update) Runners. |
| `k8s_proxy` | Access Kubernetes clusters through the GitLab agent. |
| `read_repository` | Read-only access to repositories via Git-over-HTTPS for private projects. |
| `write_repository` | Read-write access to repositories via Git-over-HTTPS. Allows push without full API access. |
| `read_registry` | Pull container images from the registry. |
| `write_registry` | Push container images to the registry. |
| `ai_features` | Access the GitLab Duo AI APIs. |
| `sudo` | Impersonate other users via the API (admin only). |

**Example use cases:**

- **glab CLI**: needs `api` + `write_repository` for full functionality
- **Read-only CI/CD status checker**: needs `read_api` only
- **Container image pulling in production**: needs `read_registry` only
- **Git clone in a script**: needs `read_repository` only

### Token rotation

Before a token expires, GitLab sends email notifications at 60 days and 30 days before expiry. You can also rotate tokens manually: go to the token in your list, click **Rotate** (since GitLab 17.7). This creates a new token with the same name and scopes, immediately invalidates the old one, and shows you the new value once.

If you use CI/CD secrets or environment variables to hold tokens, build a rotation process into your workflow. Expired tokens cause pipeline failures and deployment outages.

### Using tokens with Git over HTTPS

```bash
# Clone using a token
git clone https://oauth2:YOUR_TOKEN@gitlab.com/namespace/project.git

# Or configure Git to use the token for all GitLab operations
git config --global credential.helper store
# Then on first use, enter: username = oauth2, password = YOUR_TOKEN
```

### Using tokens with the API

```bash
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" "https://gitlab.com/api/v4/user"
```

---

## Understanding the Free Tier

The GitLab Free tier is genuinely useful but has specific limits you need to understand before starting a project.

### The five-user limit

The most important limit on the Free tier: **private groups can have a maximum of five unique members across all projects and subgroups within that group**.

Every unique user who has access to any project or subgroup within a private top-level group counts toward this limit, regardless of their role level.

**Example**: you create a group called `my-team`. Under it you create three projects. You add four team members to various projects. Even if no single project has more than five members, the total unique members across the group is what counts.

When a private group exceeds five unique members, GitLab puts the **entire namespace into read-only mode**. No new code can be pushed, no issues can be created, nothing can be changed. To resolve it you must either: reduce the member count back to five, or upgrade to a paid plan.

**Exemptions to the five-user limit:**
- Public groups and projects have no user limit
- Personal namespaces (your own projects, not in a group) have no user limit
- GitLab for Education and GitLab for Open Source programme members are exempt

### Top-level group limit

Accounts created **after 27 January 2026** are limited to **three top-level groups**. A top-level group is a group that sits at the root of the namespace - `gitlab.com/my-group`. Subgroups inside a group do not count toward this limit.

If you need more than three top-level groups, you must upgrade to a paid plan. This limit does not affect your personal namespace.

### Compute minutes

The Free tier includes **400 compute minutes per month** for pipelines running on GitLab.com's shared runners. Shared runners are the Linux, Windows and macOS machines that GitLab.com provides for running CI/CD jobs.

400 minutes sounds like a lot but runs out quickly if:
- You run tests on every push to every branch
- Your test suite takes more than a few minutes
- Multiple team members push frequently throughout the day

**The solution is straightforward and free**: install your own GitLab Runner on any machine - your development laptop, a Raspberry Pi, a cheap cloud VM - and register it to your project. **Self-hosted runners have no compute minute limits and are completely free.** See [06-gitlab-cicd.md](06-gitlab-cicd.md) for runner installation.

Compute minutes reset on the first of every month. You can purchase additional minutes at $10 per 1,000.

### Storage

Each project on the Free tier has **10 GiB** of storage for the Git repository and Large File Storage (LFS). Container registry and package registry storage is measured separately.

For most code projects, 10 GiB is more than enough. It becomes a constraint for projects that store large binary files, built artifacts or generated assets in the repository. Use `.gitignore` aggressively and consider LFS for large files you do need to track.

Premium and Ultimate projects get **500 GiB per project**.

### What the Free tier includes

Despite the limits, the Free tier is substantial. You get:

- Unlimited public and private projects
- Unlimited personal project storage (no five-user limit on personal namespace)
- Issues, merge requests, milestones, labels, boards
- CI/CD pipelines with 400 shared runner minutes/month (or unlimited with self-hosted runners)
- GitLab Pages (free static site hosting)
- Container Registry (unlimited images)
- Package Registry (npm, PyPI, Maven, NuGet, Cargo, etc.)
- Basic SAST and secret detection in CI/CD
- GitLab Snippets
- Project and group wikis
- Service Desk
- REST and GraphQL APIs
- Webhooks
- GitLab Duo Core (basic AI: Code Suggestions and Chat, included with Premium/Ultimate plans from 18.0 onward; access on Free tier through credits - see 10-gitlab-duo.md)

---

## The Credit Card Requirement for Shared Runners

> [!IMPORTANT]
> You do not need to provide a credit card to create a GitLab account or use most features. The credit card requirement is specific to using **shared runners** on the Free tier.

GitLab.com's shared runners are the managed machines that execute your CI/CD pipelines without any setup. They are valuable and costly to operate. In 2021, GitLab experienced severe abuse of the free shared runners - mainly cryptocurrency mining operations that created thousands of accounts and ran mining software 24/7 on GitLab's infrastructure, causing significant performance problems for all users.

GitLab's response was to require credit card verification before Free tier accounts can use shared runners. The card is used for **identity verification only**. GitLab charges $1 to verify the card, then immediately reverses it. No money is held, no charges are made, and GitLab does not store your card details.

**Alternatives that do not require a card:**
- **Install your own GitLab Runner**: free, no card required, no minute limits. Your runner runs on hardware you control.
- **Use a Runner registered to your project**: if someone else in your project has already set up a runner, you can use theirs.
- **Disable shared runners and use only project-specific runners**: in project Settings → CI/CD → Runners, you can disable shared runners entirely.

If you are in a country where credit card ownership is uncommon or your card is declined, the runner installation option is the practical path.

---

## Signing in on Self-Managed GitLab

If your organisation runs its own GitLab instance (self-managed), the sign-up and sign-in experience is different:

- **Account creation may be disabled**: many organisations disable public registration. Your administrator creates your account for you.
- **SAML SSO**: large organisations often configure GitLab to use their existing identity provider (Active Directory, Okta, OneLogin). You sign in with your corporate credentials using the "SAML" or company-specific button on the sign-in page.
- **LDAP integration**: some instances authenticate against LDAP/Active Directory directly. You use your corporate username and password.
- **Your instance URL**: not `gitlab.com` but something like `gitlab.yourcompany.com` or `code.yourcompany.internal`. Get this from your administrator.

Self-managed GitLab's sign-in page looks the same as GitLab.com's but may have additional options (SAML, LDAP) or fewer options (no sign-up button if registration is disabled).

SSH key setup and personal access token creation work identically on self-managed instances - the UI paths are the same. The SSH URL format changes: instead of `git@gitlab.com`, it is `git@your.instance.com`.

```bash
# Test SSH connection to a self-managed instance
ssh -T git@your.instance.com
# Expected: Welcome to GitLab, @yourusername!
```

---

## Try It Yourself

**Exercise 1: Create your account and profile**

1. Go to `https://gitlab.com/users/sign_up` and create your account
2. Verify your email address
3. Go to Avatar → Edit profile and set:
   - A bio (what you are working on or learning)
   - Your pronouns if you wish
   - A profile photo or avatar
4. Find your public profile page at `https://gitlab.com/YOUR_USERNAME`

**Exercise 2: Set up TOTP two-factor authentication**

1. Install an authenticator app if you do not have one (Aegis on Android, built-in on iOS)
2. Avatar → Edit profile → Access → Password and authentication
3. Enable two-factor authentication, scan the QR code and enter the 6-digit code
4. **Download and store your recovery codes somewhere safe**
5. Sign out and sign back in - you will be prompted for a code from your app

**Exercise 3: Generate and add an SSH key**

🍎🐧:
```bash
# Generate (replace with your email)
ssh-keygen -t ed25519 -C "you@example.com"

# Start agent and add key
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Copy your public key
cat ~/.ssh/id_ed25519.pub
```

🪟:
```powershell
# Generate
ssh-keygen -t ed25519 -C "you@example.com"

# Start agent (run PowerShell as Admin first)
Set-Service -Name ssh-agent -StartupType Automatic
Start-Service ssh-agent
ssh-add $env:USERPROFILE\.ssh\id_ed25519

# Copy public key
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub
```

Paste the output into GitLab: Avatar → Edit profile → Access → SSH keys → Add new key.

Test: `ssh -T git@gitlab.com` - you should see a welcome message.

**Exercise 4: Create a personal access token**

1. Avatar → Edit profile → Access tokens → Add new token
2. Name it "test token - delete after exercise"
3. Set expiry 7 days from now
4. Select only `read_user` scope
5. Create and copy the token
6. Test it:

```bash
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" "https://gitlab.com/api/v4/user"
```

You should see your account details in JSON. Then delete the token: back in the tokens list, click **Revoke**.

---

## Common Mistakes

**Not saving recovery codes.** GitLab shows your 2FA recovery codes exactly once. Many people dismiss the dialog without saving them. If you lose your phone and have no recovery codes, you cannot sign in. There is no account recovery form - without the second factor or a recovery code, GitLab cannot verify your identity. Save the codes somewhere safe: a password manager, printed paper in a secure location, an encrypted file.

**Confusing SSH public and private keys.** The file ending in `.pub` is your public key. This is what you paste into GitLab and share freely. The file without `.pub` (e.g. `id_ed25519`) is your private key. Never share this, never paste it into any web form, never commit it to a repository.

**Using the password to clone over HTTPS.** Password authentication for Git operations was removed from GitLab in 2021. If you use HTTPS URLs, you need a personal access token as the password, not your account password. Using your account password directly will fail with an authentication error.

**Not testing the SSH connection.** After adding your SSH key, many people assume it works and discover later that it does not - usually in the middle of a push. Run `ssh -T git@gitlab.com` immediately after adding the key to confirm everything is configured correctly.

**Creating tokens with too broad a scope.** A token used only to read repository contents does not need the `api` scope. Using `api` scope gives the token full write access to everything. Follow the principle of least privilege: give each token only the scopes it needs for its specific purpose.

**Not setting a token expiry or setting it too long.** Tokens without expiry existed before GitLab 16.0 but are no longer allowed. Always set an expiry that matches the token's use: a token for a weekend project can expire in 30 days; a token embedded in a long-running CI/CD system might be 90-365 days. Build a rotation reminder into your calendar.

**Forgetting the five-user group limit on the Free tier.** Many people set up a group, invite their team and continue working, not realising they have hit the limit. GitLab does not warn you prominently until the namespace goes read-only. Count your users before choosing whether to use the Free tier for a private group.

---

## Summary

- Create your account at `https://gitlab.com/users/sign_up`. Email verification is required before the account is active.
- **Username** forms part of every project URL and cannot easily be changed later. Choose it carefully.
- **Two-factor authentication**: enable it immediately using a TOTP app (Aegis, Google Authenticator, etc.) and save your recovery codes somewhere permanent. WebAuthn hardware keys add physical security.
- **Passkeys** (GA since GitLab 18.9) allow passwordless sign-in using biometrics or device PIN and are phishing-resistant. Set up via Avatar → Edit profile → Access → Password and authentication.
- **SSH keys**: generate Ed25519 with `ssh-keygen -t ed25519 -C "email"`. Copy the `.pub` file to GitLab (Avatar → Edit profile → SSH keys). Test with `ssh -T git@gitlab.com`.
- **Personal access tokens**: create at Avatar → Edit profile → Access tokens. Copy immediately (shown once). Use minimum required scopes. Set the shortest practical expiry.
- **Free tier limits**: 5 users per private group, 3 top-level groups (accounts after Jan 2026), 400 compute minutes/month, 10 GiB storage/project. Self-hosted runners eliminate the compute minute limit for free.
- **Credit card for shared runners**: required on Free tier to verify identity and prevent abuse. No money charged. Not required if you use your own runners.
- Self-managed GitLab uses the same profile and SSH setup but may use SAML/LDAP for authentication and has a different URL.

---

## Sources and Further Reading

- [GitLab account sign up documentation](https://docs.gitlab.com/user/profile/account/create_accounts/) - official signup guide
- [Two-factor authentication](https://docs.gitlab.com/user/profile/account/two_factor_authentication/) - complete 2FA setup guide
- [Passkeys documentation](https://docs.gitlab.com/auth/passkeys/) - passkey setup and management
- [SSH keys in GitLab](https://docs.gitlab.com/user/ssh/) - generating, adding and troubleshooting SSH keys
- [Personal access tokens](https://docs.gitlab.com/user/profile/personal_access_tokens/) - scopes, rotation and best practices
- [Free tier limits](https://docs.gitlab.com/user/free_user_limit/) - official documentation on the five-user limit
- [GitLab pricing and plans](https://about.gitlab.com/pricing/) - current plan details
- [Identity verification](https://docs.gitlab.com/security/identity_verification/) - when and why it is required
- [GitLab runner installation](https://docs.gitlab.com/runner/install/) - setting up your own runner to avoid shared runner limits

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
