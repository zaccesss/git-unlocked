# Signing Commits

**Difficulty:** 🔴 Advanced | **Time:** 25 minutes

Commit signing proves that a commit was genuinely made by you and has not been tampered with. Without signing, anyone who knows your name and email can create commits that appear to be from you. Signed commits display a "Verified" badge on GitHub and GitLab. In enterprise environments and open source security-critical projects, signing is increasingly required.

---

## Table of Contents

- [Why sign commits?](#why-sign-commits)
- [GPG signing vs SSH signing](#gpg-signing-vs-ssh-signing)
- [Setting up GPG signing](#setting-up-gpg-signing)
- [Setting up SSH signing](#setting-up-ssh-signing)
- [Signing commits](#signing-commits)
- [Signing tags](#signing-tags)
- [Verifying signatures](#verifying-signatures)
- [Adding your key to GitHub](#adding-your-key-to-github)
- [Adding your key to GitLab](#adding-your-key-to-gitlab)
- [Vigilant mode on GitHub](#vigilant-mode-on-github)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Why Sign Commits?

Git commits contain an author name and email, but Git does not verify these by default. Anyone can set any name and email in their Git config:

```bash
git config user.name "Someone Else"
git config user.email "someone@else.com"
```

Commits made with these settings will appear in the history as if that person made them. This is not a vulnerability in most contexts, but in security-sensitive environments it matters - you want to be certain that a commit claiming to be from a senior engineer actually is.

Commit signing uses cryptography to prove that:

1. You created the commit (you control the private key)
2. The commit has not been modified since you created it

GitHub and GitLab display a **Verified** badge on signed commits. Many enterprise repositories and security-critical open source projects require all commits to be signed.

---

## GPG Signing vs SSH Signing

There are two methods for signing commits in Git. Both produce a **Verified** badge on GitHub and GitLab.

**GPG signing** uses the GnuPG cryptography standard. It has been available in Git for longer and is more widely supported in auditing and compliance tooling. Setting it up requires installing GPG and managing a keyring.

**SSH signing** uses your existing SSH key pair. If you already have an SSH key set up for authentication, you can use the same key for signing with minimal additional configuration. This was added in Git 2.34 (November 2021) and is now the simpler option for most developers.

**Recommendation:** Use SSH signing if you are setting this up for the first time. It reuses your existing SSH key and requires less configuration. Use GPG if your organisation requires it, if you need hardware key support or if you are working in a compliance-heavy environment.

---

## Setting up GPG Signing

### Install GPG

**Windows:**

Download and install [Gpg4win](https://www.gpg4win.org). This includes GPG and a GUI tool called Kleopatra.

**Mac:**

```bash
brew install gnupg
```

**Linux (Ubuntu/Debian):**

```bash
sudo apt install gnupg
```

### Generate a GPG key

```bash
gpg --full-generate-key
```

Follow the prompts:
- Key type: RSA and RSA (default)
- Key size: **4096**
- Expiry: your choice (1-2 years is a sensible default)
- Name and email: use the same email as your GitHub/GitLab account
- Passphrase: use a strong passphrase

### Get your key ID

```bash
gpg --list-secret-keys --keyid-format=long
```

Output:

```
/home/user/.gnupg/secring.gpg
------------------------------
sec   rsa4096/A1B2C3D4E5F6A7B8 2026-01-01 [SC]
      ABCDEF1234567890ABCDEF1234567890ABCDEF12
uid                 [ultimate] Your Name <your@email.com>
ssb   rsa4096/1234567890ABCDEF 2026-01-01 [E]
```

The key ID is the part after `rsa4096/`: `A1B2C3D4E5F6A7B8`.

### Configure Git to use your GPG key

```bash
git config --global user.signingkey A1B2C3D4E5F6A7B8
git config --global gpg.program gpg
```

### Export your public key for GitHub/GitLab

```bash
gpg --armor --export A1B2C3D4E5F6A7B8
```

Copy the entire output including `-----BEGIN PGP PUBLIC KEY BLOCK-----` and `-----END PGP PUBLIC KEY BLOCK-----`.

> [!NOTE]
> **Windows users:** If Git cannot find GPG, specify the full path:
>
> ```bash
> git config --global gpg.program "C:\Program Files (x86)\GnuPG\bin\gpg.exe"
> ```

---

## Setting up SSH Signing

SSH signing reuses your existing SSH key. If you have not set up an SSH key yet, follow the SSH setup in [introduction/03-setting-up.md](../01-introduction/03-setting-up.md).

### Configure Git to sign with SSH

```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
```

Replace `id_ed25519.pub` with your actual public key filename.

**Windows (PowerShell):**

```powershell
git config --global gpg.format ssh
git config --global user.signingkey "$env:USERPROFILE\.ssh\id_ed25519.pub"
```

### Set up an allowed signers file (for local verification)

To verify SSH-signed commits locally, create an allowed signers file:

**Mac and Linux:**

```bash
echo "your@email.com $(cat ~/.ssh/id_ed25519.pub)" >> ~/.ssh/allowed_signers
git config --global gpg.ssh.allowedSignersFile ~/.ssh/allowed_signers
```

**Windows (PowerShell):**

```powershell
$key = Get-Content "$env:USERPROFILE\.ssh\id_ed25519.pub"
Add-Content "$env:USERPROFILE\.ssh\allowed_signers" "your@email.com $key"
git config --global gpg.ssh.allowedSignersFile "$env:USERPROFILE\.ssh\allowed_signers"
```

---

## Signing Commits

### Sign individual commits

```bash
git commit -S -m "add: signed commit"
```

The `-S` flag tells Git to sign this commit.

### Sign all commits automatically

```bash
git config --global commit.gpgsign true
```

With this setting, every commit is signed automatically. You do not need to remember `-S`.

---

## Signing Tags

```bash
git tag -s v2.0.0 -m "Release version 2.0.0"
```

The `-s` flag creates a signed tag. Use `-s` instead of `-a` for annotated signed tags.

**Sign all tags automatically:**

```bash
git config --global tag.gpgsign true
```

---

## Verifying Signatures

**Verify the signature on a specific commit:**

```bash
git verify-commit a1b2c3d
```

**Show signatures in the log:**

```bash
git log --show-signature
```

**Verify a tag:**

```bash
git verify-tag v2.0.0
```

---

## Adding Your Key to GitHub

**For GPG keys:**

1. Go to **https://github.com/settings/keys**
2. Click **New GPG key**
3. Paste your exported public key
4. Click **Add GPG key**

**For SSH signing keys:**

1. Go to **https://github.com/settings/keys**
2. Click **New SSH key**
3. Set the key type to **Signing Key** (not Authentication Key)
4. Paste your public key
5. Click **Add SSH key**

> [!NOTE]
> You can add the same SSH key as both an Authentication key and a Signing key - these are separate entries in GitHub's settings. Authentication keys are for pushing/pulling, signing keys are for verifying commits.

---

## Adding Your Key to GitLab

**For GPG keys:**

1. Go to **https://gitlab.com/-/user_settings/gpg_keys**
2. Paste your exported GPG public key
3. Click **Add key**

**For SSH signing keys:**

1. Go to **https://gitlab.com/-/user_settings/ssh_keys**
2. Paste your public key
3. Set the usage type to **Signing** or **Authentication & Signing**
4. Click **Add key**

---

## Vigilant Mode on GitHub

GitHub's **Vigilant mode** marks all unverified commits as "Unverified" rather than simply showing no badge. This makes it immediately obvious when a commit is not signed, providing stronger security guarantees for repositories that require signing.

**Enable vigilant mode:**

1. Go to **https://github.com/settings/keys**
2. Scroll to the "Vigilant mode" section
3. Check **Flag unsigned commits as unverified**

With vigilant mode enabled, unsigned commits show an explicit "Unverified" badge rather than just no badge. This is recommended for security-conscious developers and required by some organisations.

---

## Try It Yourself

**SSH signing setup (the faster option):**

**Step 1.** Check that you have an SSH key:

```bash
ls ~/.ssh/id_ed25519.pub
```

If it does not exist, create one:

```bash
ssh-keygen -t ed25519 -C "your@email.com"
```

**Step 2.** Configure Git to use SSH for signing:

```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
```

**Windows (PowerShell):**

```powershell
git config --global gpg.format ssh
git config --global user.signingkey "$env:USERPROFILE\.ssh\id_ed25519.pub"
git config --global commit.gpgsign true
```

**Step 3.** Create a test repository and make a signed commit:

**Windows (PowerShell):**

```powershell
mkdir signing-test && cd signing-test && git init
echo "# Test" > README.md
git add README.md && git commit -m "add: signed test commit"
```

**Mac and Linux:**

```bash
mkdir signing-test && cd signing-test && git init
echo "# Test" > README.md
git add README.md && git commit -m "add: signed test commit"
```

**Step 4.** Add your SSH signing key to GitHub (Settings -> Keys -> New SSH key -> Signing Key) and push. The commit should show a "Verified" badge.

**Step 5.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q signing-test
```

**Mac and Linux:**

```bash
cd .. && rm -rf signing-test
```

---

## Common Mistakes

**Using the wrong email in the GPG key.**

The email on your GPG key must match the email in your Git config and your GitHub/GitLab account. If they do not match, GitHub cannot verify the signature.

**Not adding the key to GitHub or GitLab.**

Commits are signed locally and the signature is valid cryptographically, but GitHub and GitLab cannot show the "Verified" badge without the corresponding public key in your account settings.

**Forgetting that SSH keys have separate authentication and signing roles on GitHub.**

You need to add your SSH key twice on GitHub if you want to use it for both authentication (pushing/pulling) and signing. They are separate key entries with different types.

**GPG agent not running on Mac or Linux.**

If you get `error: gpg failed to sign the data`, the GPG agent may not be running. Add this to your shell profile (`~/.zshrc` or `~/.bashrc`):

```bash
export GPG_TTY=$(tty)
```

**Signing with an expired key.**

GPG keys have expiry dates. If your key has expired, commits will fail to sign. Either extend the key's expiry or generate a new key.

---

## Summary

- Commit signing proves cryptographically that a commit was made by you and has not been tampered with
- Two methods: **GPG signing** (traditional, widely supported) and **SSH signing** (simpler, reuses existing SSH key)
- SSH signing is recommended for most developers starting fresh - configure with `gpg.format ssh` and `user.signingkey`
- `git config --global commit.gpgsign true` signs all commits automatically
- Add your public key to GitHub (Settings -> Keys) and GitLab (User settings -> SSH/GPG keys) for the "Verified" badge
- GitHub's **Vigilant mode** marks unsigned commits as "Unverified" for stricter security
- SSH signing keys and authentication keys are separate entries in GitHub settings

---

## Sources and Further Reading

- [Official git commit signing documentation](https://git-scm.com/docs/git-commit#Documentation/git-commit.txt--Sltkeyidgt) - the reference for signing options
- [GitHub - Signing commits](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits) - GitHub's complete guide to GPG and SSH signing
- [GitHub - SSH commit signing](https://docs.github.com/en/authentication/managing-commit-signature-verification/about-commit-signature-verification#ssh-commit-signature-verification) - GitHub's guide to SSH signing specifically
- [GitLab - Signing commits](https://docs.gitlab.com/user/project/repository/signed_commits/) - GitLab's guide to commit signing
- [Gpg4win](https://www.gpg4win.org) - the recommended GPG suite for Windows

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
