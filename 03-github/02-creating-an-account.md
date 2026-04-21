# Creating a GitHub Account

**Difficulty:** 🟢 Beginner | **Time:** 25 minutes

Your GitHub account is your identity in the developer community. It is the first thing a recruiter, open source maintainer or potential collaborator will see when they look you up. Getting it right from the start - choosing a good username, enabling proper security, protecting your email and setting up your profile - saves time and builds a professional presence from day one.

---

## Table of Contents

- [Before you sign up](#before-you-sign-up)
- [Choosing a username](#choosing-a-username)
- [Creating your account](#creating-your-account)
- [Verifying your email](#verifying-your-email)
- [Setting up two-factor authentication](#setting-up-two-factor-authentication)
- [Protecting your email address](#protecting-your-email-address)
- [Completing your profile](#completing-your-profile)
- [GitHub Free vs Pro](#github-free-vs-pro)
- [GitHub Student Developer Pack](#github-student-developer-pack)
- [GitHub achievements](#github-achievements)
- [Notification settings](#notification-settings)
- [Account security checklist](#account-security-checklist)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Before You Sign Up

**What you need:**
- An email address (use one you will keep long term - changing it later is possible but inconvenient)
- A chosen username (read the next section before deciding)
- A strong password or a passkey-capable device

**Which email to use:**

Use a personal email address you will have for years - not a university email that expires when you graduate, not a work email that you lose when you change jobs. Your GitHub account will outlast any employer or institution. If you use a university email and your account later loses access to it, recovery becomes complicated.

If you are a student, you can add your university email as a secondary address later specifically to verify the Student Developer Pack, while keeping a personal email as your primary.

---

## Choosing a Username

Your GitHub username is permanent in practice - you can change it but doing so breaks links, bookmarks and any integrations pointing to your profile. Choose carefully.

**The technical rules:**

- Maximum **39 characters**
- Letters (a-z, A-Z), numbers (0-9) and hyphens (-) only
- Cannot start or end with a hyphen
- No consecutive hyphens (--)
- No underscores, dots or special characters
- Case-insensitive (GitHub treats `YourName` and `yourname` as the same)

**What makes a good username:**

**Use your real name or a recognisable variation.** Employers, collaborators and open source maintainers search GitHub for people they have met or heard of. A username based on your real name is immediately findable. Examples: `jane-smith`, `janesmith`, `j-smith`, `jsmith`.

**Keep it short.** You will type this username regularly, paste it into forms and use it in profile links. Shorter is better.

**Make it consistent.** If possible, use the same username across GitHub, LinkedIn, Twitter/X and other professional platforms. Consistency makes you easier to find and remember.

**Avoid numbers that look random.** `janesmith42` looks professional; `coolcoder9847532` does not.

**Avoid words that date or limit you.** Usernames like `student2024`, `pythonnerd` or `androiddev` either suggest a phase you will grow out of or pigeonhole you into a single technology.

**Think about recruiters.** Your GitHub URL will be on your CV: `github.com/YOUR_USERNAME`. `github.com/jane-smith` looks professional. `github.com/xXdarkl0rdXx` does not.

> [!TIP]
> If your preferred username is taken, try adding your country code, a hyphen, or using initials. For example, if `janesmith` is taken, try `jane-smith`, `jsmith` or `j-a-smith`. Avoid adding long strings of numbers.

**What happens if you change your username later:**

GitHub creates a redirect from your old username to your new one, but this redirect is temporary and can be claimed by another user. Any existing links to `github.com/OLD_USERNAME` will break if someone else claims that name. Hardcoded references in documentation, `package.json` files and CI configurations will also need updating.

---

## Creating Your Account

**Step 1.** Go to [github.com](https://github.com) and click **Sign up**.

**Step 2.** Enter your **email address** and click **Continue**.

**Step 3.** Create a **password**. GitHub requires a minimum of 15 characters, or 8 characters if it includes a number and a lowercase letter. Use a password manager to generate and store a strong password.

**Step 4.** Enter your chosen **username**. GitHub checks availability in real time.

**Step 5.** Choose whether to receive product updates and announcements by email. This is optional.

**Step 6.** Complete the **verification puzzle** (GitHub's CAPTCHA).

**Step 7.** Click **Create account**.

**Step 8.** GitHub sends a **launch code** to your email. Enter the 8-digit code to verify your address.

**Step 9.** Answer a few optional questions about your experience and intended use. These personalise your dashboard. You can skip them.

**Step 10.** You are in. GitHub presents you with a personalised dashboard.

---

## Verifying Your Email

Email verification is required before you can create repositories, open issues or interact with other repositories. If you skipped verification or need to resend the code:

1. Go to **Settings** (click your profile photo → Settings)
2. Click **Emails** in the left sidebar
3. Click **Resend verification email** next to your unverified address

You can add multiple email addresses to your account. Only the primary address receives account-related notifications.

---

## Setting Up Two-Factor Authentication

**Two-factor authentication (2FA) is mandatory for all GitHub users** who contribute code. GitHub began rolling out this requirement in March 2023 and it now applies to all accounts involved in coding activity - commits, pull requests, packages and Actions.

2FA means that logging in requires both your password and a second factor. Even if someone steals your password, they cannot access your account without the second factor.

**Available 2FA methods (listed from most to least secure):**

### 1. Passkeys (recommended)

Passkeys replace both your password and 2FA with a single cryptographic credential stored on your device. They use your device's biometrics (Face ID, Touch ID, Windows Hello, fingerprint sensor) or a hardware key. Passkeys cannot be phished because they are tied to the specific website and device.

To add a passkey: **Settings → Password and authentication → Passkeys → Add a passkey**.

> [!NOTE]
> Passkeys can sync across devices via iCloud Keychain (Apple), Google Password Manager (Android/Chrome) or a password manager like 1Password or Bitwarden. You can add multiple passkeys for different devices.

### 2. Hardware security keys (FIDO2/WebAuthn)

Physical devices like YubiKey or Google Titan Key plug into USB or tap via NFC. Highly secure, resistant to phishing. Recommended for accounts with access to sensitive or production repositories.

To add a hardware key: **Settings → Password and authentication → Security keys → Add a security key**.

### 3. Authenticator app (TOTP)

Time-based one-time passwords generated by an app on your phone. Common apps: **Authy**, **Google Authenticator**, **Microsoft Authenticator**, **1Password**, **Bitwarden**. Generates a new 6-digit code every 30 seconds.

To set up: **Settings → Password and authentication → Two-factor authentication → Set up using an app**.

Scan the QR code with your authenticator app. **Save the recovery codes** that GitHub displays - store them somewhere safe (a password manager, a printed sheet in a secure location). These are the only way back into your account if you lose access to your authenticator.

### 4. SMS text messages

GitHub sends a code to your phone number by text. This is the least secure option - SIM-swap attacks can intercept SMS codes. Only use SMS if you have no other option, and plan to upgrade to an authenticator app as soon as possible.

### 5. GitHub Mobile as a backup

The GitHub Mobile app can approve 2FA requests via push notification. This is a backup method, not a primary 2FA method.

**Setting up 2FA step by step:**

1. Go to **Settings → Password and authentication**
2. Under "Two-factor authentication", click **Enable two-factor authentication**
3. Choose your primary method (authenticator app recommended)
4. Follow the setup steps
5. **Download and save your recovery codes** - this step is critical
6. Optionally add a hardware key or SMS as a backup method

> [!WARNING]
> If you lose access to your 2FA method and do not have your recovery codes, you will lose access to your account permanently. GitHub cannot bypass 2FA. Save your recovery codes in at least two separate secure locations.

---

## Protecting Your Email Address

Every commit you make contains your name and email address in its metadata. If you use your real email in commits, it will appear in public repositories forever - visible to anyone who clones or browses the repository, and indexed by search engines.

**GitHub's noreply email address** solves this. When you enable email privacy, GitHub provides a special address in the format:

```
ID+USERNAME@users.noreply.github.com
```

For example:

```
12345678+YOUR_USERNAME@users.noreply.github.com
```

This address routes to your real inbox for repository notifications but hides your personal email from commit metadata, contributor graphs and public APIs.

**Enable email privacy:**

1. Go to **Settings → Emails**
2. Check **Keep my email addresses private**
3. Check **Block command line pushes that expose my email**

**Configure Git to use the noreply address:**

GitHub shows your exact noreply address on the Emails settings page. Copy it and run:

**Windows (PowerShell), Mac and Linux:**

```bash
git config --global user.email "12345678+YOUR_USERNAME@users.noreply.github.com"
```

Replace `12345678+YOUR_USERNAME` with your actual noreply address from the settings page.

> [!IMPORTANT]
> If you have already made commits with your real email in a public repository, those commits permanently contain that email. The noreply address only protects future commits. Removing an email from past commits requires rewriting history - covered in [git/15-reset-and-revert.md](../02-git/15-reset-and-revert.md).

---

## Completing Your Profile

Your GitHub profile is your developer CV. It is often the first thing a recruiter or open source maintainer looks at. Fill it in properly.

**Go to Settings → Profile to edit:**

**Profile photo:** Upload a clear, professional photo. A recognisable face builds trust in the open source community. If you prefer not to use your photo, a consistent avatar or illustration works too - just make it distinctive.

**Name:** Your real name or the name you go by professionally. This appears everywhere on GitHub alongside your username.

**Bio:** 160 characters maximum. Be specific. "Electronic engineering and computer science student | Building tools for developers" is more useful than "I like coding". Include what you are working on or interested in.

**Pronouns:** Optional. Visible only to signed-in GitHub users.

**Company/organisation:** Your employer, university or a project you are affiliated with. You can use `@` to link to a GitHub organisation.

**Location:** City and country. Helps in community context and is useful for networking.

**Website:** A link to your personal site, portfolio or LinkedIn. Only one URL is supported, so choose the most important one.

**Social accounts:** GitHub supports linking Twitter/X and LinkedIn accounts directly.

> [!TIP]
> Keep your profile up to date. Outdated information (old employer, old university) looks neglected. Set a reminder to review your profile every few months.

---

## GitHub Free vs Pro

**GitHub Free** is sufficient for most individual developers and students.

| Feature | Free | Pro |
|---|---|---|
| Public repositories | Unlimited | Unlimited |
| Private repositories | Unlimited | Unlimited |
| Collaborators | Unlimited | Unlimited |
| GitHub Actions minutes/month | 2,000 | 3,000 |
| Packages storage | 500 MB | 2 GB |
| GitHub Pages | Public repos only | Public and private repos |
| Required reviewers | Public repos only | All repos |
| Branch protection | Public repos only | All repos |
| Repository insights/traffic | Public repos only | All repos |
| Wikis in private repos | No | Yes |
| Price | **$0** | **$4/month** |

**When to upgrade to Pro:**

- You need branch protection rules or required reviewers on private repositories
- You need to publish GitHub Pages from a private repository
- You need more Actions minutes per month
- You want repository traffic analytics for private repositories

**GitHub Team** ($4/user/month) adds organisation-level features: team access controls, CODEOWNERS, environment deployment branches, draft pull requests and web-based support.

**GitHub Enterprise** ($21/user/month) adds SAML SSO, audit log API, data residency, Enterprise Managed Users and 50,000 Actions minutes per month.

---

## GitHub Student Developer Pack

If you are a student, the **GitHub Student Developer Pack** is one of the most valuable things available to you. It is completely free and includes over 100 tools and services worth thousands of pounds/dollars.

**Eligibility:**
- Aged 13 or older
- Currently enrolled in a degree or diploma-granting course at an accredited institution
- Have a school-issued email address or proof of enrolment

**How to apply:**

1. Go to [education.github.com/pack](https://education.github.com/pack)
2. Click **Get Student Benefits**
3. Sign in to your GitHub account
4. Select your role as **Student**
5. Verify your academic status - either with a school email address (`.edu` or equivalent) or by uploading proof of enrolment (student ID card, enrolment letter)
6. Benefits activate within approximately 72 hours of approval

**Key 2026 benefits include:**

| Benefit | Value |
|---|---|
| **GitHub Copilot Student** | Unlimited completions, premium models, coding agent |
| **GitHub Pro** | Full Pro features |
| **Microsoft Azure** | $100 credit + 25+ free services |
| **JetBrains IDEs** | Free professional licence (IntelliJ, PyCharm, WebStorm etc.) |
| **Namecheap** | Free `.me` domain for 1 year + SSL certificate |
| **DigitalOcean** | $200 in cloud credits |
| **MongoDB Atlas** | $200 in database credits |
| **1Password** | Free password manager for 1 year |
| **Canva Pro** | 12 months free |
| **GitHub Certifications** | 1 free exam voucher |
| **100+ other offers** | Cloud, databases, design, security, education |

Benefits last approximately one year and are renewable while you remain enrolled. Your GitHub Pro access through the Student Pack is free for the duration of your studies.

> [!TIP]
> Apply for the Student Developer Pack as soon as you sign up for GitHub. Even if you already have a GitHub account, you can apply at any point during your studies. The Copilot Student benefit alone is worth significant money - it gives you unlimited access to the same AI coding tools that professionals pay for.

---

## GitHub Achievements

GitHub awards **achievements** - small badges that appear on your profile - for certain actions. They are a light gamification element and a visible record of your activity.

**Current achievements and how to earn them:**

| Achievement | How to earn | Tiers |
|---|---|---|
| **Pull Shark** | Get pull requests merged | Default (2), Bronze (16), Silver (128), Gold (1,024) |
| **Starstruck** | Have a repository receive stars | Default (16 stars), Bronze (128), Silver (512), Gold (4,096) |
| **Pair Extraordinaire** | Co-author a merged pull request | Default (1), Bronze (10), Silver (24), Gold (48) |
| **Galaxy Brain** | Have an answer accepted in Discussions | Default (2), Bronze (8), Silver (16), Gold (32) |
| **Quickdraw** | Close an issue or PR within 5 minutes of opening | No tiers |
| **YOLO** | Merge a pull request without any code review | No tiers |
| **Public Sponsor** | Sponsor open source work via GitHub Sponsors | No tiers |

**Achievements that are no longer earnable:**
- Arctic Code Vault Contributor (2020 GitHub Archive Program)
- Mars 2020 Contributor (NASA Mars Helicopter Mission)

**Managing achievements:** You can hide specific achievements or all achievements from your profile. Go to **Settings → Profile** and toggle the achievement visibility options. You can also set an emoji skin tone preference which changes the appearance of certain achievement badges.

**Highlight badges** (separate from achievements): These appear on your profile for: GitHub Pro, Developer Program Member, Security Bug Bounty Hunter, GitHub Campus Expert and Security Advisory Credit.

---

## Notification Settings

GitHub can generate a significant volume of notifications if left at default settings. Configure them early to avoid inbox overload.

**Access notification settings:** Settings → Notifications

**Notification channels:**
- **Web and mobile** - in-app notifications at [github.com/notifications](https://github.com/notifications) and in GitHub Mobile
- **Email** - notifications sent to your email addresses

**Subscription types:**
- **Participating** - conversations you are directly involved in (you commented, you were @mentioned, you opened the issue)
- **Watching** - all activity on repositories you have chosen to watch
- **Custom** - select specific events: issues, pull requests, releases, discussions etc.

**Recommended settings for beginners:**
- Web notifications: **On** for participating, custom for watching
- Email notifications: **On** for participating only; turn off "All activity" unless you want detailed emails
- Automatically watch repositories: **Off** (otherwise every repository you contribute to floods your feed)

**Managing your inbox:** At [github.com/notifications](https://github.com/notifications) you can filter by reason (direct mention, team mention, review requested etc.), mark as read, save for later and set custom notification routing per organisation.

---

## Account Security Checklist

Before moving on, work through this list:

- [ ] Email address verified
- [ ] Two-factor authentication enabled with an authenticator app or hardware key
- [ ] Recovery codes downloaded and stored safely in at least two locations
- [ ] Email privacy enabled (Keep my email addresses private)
- [ ] Git configured to use the noreply email address
- [ ] Profile photo, name and bio filled in
- [ ] Strong, unique password (not used on any other site)
- [ ] Student Developer Pack applied for (if you are a student)

---

## Try It Yourself

**Step 1.** Create your GitHub account at [github.com/signup](https://github.com/signup). Use the username advice in this file.

**Step 2.** Verify your email address when the verification email arrives.

**Step 3.** Enable 2FA:
- Go to **Settings → Password and authentication**
- Click **Enable two-factor authentication**
- Set up an authenticator app
- Save your recovery codes

**Step 4.** Enable email privacy:
- Go to **Settings → Emails**
- Check **Keep my email addresses private**
- Copy the noreply address shown

**Step 5.** Configure Git to use the noreply address:

**Windows (PowerShell):**

```powershell
git config --global user.email "12345678+YOUR_USERNAME@users.noreply.github.com"
git config --global user.name "Your Name"
```

**Mac and Linux:**

```bash
git config --global user.email "12345678+YOUR_USERNAME@users.noreply.github.com"
git config --global user.name "Your Name"
```

**Step 6.** Complete your profile:
- Go to **Settings → Profile**
- Add a photo, your name, a bio, your location and a website if you have one

**Step 7.** If you are a student, apply for the Developer Pack:
- Go to [education.github.com/pack](https://education.github.com/pack)
- Follow the application steps

**Step 8.** Visit your profile page at `github.com/YOUR_USERNAME` and see how it looks to other people.

---

## Common Mistakes

**Using a university or work email as the primary address.**

University emails expire when you graduate. Work emails become inaccessible when you leave. Use a personal email as your primary GitHub address. Add institutional emails as secondary addresses for verification purposes only.

**Choosing a username you will regret.**

Your username is your professional identity on GitHub. Think about how it looks on a CV and in a URL before committing. If you have already created an account with a poor username, changing it as soon as possible - before it appears in job applications or open source contributions - minimises the damage.

**Skipping 2FA.**

GitHub requires it for code contributors. More importantly, your GitHub account may contain private repositories, API tokens and access to organisation code. An account without 2FA is one phishing email away from being compromised.

**Not saving recovery codes.**

If your phone is lost or stolen and you did not save recovery codes, you are locked out of your account permanently. GitHub cannot bypass 2FA. Save recovery codes in a password manager and a second offline location.

**Using your real email in commits.**

Once committed to a public repository, your email is permanent and indexed. Enable email privacy before making your first commit. If you have already committed with your real email, rotate it immediately and configure the noreply address going forward.

**Ignoring the Student Developer Pack.**

The Student Pack includes GitHub Copilot with unlimited access, which is worth significant money to any developer. Many students never apply because they do not know it exists. If you are a student, apply immediately.

**Not completing your profile.**

An empty profile with no photo, no bio and no activity looks abandoned. Recruiters and maintainers do look at GitHub profiles. A few minutes spent on your profile creates a significantly better first impression.

---

## Summary

- Choose your username carefully - use your real name or a professional variation, keep it short and consistent across platforms
- GitHub usernames allow letters, numbers and hyphens only, with a 39-character maximum
- 2FA is mandatory for all code contributors on GitHub - set up an authenticator app and save your recovery codes
- Enable email privacy to protect your personal email from appearing in commit metadata
- Configure Git to use your GitHub noreply address: `ID+USERNAME@users.noreply.github.com`
- Complete your profile - photo, name, bio, location and website
- GitHub Free is sufficient for most developers; GitHub Pro adds branch protection and Pages for private repositories
- The Student Developer Pack gives verified students unlimited GitHub Copilot, GitHub Pro and 100+ other tools for free
- GitHub awards achievements for contributing, earning stars, co-authoring and other actions
- Configure notification settings early to avoid inbox overload

---

## Sources and Further Reading

- [GitHub account setup documentation](https://docs.github.com/en/get-started/start-your-journey/creating-an-account-on-github) - official guide to creating and configuring a GitHub account
- [GitHub two-factor authentication](https://docs.github.com/en/authentication/securing-your-account-with-two-factor-authentication-2fa/about-two-factor-authentication) - full guide to 2FA options and setup
- [Passkeys on GitHub](https://docs.github.com/en/authentication/authenticating-with-a-passkey/about-passkeys) - official guide to passkey setup and management
- [GitHub email privacy](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-email-preferences/setting-your-commit-email-address) - setting up the noreply email address
- [GitHub Student Developer Pack](https://education.github.com/pack) - apply and see current benefits
- [GitHub profile achievements](https://github.com/Schweinepriester/github-profile-achievements) - community-maintained reference for all achievements and tiers
- [GitHub pricing](https://github.com/pricing) - current plan comparison and pricing

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
