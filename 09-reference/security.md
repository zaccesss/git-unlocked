# Security

**Difficulty:** 🟡 Intermediate to 🔴 Advanced | **Time:** 60 minutes

A comprehensive security reference covering secret detection, signed commits, branch protection, supply chain security, credential management, IDE security, terminal hygiene and platform-specific features - with real-world incident lessons throughout.

---

## Table of contents

- [Introduction](#introduction)
- [Secret detection and prevention](#secret-detection-and-prevention)
- [Signed commits](#signed-commits)
- [Branch protection and access control](#branch-protection-and-access-control)
- [Supply chain security](#supply-chain-security)
- [Credential and secret management](#credential-and-secret-management)
- [Security in IDEs](#security-in-ides)
- [Security in the terminal](#security-in-the-terminal)
- [Platform security features](#platform-security-features)
- [Real-world incidents and lessons](#real-world-incidents-and-lessons)
- [The five controls that prevent most incidents](#the-five-controls-that-prevent-most-incidents)
- [Try it yourself](#try-it-yourself)
- [Common mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Introduction

Git repositories are one of the most valuable targets for attackers. They contain source code, configuration, infrastructure definitions and - far too often - secrets. The 2024 Verizon DBIR found that credentials remain the leading attack vector for breaches. Snyk reported 28 million credentials leaked on GitHub in 2025 alone.

The good news is that the tooling to prevent this is mature, largely free and takes minutes to configure. This file covers every layer: the commit itself, the push, the platform, the IDE, the terminal and the supply chain.

**A key principle runs through all of it:** defence must be layered. No single tool or platform feature catches everything. The combination of a pre-commit hook, push protection, CI scanning and short-lived credentials stops what any individual layer misses.

---

## Secret detection and prevention

### The four-layer model

An effective secret detection posture uses four complementary layers:

1. **Pre-commit hook** (gitleaks or TruffleHog) - catches secrets before they enter local history
2. **CI scan on every push** (gitleaks in GitHub Actions) - catches anything that bypassed the hook
3. **Weekly deep scan with live verification** (TruffleHog) - scans full history with credential validation
4. **Platform push protection** (GitHub or GitLab) - server-side final gate before the remote accepts the push

### gitleaks 🟡

**Current version: v8.30.0**. gitleaks is a Go-based secret scanner using regex rules. It scans Git history, staged changes or directories. It outputs SARIF for GitHub's Security tab.

> [!IMPORTANT]
> gitleaks-action v2+ requires a paid licence for organisation repositories. The CLI itself is MIT-licensed and free. Many 2022-era CI tutorials assume the action is free for all repos - this is no longer the case.

**Install:**

```bash
# 🍎 Mac / 🐧 Linux (Homebrew)
brew install gitleaks

# 🐧 Linux (apt)
sudo apt install gitleaks

# 🪟 Windows (Scoop)
scoop install gitleaks

# All platforms (Go)
go install github.com/gitleaks/gitleaks/v8@latest
```

**Core commands:**

```bash
# Scan entire Git history
gitleaks git .

# Scan staged changes only (use in pre-commit hook)
gitleaks protect --staged

# Scan a directory (not a Git repo)
gitleaks dir /path/to/scan

# Generate a SARIF report for GitHub Security tab
gitleaks git . --report-format sarif --report-path gitleaks.sarif

# Create a baseline of existing findings (ignore them going forward)
gitleaks git . --report-format json --report-path .gitleaks-baseline.json
# Future scans: add --baseline-path .gitleaks-baseline.json
```

**GitHub Actions integration:**

```yaml
name: gitleaks
on: [push, pull_request]
permissions:
  contents: read
  security-events: write
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683
        with: { fetch-depth: 0 }
      - uses: gitleaks/gitleaks-action@cb7149a9b57195b609c63e8518d2c6ef8e62af45
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          GITLEAKS_LICENSE: ${{ secrets.GITLEAKS_LICENSE }}
      - uses: github/codeql-action/upload-sarif@v3
        if: always()
        with: { sarif_file: results.sarif }
```

**Minimum `.gitleaks.toml` config:**

```toml
title = "gitleaks config"
[extend]
useDefault = true

[[rules]]
id = "internal-service-token"
description = "Internal service authentication token"
regex = '''svc_[a-zA-Z0-9]{32}'''
tags = ["internal"]

[rules.allowlist]
regexes = ['''svc_EXAMPLE[a-zA-Z0-9]{26}''']   # known test value
paths = ['''(?i)(test|fixture|testdata)/''']     # test directories
```

### TruffleHog 🔴

**Current version: v3.94.3**. TruffleHog goes beyond regex matching by actively verifying found credentials against provider APIs. A finding is labeled `verified` (live), `unverified` (pattern match, not confirmed) or `unknown` (provider unreachable). `--only-verified` eliminates noise.

> [!NOTE]
> TruffleHog v3+ is licensed AGPL-3.0. Check with your legal team before embedding it in commercial products.

**Install:**

```bash
# 🍎 Mac / 🐧 Linux
brew install trufflehog

# 🐧 Linux (install script)
curl -sSfL https://raw.githubusercontent.com/trufflesecurity/trufflehog/main/scripts/install.sh \
  | sh -s -- -b /usr/local/bin

# 🪟 Windows
scoop install trufflehog

# All platforms (Docker)
docker run --rm -v "$PWD:/pwd" trufflesecurity/trufflehog:latest git file:///pwd
```

**Core commands:**

```bash
# Scan a Git repository (local)
trufflehog git file://. --only-verified

# Scan a remote GitHub repository
trufflehog github --repo https://github.com/OWNER/REPO --only-verified

# Scan an entire GitHub organisation
trufflehog github --org YOUR_ORG --token $GITHUB_TOKEN --only-verified

# Scan only recent history
trufflehog git file://. --since-commit HEAD~50 --only-verified

# Scan a Docker image
trufflehog docker --image ghcr.io/org/image:tag --only-verified
```

**GitHub Actions (recommended for weekly deep scans):**

```yaml
name: trufflehog scan
on:
  schedule:
    - cron: '0 2 * * 1'   # every Monday at 2 AM
  workflow_dispatch:
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683
        with: { fetch-depth: 0 }
      - uses: trufflesecurity/trufflehog@main
        with:
          extra_args: --results=verified,unknown --fail
```

### pre-commit framework

**Current version: 4.5.1**. The `pre-commit` framework manages client-side Git hooks in a declarative YAML file.

```bash
# Install
pip install pre-commit       # or: brew install pre-commit

# Initialise in a repository
pre-commit install            # installs pre-commit hook
pre-commit install --hook-type pre-push   # also install pre-push hook
```

**Recommended `.pre-commit-config.yaml`:**

```yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.30.0
    hooks:
      - id: gitleaks

  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.5.0
    hooks:
      - id: detect-secrets
        args: ['--baseline', '.secrets.baseline']

  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: detect-private-key
      - id: check-added-large-files
        args: ['--maxkb=500']
      - id: check-merge-conflict
      - id: no-commit-to-branch
        args: [--branch, main, --branch, master]
```

```bash
# Keep hook versions current
pre-commit autoupdate

# Run all hooks against all files manually
pre-commit run --all-files
```

> [!NOTE]
> Since pre-commit v4, stages were renamed: `commit` is now `pre-commit`, `push` is now `pre-push`. Run `pre-commit migrate-config` on repositories using older configs.

### GitHub push protection

Push protection is **default-on for all public repositories since 1 March 2024** and for all new personal public repositories since 11 March 2024. It covers 200+ secret types from 180+ providers and blocks the push at the server before the secret lands in remote history.

**For private and internal repositories**, push protection requires GitHub Secret Protection ($19 per active committer per month, introduced April 2025). Private repositories on free plans do not have push protection.

**What happens when a push is blocked:**

```
remote: error: GH013: Repository rule violations found for refs/heads/main.
remote: - GITHUB PUSH PROTECTION
remote:   Affected file: config/app.yml
remote:   Secret:        GitHub Personal Access Token
remote:   —————————————————————————————————————————
remote:   To push, remove this secret or visit https://github.com/...
```

You must either remove the secret from the commit or choose a bypass reason via the link provided. Every bypass is logged as a security alert.

**Enable org-wide for private repos** (requires Secret Protection):

Go to Organisation Settings > Code security > Configurations and enable "Push protection" in the applicable security configuration.

### GitLab secret push protection

Available on **Ultimate tier**. General availability reached in GitLab **17.5**.

**Enable:**

1. Admin > Settings > Security and Compliance > enable "Allow secret push protection"
2. Per project: Secure > Security configuration > toggle "Secret push protection"

**Bypass:** include `[skip secret push protection]` in a commit message, or use `git push -o secret_detection.skip_all`. All bypasses are audited.

**Limits to know:** skipped if a push is over 3,150 paths or over 350,000 lines; files over 1 MiB and binaries are skipped; initial repository pushes are skipped.

### detect-secrets (Yelp) 🟡

**Current version: v1.5.0**. detect-secrets uses a **baseline-driven** workflow: you scan once, review and mark every finding as a known true or false positive, commit the baseline file, and CI only fails on new secrets introduced after the baseline.

```bash
pip install detect-secrets

# Create the initial baseline
detect-secrets scan > .secrets.baseline

# Audit and mark each finding
detect-secrets audit .secrets.baseline

# Use as pre-commit hook (via .pre-commit-config.yaml above)
# Or manually scan staged files
detect-secrets-hook --baseline .secrets.baseline $(git diff --staged --name-only)
```

---

## Signed commits

Signing commits cryptographically proves that a commit was created by the holder of a specific key. GitHub and GitLab show a green "Verified" badge on verified commits. Some organisations require signed commits via branch protection rules.

### SSH commit signing (recommended for new setups) 🟢

SSH signing requires Git **2.34+** and was not widely supported before late 2022. It is the simplest setup - you likely already have an SSH key.

**One-time setup (🪟 Windows / 🍎 Mac / 🐧 Linux):**

```bash
# Generate a key if you do not have one
ssh-keygen -t ed25519 -C "you@example.com"

# Configure Git to use it for signing
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
git config --global tag.gpgsign true
```

On **🪟 Windows PowerShell**, substitute `"$env:USERPROFILE\.ssh\id_ed25519.pub"`.

**Upload the key to GitHub:**
Settings > SSH and GPG keys > New SSH key > Key type: **Signing Key** (you need to add it as a signing key separately from your authentication key, even if it is the same key).

**Upload to GitLab:**
Settings > SSH Keys > set Usage type to **Authentication and Signing** or **Signing** only.

**Verify signatures locally:**

```bash
# Create an allowed_signers file
echo "$(git config --get user.email) namespaces=\"git\" $(cat ~/.ssh/id_ed25519.pub)" \
  >> ~/.config/git/allowed_signers

git config --global gpg.ssh.allowedSignersFile ~/.config/git/allowed_signers

# Now git log shows signature status
git log --show-signature
```

### GPG commit signing 🟡

Still widely used, especially in organisations with existing GPG infrastructure.

```bash
# Generate a key (use ECC or RSA 4096, set expiry to 1-2 years)
gpg --full-generate-key

# List keys and find your key ID
gpg --list-secret-keys --keyid-format=long

# Export public key to upload to platform
gpg --armor --export YOUR_KEY_ID

# Configure Git
git config --global user.signingkey YOUR_KEY_ID
git config --global commit.gpgsign true
```

**Common pitfalls:**

```bash
# Required on Linux and macOS terminals to fix hanging signatures
echo "export GPG_TTY=\$(tty)" >> ~/.bashrc   # or .zshrc

# Required on macOS with pinentry-mac
echo "pinentry-program $(brew --prefix)/bin/pinentry-mac" >> ~/.gnupg/gpg-agent.conf
gpgconf --kill gpg-agent

# Required on Windows - set the GPG binary path
git config --global gpg.program "C:\Program Files (x86)\GnuPG\bin\gpg.exe"
```

### GitHub Vigilant mode

Enable at Settings > SSH and GPG keys > Vigilant mode. With it on, every commit attributed to your account is labeled `Verified`, `Partially verified` or `Unverified`. Any commit not signed with your key - even if the author name and email match - shows as Unverified. This makes impersonation visible.

> [!CAUTION]
> Only enable Vigilant mode after you have configured signing everywhere you commit - CLI, IDE, CI bots. Unsigned commits from your own tools will show as Unverified once it is on.

### GitLab verified commits

GitLab supports GPG (all versions), SSH signing (GA since 15.9) and X.509 (GA since 12.8). Web UI commits are signed by the instance key (GA in GitLab 18.10).

**Require signatures (Premium/Ultimate):** Settings > Repository > Push rules > tick "Reject unsigned commits".

### Sigstore / gitsign (keyless signing) 🔴

**gitsign v0.14.0**. Keyless signing uses short-lived certificates (~10 minutes) from Sigstore's Fulcio CA, bound to an OIDC identity (GitHub account, Google account, GitHub Actions OIDC). No long-lived keys ever live on disk. Each signing event is logged to the Rekor transparency log.

```bash
# Install
brew install sigstore/tap/gitsign    # macOS
go install github.com/sigstore/gitsign@latest  # any OS

# Configure
git config --global gpg.x509.program gitsign
git config --global gpg.format x509
git config --global commit.gpgsign true

# Verify (requires OIDC identity claims)
gitsign verify \
  --certificate-identity=you@example.com \
  --certificate-oidc-issuer=https://accounts.google.com HEAD
```

> [!NOTE]
> GitHub's UI currently shows gitsign-signed commits as **Unverified** because Sigstore's root is not in GitHub's trust store. Local and CI verification works correctly. Chainguard, Red Hat Trusted Artifact Signer and Buildkite use gitsign in production.

---

## Branch protection and access control

### GitHub Rulesets (current standard) 🟡

Rulesets replaced classic branch protection as the recommended approach in 2023. They stack (multiple rules can apply to one branch), apply at the organisation level, cover the fork network for push rules and support an Evaluate mode for dry-running before enforcing.

**Create a ruleset:** Settings > Rules > Rulesets > New branch ruleset.

**Recommended production `main` ruleset:**

```
Target branches: main
Rules:
  Require a pull request:
    - Required approvals: 1
    - Require review from CODEOWNERS
    - Dismiss stale reviews on new push
  Require status checks to pass:
    - Add your CI job names (e.g. ci/build, ci/test)
    - Require branches to be up to date
  Require signed commits: on
  Block force pushes: on
  Require linear history: on (optional)
  Restrict deletions: on
Bypass list: (empty, or add your release automation GitHub App)
```

> [!IMPORTANT]
> Classic branch protection rules are **not formally deprecated** but Rulesets are the go-forward model. Classic rules cannot stack, cannot apply at the org level and do not cover the fork network. Migrate via Settings > Rules > Rulesets > New ruleset > Import from branch protection.

### GitLab protected branches and push rules 🟡

**Protected branches** (all tiers): Settings > Repository > Branch rules. Set allowed-to-merge and allowed-to-push roles. Use "No one" for push on `main` to enforce PRs.

**Push rules** (Premium/Ultimate): Settings > Repository > Push rules:

- Commit message must match regex (e.g. enforce Conventional Commits: `^(feat|fix|docs|chore|refactor|test|ci)(\(.+\))?: .+`)
- Reject unsigned commits
- Author email must match regex
- Prohibited file patterns (e.g. `\.pem$|\.key$|id_rsa`)
- Maximum file size (e.g. 100 MB)
- Prevent tag deletion

### Gitea / Forgejo branch protection 🟡

Per-repo: Settings > Branches. Set glob patterns, required approvals, required status checks, protected file patterns and whether administrators must follow the rules.

> [!NOTE]
> OSS Gitea and Forgejo have no organisation-level rule inheritance - rules must be set per repository. Gitea Enterprise adds inheritance. Forgejo v13 (October 2025) added instance-wide 2FA enforcement.

### Bitbucket branch restrictions 🟡

Repository Settings > Workflow > Branch restrictions. Key types: `push`, `force`, `delete`, `restrict_merges`, `require_approvals_to_merge`, `require_passing_builds_to_merge`, `require_tasks_to_be_completed`.

### Azure DevOps branch policies 🟡

Repos > Branches > (branch) > Branch policies. Key policies: minimum reviewers with "Prohibit the most recent pusher from approving", required build validation, comment resolution, path-based required reviewers (the ADO equivalent of CODEOWNERS).

---

## Supply chain security

### GitHub Artifact Attestations 🔴

**GA since June 2024**. Free on public repos; requires GitHub Enterprise Cloud for private repos. Achieves SLSA Build Level 2 automatically; Level 3 with reusable-workflow isolation.

```yaml
# .github/workflows/build.yml
permissions:
  id-token: write
  contents: read
  attestations: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683
      # ...build step produces ./dist/app...
      - uses: actions/attest-build-provenance@v1
        with:
          subject-path: 'dist/app'
```

**Verify:**

```bash
# Verify a binary
gh attestation verify dist/app -o myorg

# Verify a container image
gh attestation verify oci://ghcr.io/org/img:tag -R org/repo
```

### SLSA framework 🔴

SLSA (Supply-chain Levels for Software Artifacts, pronounced "salsa") defines levels of supply chain integrity:

- **L1** - provenance generated (may be unsigned)
- **L2** - hosted build platform, signed provenance
- **L3** - hardened platform with isolation (signing material inaccessible to user build steps)

Use **slsa-github-generator** for language-agnostic L3 provenance on GitHub Actions. Upgrade past v1.10.0 (earlier versions had a provenance-generation bug).

```yaml
# Use the slsa-github-generator for Build L3
jobs:
  build:
    outputs:
      hashes: ${{ steps.hash.outputs.hashes }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683
      - run: make build
      - id: hash
        run: |
          sha256sum dist/* | base64 -w0
          echo "hashes=$(sha256sum dist/* | base64 -w0)" >> $GITHUB_OUTPUT

  provenance:
    needs: [build]
    permissions: { actions: read, id-token: write, contents: write }
    uses: slsa-framework/slsa-github-generator/.github/workflows/generator_generic_slsa3.yml@v2.0.0
    with:
      base64-subjects: "${{ needs.build.outputs.hashes }}"
```

### Sigstore cosign 🔴

**Current version: v3.0.3**. Sign and verify container images and blobs using Sigstore's transparency log.

> [!IMPORTANT]
> Cosign v3 broke several flags from pre-2.0 tutorials. `COSIGN_EXPERIMENTAL=1` is gone (keyless is now default). `--certificate-email` is removed (use `--certificate-identity` + `--certificate-oidc-issuer`). The `--bundle` flag is now required for blob signing.

```bash
# Install
brew install cosign                   # macOS
choco install cosign                  # Windows
go install github.com/sigstore/cosign/v3/cmd/cosign@latest

# Sign a container image (keyless, in CI with OIDC)
cosign sign ghcr.io/org/image:tag

# Verify
cosign verify ghcr.io/org/image:tag \
  --certificate-identity=https://github.com/org/repo/.github/workflows/release.yml@refs/heads/main \
  --certificate-oidc-issuer=https://token.actions.githubusercontent.com

# Sign a file (blob)
cosign sign-blob dist/app --bundle dist/app.sigstore.json

# Verify a file
cosign verify-blob dist/app --bundle dist/app.sigstore.json \
  --certificate-identity=you@example.com \
  --certificate-oidc-issuer=https://accounts.google.com
```

### npm provenance and Trusted Publishing 🟡

Since July 2025, npm **Trusted Publishing** is GA. With OIDC-based publishing, no long-lived npm token is stored anywhere. Provenance is generated automatically.

```yaml
# .github/workflows/publish.yml
permissions:
  id-token: write
  contents: read

steps:
  - uses: actions/setup-node@v4
    with:
      node-version: '22'
      registry-url: 'https://registry.npmjs.org'
  - run: npm ci && npm publish --access public
    # No NPM_TOKEN needed - OIDC handles it
```

Consumers verify: `npm audit signatures`.

### Dependabot 🟢

Dependabot keeps dependencies current and alerts on known CVEs. Key 2025 addition: **cooldown** delays fresh package versions by a configurable number of days, defending against packages that are malicious at publish time but cleaned up hours later.

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule: { interval: "weekly" }
    cooldown:
      default-days: 7
      semver-major-days: 30
      semver-patch-days: 1
    groups:
      dev-dependencies:
        patterns: ["*"]
        update-types: ["minor", "patch"]
        dependency-type: "development"

  - package-ecosystem: "github-actions"
    directory: "/"
    schedule: { interval: "weekly" }
```

> [!NOTE]
> Dependabot has no built-in auto-merge UI toggle (old tutorials showing one are outdated). Use the `gh` CLI in a workflow triggered by `pull_request` events from the `dependabot[bot]` actor to auto-merge patch/minor updates.

---

## Credential and secret management

### .gitignore for secrets 🟢

These patterns belong in every project's `.gitignore`:

```gitignore
# Environment files
.env
.env.*
!.env.example    # keep the example file

# Keys and certificates
*.pem
*.key
*.crt
*.p12
*.pfx
id_rsa
id_ed25519
*.ppk

# Cloud credentials
.aws/credentials
kubeconfig
.kube/config
service-account*.json
*.tfvars
terraform.tfstate*

# Application secrets
config/master.key         # Rails
config/credentials/*.key  # Rails
gradle.properties
.netrc
.npmrc
.pypirc
.docker/config.json
```

Set a global gitignore for machine/IDE-specific files:

```bash
# 🍎 Mac / 🐧 Linux
git config --global core.excludesfile ~/.config/git/ignore

# Add to that file
echo ".DS_Store\nThumbs.db\n.idea/\n.vscode/\n*.swp\n.env.local" \
  >> ~/.config/git/ignore
```

### Git credential helpers 🟢

Use a secure credential helper - never `credential.helper=store` (stores passwords in plaintext).

```bash
# 🪟 Windows - Git Credential Manager (GCM, bundled with Git for Windows)
git config --global credential.helper manager

# 🍎 Mac - macOS Keychain
git config --global credential.helper osxkeychain
# Or GCM for multi-account / Azure DevOps support:
brew install --cask git-credential-manager
git-credential-manager configure

# 🐧 Linux - libsecret (GNOME Keyring)
# Build or install git-credential-libsecret, then:
git config --global credential.helper /usr/lib/git-core/git-credential-libsecret
# Or use GCM:
dotnet tool install -g git-credential-manager
git-credential-manager configure
```

**Git Credential Manager (GCM) current version: 2.7.3** (March 2026). Supports GitHub, GitLab, Bitbucket and Azure DevOps with OAuth, MFA and FIDO2 passkeys.

### SSH key best practices 🟢

```bash
# Always use ed25519 (faster, smaller, no "is 2048 bits enough?" debate)
ssh-keygen -t ed25519 -C "you@example.com"

# Always use a passphrase
# Cache the passphrase with ssh-agent

# macOS: add to Keychain so the passphrase persists across reboots
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
# Add to ~/.ssh/config:
echo "Host *\n  UseKeychain yes\n  AddKeysToAgent yes" >> ~/.ssh/config

# Hardware-backed keys (YubiKey, etc.) - OpenSSH 8.2+
ssh-keygen -t ed25519-sk -O resident -O verify-required -C "you@example.com"
# Works with GitHub, GitLab and Bitbucket
```

**Rotation:** check "last used" timestamps per key on GitHub (Settings > SSH and GPG keys). Prune keys that have not been used in 90 days. Rotate CI deploy keys at each quarterly credential review.

### Deploy keys vs PATs vs GitHub Apps 🟡

```
One CI server clones one repo (read)          → Deploy key (read-only)
CI pushes releases to one repo                → Deploy key (write) or GitHub App
Personal CLI / local scripts                  → Fine-grained PAT, short TTL
Bot opening PRs across many repos             → GitHub App
Multi-tenant SaaS acting on customer repos    → GitHub App
Action needing token in another repo          → actions/create-github-app-token@v1
Publishing packages (GITHUB_TOKEN insufficient) → GitHub App token
```

**Fine-grained PATs** (GA March 2025) are scoped to specific repos and permissions with mandatory expiry (max 366 days). Classic PATs should be retired.

### SOPS with age encryption 🔴

**SOPS v3.12.2** (moved from mozilla/sops to getsops/sops). `age` is the preferred key type - X25519-based, tiny public keys, no GPG-agent state.

```bash
# Install
brew install sops age

# Generate an age key
age-keygen -o ~/.config/sops/age/keys.txt
export SOPS_AGE_KEY_FILE=~/.config/sops/age/keys.txt
```

**`.sops.yaml` creation rules:**

```yaml
creation_rules:
  - path_regex: secrets/dev/.*\.yaml$
    encrypted_regex: '^(data|stringData)$'
    age: age1ql3z7hjy54pw3pywairh23suapme6h37yjgl2...

  - path_regex: secrets/prod/.*\.yaml$
    kms: 'arn:aws:kms:us-west-2:111122223333:key/abc'
    age: age129h70qwxnjsscal...
```

```bash
# Edit secrets inline (decrypt, open in editor, re-encrypt on save)
sops edit secrets.yaml

# Decrypt to stdout
sops -d secrets.yaml

# Add a new recipient, re-wrap all keys
sops updatekeys -i secrets.yaml

# Rotate the data encryption key
sops rotate -i secrets.yaml
```

---

## Security in IDEs

### VS Code extensions 🟢

Install all three:

- **GitGuardian** (`gitguardian-secret-security.gitguardian`) - detects 500+ secret types on file save, 10,000 API calls/month free
- **Snyk Security** (`snyk-security.snyk-vulnerability-scanner`) - SAST, SCA, IaC and secret detection; free for individuals
- **SonarQube for IDE** (formerly SonarLint) - 340+ secret rules, 248 cloud service patterns, under 5% false-positive rate, works standalone

**GitLens 17.12** adds commit signature verification inline in the editor. The `.aiignore` and `.cursorignore` files prevent sensitive files from being sent to AI assistants.

### JetBrains 🟡

**Qodana's HardcodedPasswords inspection** (bundled in IntelliJ IDEA Ultimate via the Security Analysis by Qodana plugin since IDEA 2024.3.1) detects hardcoded credentials in JS/TS, Java, Kotlin, Python, Go, PHP, C#, JSON, YAML and XML. Enable at Settings > Editor > Inspections > Security > Hardcoded passwords.

**Package Checker** (bundled in IDEA Ultimate) flags vulnerable Maven/Gradle dependencies inline.

**Wiz plugin for JetBrains** (GA 2025) - secrets, IaC scanning and vulnerable package detection with one-click fixes.

### Pre-commit hooks in the IDE 🟢

VS Code runs `.git/hooks/pre-commit` automatically on commit from the Source Control panel. JetBrains runs hooks from the "Before Commit" section of the Commit dialog.

For Node.js projects, use **husky v9**:

```bash
npm install -D husky
npx husky init
# .husky/pre-commit is created and the prepare script is wired
```

Combine with **lint-staged** to run only on staged files:

```json
// package.json
{
  "lint-staged": {
    "*.{js,ts}": ["eslint --fix", "prettier --write"],
    "*": "gitleaks protect --staged"
  }
}
```

---

## Security in the terminal

### Preventing secrets in shell history 🟢

```bash
# Bash: ignore commands starting with a space and duplicates
echo 'export HISTCONTROL=ignoreboth' >> ~/.bashrc
echo "export HISTIGNORE='export *KEY*:export *TOKEN*:export *SECRET*:* --password=*'" \
  >> ~/.bashrc

# Zsh: equivalent settings
echo 'setopt HIST_IGNORE_SPACE HIST_IGNORE_ALL_DUPS HIST_NO_STORE' >> ~/.zshrc

# Fish: ignores leading-space commands by default
# Remove a specific command from history:
fish -c "history delete --contains 'my_secret_token'"

# Prefix any command with a space to suppress it from history (all shells):
 export API_KEY=my-secret-value   # space at the start
```

### Using environment variables safely 🟢

Never export secrets in a way that persists in your shell history or process list. Use `direnv` or a secret manager CLI instead:

```bash
# direnv: per-directory .envrc, requires explicit 'direnv allow'
# on each change - prevents auto-loading malicious .envrc files
echo "export API_KEY=value" > .envrc
direnv allow                    # must run manually each time .envrc changes

# 1Password CLI: inject secrets into a child process for one command only
op run --env-file=.env -- ./deploy.sh

# Read a single secret into a variable without shell history
API_KEY=$(op read "op://vault/item/field")
```

### Safe git config practices 🟡

```bash
# Prevent Git from guessing your identity (causes commits with wrong email)
git config --global user.useConfigOnly true

# Conditional includes for work/personal separation
cat >> ~/.gitconfig << 'EOF'
[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work

[includeIf "hasconfig:remote.*.url:git@gitlab.company.com:*/**"]
    path = ~/.gitconfig-work
EOF

# Harden object transfer integrity checking
git config --global transfer.fsckObjects true
git config --global fetch.fsckObjects true
git config --global receive.fsckObjects true

# Protect credential protocol (guards against CVE-2024-52006)
git config --global credential.protectProtocol true
```

> [!WARNING]
> Keep Git up to date. Git has had several credential-related CVEs in 2024-2025: CVE-2024-50338 and CVE-2024-52006 (credential protocol), CVE-2025-23040 (GitHub Desktop), CVE-2025-66413 (Git for Windows NTLM). Git 2.48+ includes the carriage-return protections. Update regularly on all platforms.

---

## Platform security features

| Feature | GitHub | GitLab | Gitea/Forgejo | Bitbucket | Azure DevOps |
|---|---|---|---|---|---|
| Branch protection | Rulesets (stacking, org-wide) | Branch rules + push rules | Per-repo (Enterprise adds inheritance) | Branch restrictions + project defaults | Branch policies |
| Require signed commits | Ruleset rule | Push rule (Premium+) | Ruleset option | Hooks only | Not native |
| Secret scanning | Free on public repos | Pipeline (all tiers); push protection (Ultimate) | None (use Actions) | Snyk add-on | Via GHAzDO |
| SAST | CodeQL (public free / Code Security $30 private) | Semgrep (all); Advanced SAST (Ultimate) | None | Snyk | CodeQL via GHAzDO |
| Dependency scanning | Dependabot (free) | Gemnasium (Ultimate UI) | Enterprise | Snyk built-in | GHAzDO |
| Push protection | Default on public repos; Secret Protection $19 private | Ultimate GA 17.5 | None | None | GHAzDO |
| OIDC for CI | GitHub Actions OIDC | GitLab CI OIDC | Via Actions | Pipelines OIDC | Federated workload identity |

### GitHub security features 🟡

Free on all public repositories: Dependabot, secret scanning, push protection, CodeQL, dependency review action.

For private repositories, GitHub split its Advanced Security bundle in April 2025:

- **GitHub Secret Protection** ($19/active committer/month) - secret scanning, push protection, validity checks
- **GitHub Code Security** ($30/active committer/month) - CodeQL, dependency review, code scanning

Both tiers are now available on GitHub Team (not just Enterprise).

### GitLab security features by tier 🟡

All tiers get SAST, dependency scanning, container scanning, IaC scanning and pipeline secret detection as CI jobs (JSON artifacts). The **Vulnerability Report UI, MR security widget, security dashboard, approval policies on vulnerabilities, Advanced SAST, secret push protection and DAST** are Ultimate-only.

### Gitea and Forgejo security 🟡

OSS Gitea and Forgejo have no built-in SAST, DAST or secret scanning. Run scanners via Gitea Actions / Forgejo Actions (GitHub Actions-compatible). Gitea Enterprise adds dependency scanning and branch protection inheritance.

**Forgejo v13 (October 2025)** added instance-wide 2FA enforcement and hardened Actions secrets encryption.

### Azure DevOps Advanced Security 🔴

GA since October 2023, cloud-only (no ADO Server support). Pricing matches GitHub: Secret Protection $19 + Code Security $30 per active committer. Pipeline tasks: `AdvancedSecurity-Codeql-Init@1` and `AdvancedSecurity-Codeql-Analyze@1`. Secret push blocking is default-on when GHAzDO is enabled.

### Bitbucket security 🟡

**Snyk Security for Bitbucket Cloud** (free Atlassian Marketplace app) provides dependency scanning, container scanning, licence compliance and auto-fix PRs via Code Insights. Bitbucket Pipelines supports OIDC federation to AWS, GCP and Azure (no stored cloud credentials) and deployment approvals.

> [!CAUTION]
> Atlassian is deprecating Bitbucket **app passwords on June 9, 2026**. Migrate all integrations to API tokens before that date.

---

## Real-world incidents and lessons

### tj-actions/changed-files compromise (CVE-2025-30066, March 2025) 🔴

Attackers retroactively moved every tag (v1 through v45.0.7) of `tj-actions/changed-files` to a single malicious commit that dumped runner process memory - including secrets - into public workflow logs. The initial pivot was a stolen PAT from a related action (`reviewdog/action-setup`). Roughly 23,000 consumer repositories were affected. Repositories pinned to a full commit SHA were unaffected.

**Lessons:**
- Pin every GitHub Action by full commit SHA, never by tag
- Use `GITHUB_TOKEN` with minimal `permissions:` blocks
- Add `gitleaks` or `trufflehog` scans so leaked secrets trigger alerts
- Use Harden-Runner-style egress filtering to prevent exfiltration even if a workflow is compromised

### s1ngularity / Nx supply chain attack (August 2025) 🔴

Malicious versions of `nx` (20.9.0-20.12.0 and 21.5.0-21.8.0) contained a `postinstall` script that harvested GitHub PATs, npm tokens, SSH keys, `.env`, `.npmrc` and crypto wallets, then pushed them to public repositories using the stolen tokens. About 5,500 private repositories were flipped public. The initial attack vector was a vulnerable `pull_request_target` workflow that exfiltrated the npm publish token.

**Lessons:**
- Use OIDC Trusted Publishing for npm - no long-lived token to steal
- Never combine `pull_request_target` with a checkout of the PR's code
- Use `npm ci --ignore-scripts` in CI to prevent `postinstall` attacks
- Dependabot cooldown (introduced July 2025) would have delayed these malicious versions

### xz-utils backdoor (CVE-2024-3094, March 2024) 🔴

A multi-year social engineering campaign resulted in a CVSS 10.0 sshd backdoor injected via tarball-only files invisible to Git history. The attacker built trust over ~2.6 years and ~750 legitimate contributions.

**Lessons:**
- Reproducible builds from Git (not tarballs) make tarball-only injections detectable
- Diverse maintainer pools and scepticism toward urgent pressure to merge
- Scrutiny on binary test fixtures and build system files

### Internet Archive credential theft (October 2024) 🔴

A plaintext GitLab authentication token was exposed on a public developer host for approximately 22 months. After the initial breach, the attacker used a Zendesk token found in the downloaded source to exfiltrate 800,000 support tickets 11 days later.

**Lessons:**
- Pre-commit secret scanning and push protection on self-hosted forges
- Mandatory rotation on any suspected exposure - do not wait for confirmation
- Audit all tokens, not just the one that was obviously compromised

### Sisense credential theft (April 2024) 🔴

A hardcoded AWS token in a self-managed GitLab repository led to terabytes of customer data being exfiltrated from unencrypted S3 buckets.

**Lessons:**
- OIDC/IAM roles instead of long-lived cloud credentials
- Secret scanning enabled on self-hosted Git instances
- Encrypt S3 buckets and other storage at rest

---

## The five controls that prevent most incidents

Applied together, these five controls would have fully prevented or dramatically contained every incident described above.

**1. Pin every Action and CI dependency by commit SHA, not tag.**

```yaml
# Wrong
- uses: actions/checkout@v4

# Correct
- uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683
```

Use Dependabot with `package-ecosystem: "github-actions"` to keep SHA pins current automatically.

**2. Eliminate long-lived publish tokens with OIDC.**

Replace npm tokens, PyPI tokens, and cloud provider credentials with OIDC Trusted Publishing or short-lived tokens from GitHub Apps. A stolen token with a TTL of 15 minutes cannot be used the next day.

**3. Protect the push with gitleaks and platform push protection.**

Add a `gitleaks protect --staged` pre-commit hook for every developer. Enable GitHub push protection on all repositories. Add `gitleaks git .` to CI. These three together catch secrets at three different points.

**4. Sign every commit.**

Two commands and a key upload. SSH signing on Git 2.34+ is the simplest path. Enable Vigilant mode on GitHub or "Reject unsigned commits" on GitLab Premium to enforce it.

**5. Produce and verify build provenance.**

One GitHub Actions step with `actions/attest-build-provenance@v1` gives SLSA Build Level 2. Verify at deploy time with `gh attestation verify`. This makes silent modification of build artifacts detectable.

---

## Try it yourself

### Exercise 1: set up the full secret detection stack

```bash
# In any Git repository
pip install pre-commit gitleaks

cat > .pre-commit-config.yaml << 'EOF'
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.30.0
    hooks:
      - id: gitleaks
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: detect-private-key
      - id: check-added-large-files
        args: ['--maxkb=500']
EOF

pre-commit install
pre-commit run --all-files

# Test: try to commit a fake secret
echo 'API_KEY=AKIAIOSFODNN7EXAMPLE' >> test-secret.txt
git add test-secret.txt
git commit -m "test: should be blocked"
# gitleaks should block this commit
```

### Exercise 2: set up SSH commit signing

```bash
# Generate a key if you do not have one
ssh-keygen -t ed25519 -C "you@example.com"

# Configure signing
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true

# Set up local verification
echo "$(git config user.email) namespaces=\"git\" $(cat ~/.ssh/id_ed25519.pub)" \
  >> ~/.config/git/allowed_signers
git config --global gpg.ssh.allowedSignersFile ~/.config/git/allowed_signers

# Make a signed commit and verify it
git commit --allow-empty -m "test: first signed commit"
git log --show-signature -1
```

---

## Common mistakes

**Relying on a single detection layer.** Push protection catches known token formats. gitleaks catches regex patterns. Neither catches custom internal token formats, high-entropy strings or secrets embedded in images. Use all four layers together.

**Not rotating after a suspected exposure.** Rewriting history does not un-compromise a secret. If a secret was in a pushed commit for any length of time, treat it as compromised and rotate it immediately, regardless of whether you are sure it was seen.

**Using `git filter-branch` to remove secrets.** `git filter-branch` is officially deprecated and significantly slower than `git filter-repo`. Its own man page redirects to filter-repo. Always use `git filter-repo` v2.47+.

**Pinning Actions by tag.** The `tj-actions` incident (March 2025) demonstrated conclusively that tags are mutable. Pin by SHA. Dependabot keeps SHA pins current.

**Storing long-lived cloud credentials in CI secrets.** OIDC Trusted Publishing for npm, PyPI and Docker registries; GitHub Actions OIDC for AWS, GCP and Azure; GitHub Apps for cross-repo tokens. Long-lived tokens are the root cause of most supply chain compromises.

**Disabling `--ignore-scripts` in npm CI.** Never run `npm install` in CI - always run `npm ci --ignore-scripts`. The s1ngularity attack worked via `postinstall` scripts. Ignoring scripts prevents this entire class of attack.

**Committing with the wrong GPG email.** The most common cause of a GPG "Unverified" badge: the key UID email does not exactly match `git config user.email` and/or is not a verified address on the platform. Verify both before enabling Vigilant mode.

---

## Summary

Security in version control is a layered problem. The first line of defence is the developer's machine: a pre-commit hook with gitleaks or detect-secrets, proper `.gitignore` patterns, a secure credential helper and SSH commit signing. The second line is the push: GitHub push protection or GitLab secret push protection, Rulesets or protected branches requiring CI and approvals. The third line is the supply chain: OIDC Trusted Publishing, Dependabot with cooldown, SLSA build provenance and cosign verification.

The incidents of 2024-2025 - tj-actions, s1ngularity, xz-utils, Internet Archive, Sisense - share a common thread: long-lived credentials or mutable build inputs. The five controls at the end of this file eliminate both. They take less than an hour to set up and require almost no ongoing maintenance.

---

## Sources and Further Reading

- [GitHub: about push protection](https://docs.github.com/en/code-security/secret-scanning/push-protection-for-repositories-and-organizations)
- [GitHub: about secret scanning](https://docs.github.com/en/code-security/secret-scanning/about-secret-scanning)
- [GitLab: secret push protection](https://docs.gitlab.com/user/application_security/secret_detection/secret_push_protection/)
- [gitleaks](https://github.com/gitleaks/gitleaks)
- [TruffleHog](https://github.com/trufflesecurity/trufflehog)
- [pre-commit framework](https://pre-commit.com/)
- [GitHub: commit signature verification](https://docs.github.com/en/authentication/managing-commit-signature-verification/about-commit-signature-verification)
- [Sigstore documentation](https://docs.sigstore.dev/)
- [gitsign](https://github.com/sigstore/gitsign)
- [GitHub: artifact attestations](https://docs.github.com/en/actions/security-for-github-actions/using-artifact-attestations)
- [SLSA framework](https://slsa.dev/)
- [Cosign documentation](https://docs.sigstore.dev/cosign/)
- [npm Trusted Publishing](https://docs.npmjs.com/generating-provenance-statements)
- [SOPS documentation](https://github.com/getsops/sops)
- [Git Credential Manager](https://github.com/git-ecosystem/git-credential-manager)
- [GitHub: about rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets)
- [OpenSSF: tj-actions CVE-2025-30066 analysis](https://openssf.org/blog/2025/03/18/cve-2025-30066-tj-actions-changed-files-action-is-compromised/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
