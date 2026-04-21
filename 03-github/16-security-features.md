# Security Features

**Difficulty:** 🟡 Intermediate to 🔴 Advanced | **Time:** 30 minutes

GitHub has a comprehensive suite of security tools built directly into the platform. They automatically scan your code for vulnerabilities, detect secrets that should never have been committed, keep your dependencies up to date and provide a structured process for disclosing and fixing security issues. Most of these tools are free for public repositories and require no external service. This file covers every security feature GitHub offers, what each does and how to configure it.

---

## Table of Contents

- [GitHub security features overview](#github-security-features-overview)
- [Dependency graph](#dependency-graph)
- [Dependabot alerts](#dependabot-alerts)
- [Dependabot security updates](#dependabot-security-updates)
- [Dependabot version updates](#dependabot-version-updates)
- [Secret scanning](#secret-scanning)
- [Push protection](#push-protection)
- [Code scanning and CodeQL](#code-scanning-and-codeql)
- [Copilot Autofix](#copilot-autofix)
- [Security advisories](#security-advisories)
- [Private vulnerability reporting](#private-vulnerability-reporting)
- [Security policies](#security-policies)
- [GitHub Advanced Security (GHAS)](#github-advanced-security-ghas)
- [Security overview (organisations)](#security-overview-organisations)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## GitHub Security Features Overview

GitHub's security features fall into two categories:

**Free for all repositories (public and private):**

- Dependency graph
- Dependabot alerts
- Dependabot security updates
- Security advisories (public repos)
- Private vulnerability reporting

**Free for public repositories only:**

- Secret scanning and push protection
- Code scanning (CodeQL)
- Copilot Autofix for code scanning alerts
- Dependency review

**Paid (GitHub Advanced Security - requires Team or Enterprise plan):**

- Secret scanning and push protection for **private** repositories
- Code scanning for **private** repositories
- Copilot Autofix for **private** repositories
- Custom secret scanning patterns
- AI-powered generic password detection
- Security campaigns
- Premium Dependabot auto-triage rules

**GHAS restructure (April 2025):**

GitHub Advanced Security was split into two separately purchasable products:
- **GitHub Secret Protection** - $19/month per active committer. Covers secret scanning, push protection, AI detection and custom patterns.
- **GitHub Code Security** - $30/month per active committer. Covers CodeQL, Copilot Autofix, dependency review and security campaigns.

Both are now available to Team plan customers (previously Enterprise only).

---

## Dependency Graph

The dependency graph automatically detects and maps all dependencies in your repository based on manifest files.

**Supported ecosystems:**

npm (`package.json`), pip (`requirements.txt`, `setup.py`, `Pipfile`), Maven (`pom.xml`), Gradle (`build.gradle`), Bundler (`Gemfile`), Cargo (`Cargo.toml`), Go modules (`go.mod`), NuGet (`.csproj`), Composer (`composer.json`), Swift (`Package.swift`), pub (`pubspec.yaml`), Actions (`.github/workflows/*.yml`) and more.

**Viewing the dependency graph:**

Repository → Insights → Dependency graph.

Shows:
- **Dependencies** - all packages your project depends on, with version and licence
- **Dependents** - repositories that depend on your project (for libraries)

**Enable/disable:**

Settings → Security → Dependency graph (enabled by default on public repos).

The dependency graph is the foundation that all Dependabot features are built on.

---

## Dependabot Alerts

Dependabot alerts notify you when a dependency in your repository has a known security vulnerability. GitHub compares your dependency graph against the **GitHub Advisory Database** - a curated database of security vulnerabilities from CVE, GHSA and other sources.

**How alerts work:**

1. A new vulnerability is added to the GitHub Advisory Database
2. GitHub checks all repositories using the affected dependency and version
3. Repository administrators receive an alert notification
4. The alert appears in the repository's Security tab → Dependabot alerts

**What an alert shows:**

- The vulnerable dependency and affected versions
- The vulnerability's severity (Critical, High, Medium, Low)
- CVSS score
- A description of the vulnerability
- The patched version to upgrade to
- Links to the CVE and advisory

**Enabling Dependabot alerts:**

Settings → Security → Dependabot alerts → Enable.

Enabled automatically on all public repositories. Repository administrators receive email and web notifications.

**Alert states:**

- **Open** - vulnerability is present and not yet fixed
- **Fixed** - you updated the dependency to a safe version
- **Dismissed** - manually dismissed with a reason (tolerable risk, false positive, no patch available, code not used in production)
- **Auto-dismissed** - automatically dismissed when Dependabot determines the vulnerability path is not reachable in your code

**Alert notifications:**

Alerts notify: repository owners, security managers, anyone with repository admin access and anyone who has enabled security alert notifications in their notification settings.

---

## Dependabot Security Updates

Dependabot security updates automatically create pull requests to update vulnerable dependencies to the minimum safe version.

**How it works:**

1. A Dependabot alert is generated for a vulnerability
2. If a fixed version exists, Dependabot opens a pull request automatically
3. The PR updates only the vulnerable dependency, not all dependencies
4. You review, test and merge the PR

**Enable:**

Settings → Security → Dependabot security updates → Enable.

**What security updates cover:**

Only vulnerabilities with a known fix. If no patched version exists, no PR is created (the alert remains open until a fix is available).

**Dependency compatibility:**

Dependabot checks compatibility scores - how likely an update is to break things based on data from other repositories that made the same update. High compatibility scores appear in the PR.

---

## Dependabot Version Updates

Dependabot version updates keep all your dependencies current even when there is no known security issue. These are scheduled PRs that update dependencies to their latest versions.

**Configuration file:**

Create `.github/dependabot.yml`:

```yaml
version: 2
updates:
  # Keep npm packages up to date
  - package-ecosystem: "npm"
    directory: "/"              # location of package.json
    schedule:
      interval: "weekly"        # daily, weekly, or monthly
      day: "monday"
      time: "09:00"
      timezone: "Europe/London"
    open-pull-requests-limit: 10
    labels:
      - "dependencies"
      - "automated"
    assignees:
      - "YOUR_USERNAME"
    reviewers:
      - "YOUR_USERNAME"

  # Keep GitHub Actions up to date
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
    groups:
      actions:                  # group all Actions updates into one PR
        patterns:
          - "*"

  # Keep Docker base images up to date
  - package-ecosystem: "docker"
    directory: "/"
    schedule:
      interval: "monthly"
```

**Grouping updates:**

Use `groups:` to batch multiple dependency updates into a single PR instead of one PR per package. Reduces noise significantly:

```yaml
groups:
  production-dependencies:
    dependency-type: "production"
  development-dependencies:
    dependency-type: "development"
```

**Ignoring specific packages:**

```yaml
ignore:
  - dependency-name: "some-package"
    versions: ["1.x", "2.x"]
  - dependency-name: "another-package"
    update-types: ["version-update:semver-major"]
```

**Supported ecosystems:**

`bundler`, `cargo`, `composer`, `docker`, `elm`, `github-actions`, `gitsubmodule`, `gomod`, `gradle`, `maven`, `mix`, `npm`, `nuget`, `pip`, `pub`, `swift`, `terraform`.

---

## Secret Scanning

Secret scanning detects known secret formats that have been committed to your repository. GitHub partnered with 200+ service providers (AWS, Azure, Google Cloud, Stripe, Slack, npm, GitHub itself, and many more) to detect their specific credential formats.

**What it scans:**

- All commits pushed to the repository
- Issues, pull requests, discussions and comments
- Repository descriptions and READMEs
- The full Git history (including content before enabling the feature)

**When a secret is detected:**

- Repository administrators receive an email alert
- The alert appears in Security → Secret scanning alerts
- If push protection is enabled, the push is blocked before the secret reaches the repository

**Alert information:**

- Which type of secret was found (AWS access key, GitHub PAT, Stripe API key etc.)
- Where it was found (file, commit, issue body)
- Whether the secret is still valid (validity checks where the provider supports it)

**Validity checks:**

GitHub periodically checks with partner service providers whether detected secrets are still active. An active secret is more urgent than an expired one. This is an opt-in feature as it shares the token with the provider for validation.

**AI-powered generic password detection (GitHub Secret Protection only):**

Beyond known patterns, GitHub uses Copilot AI to detect generic passwords, unstructured credentials and credentials without a fixed format. Available since November 2025 with push protection support.

---

## Push Protection

Push protection blocks pushes containing detected secrets before they reach the repository. It is the most important secret scanning feature - preventing leaks rather than detecting them after the fact.

**How it works:**

When you push a commit containing a recognised secret:

```
remote: error: Push cannot contain secrets.
remote:
remote: GitHub Secret scanning found a GitHub Personal Access Token.
remote: Location: path/to/file.txt, line 12
remote:
remote: To push, you must remove the detected secret, or approve this
remote: push as a false positive.
remote:
remote: Please visit https://github.com/OWNER/REPO/security/secret-scanning/...
remote: to review and bypass this protection.
```

The push is rejected. You must either:
1. Remove the secret from the commit (and rewrite history with `git filter-repo`)
2. Bypass the protection with a stated reason (false positive, used in tests, will rotate immediately)

**Enable push protection:**

Settings → Security → Secret scanning → Push protection → Enable.

**Bypassing push protection:**

Click the link in the error message to open a bypass form on GitHub. Select a reason:
- **It's a false positive** - not actually a secret
- **It's used in tests** - test credential, not real
- **I'll fix it later** - you acknowledge the risk and will rotate immediately

Bypasses are logged in the audit log. Organisation admins can configure **delegated bypass** - requiring approval from a designated reviewer before a bypass is allowed.

**Bypass via CLI:**

```bash
git push --no-verify   # bypasses ALL hooks and protection
```

> [!WARNING]
> `--no-verify` bypasses push protection entirely without logging a reason. Avoid using it as a shortcut for push protection bypass. Use the web form instead to maintain a proper audit trail.

---

## Code Scanning and CodeQL

Code scanning automatically finds security vulnerabilities in your source code by analysing it semantically - treating code as data and running queries against it.

**CodeQL** is GitHub's semantic analysis engine. It understands the structure of your code (not just its text) and can trace data flow, find injection vulnerabilities, detect unsafe deserialization and identify hundreds of other security issues.

**Supported languages (as of 2026):**

C, C++, C#, Go, Java, JavaScript, TypeScript, Kotlin, Python, Ruby, Swift, Rust (GA October 2025), GitHub Actions workflows.

**Three setup modes:**

**Default setup (recommended):** One click. GitHub analyses your code and selects the appropriate CodeQL configuration automatically. Go to Settings → Security → Code scanning → Set up → Default.

**Advanced setup:** Full control via a workflow file. Useful when you need custom queries, specific CodeQL versions or integration with other tools.

```yaml
name: CodeQL Analysis

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '30 1 * * 0'  # weekly scan

jobs:
  analyse:
    name: Analyse ${{ matrix.language }}
    runs-on: ubuntu-latest

    permissions:
      security-events: write
      actions: read
      contents: read

    strategy:
      matrix:
        language: [javascript-typescript, python]

    steps:
      - uses: actions/checkout@v4

      - uses: github/codeql-action/init@v3
        with:
          languages: ${{ matrix.language }}
          queries: security-extended  # or security-and-quality

      - uses: github/codeql-action/autobuild@v3

      - uses: github/codeql-action/analyze@v3
        with:
          category: "/language:${{ matrix.language }}"
```

**Third-party code scanning:** Upload SARIF (Static Analysis Results Interchange Format) files from other tools (Semgrep, SonarQube, Snyk, ESLint, etc.) to show their results alongside CodeQL in the same interface.

**Alert severity levels:**

Critical, High, Medium, Low, Note, Warning, Error.

**Alert states:**

Open, Dismissed (with a reason), Fixed (automatically closed when code changes remove the vulnerability).

**Query suites:**

- `security-extended` - all security queries (more coverage, more false positives)
- `security-and-quality` - security plus code quality queries

---

## Copilot Autofix

Copilot Autofix generates AI-powered fix suggestions for code scanning alerts. For each alert, it proposes a concrete code change with an explanation.

**How it works:**

1. Code scanning finds a vulnerability
2. Copilot analyses the vulnerable code and the alert details
3. Copilot generates a suggested fix as a code diff
4. You review the fix and apply it (or modify it)

**Coverage:** Over 90% of alert types in JavaScript/TypeScript, Java and Python as of 2026. More languages being added.

**Applying a fix:**

Security → Code scanning → open an alert → view the Autofix suggestion → **Create PR with fix** (creates a pull request with the fix) or **Apply fix** (applies directly).

**Free for public repositories.** Requires GitHub Code Security for private repositories.

---

## Security Advisories

A security advisory is a formal notification of a vulnerability in your project. GitHub is a **CVE Numbering Authority (CNA)** - you can request a CVE ID directly from a repository advisory.

**When to create an advisory:**

When you discover or are notified of a security vulnerability in your project. The advisory allows you to privately discuss and coordinate the fix before making it public.

**Creating an advisory:**

Repository → Security → Advisories → New draft security advisory.

Fill in:
- **Title** - brief description
- **CVE identifier** - leave blank to request one from GitHub, or add an existing CVE
- **Description** - full details of the vulnerability
- **Affected packages** - which package and versions are affected
- **Severity** - calculated from CVSS vector or manually set
- **Weaknesses (CWE)** - Common Weakness Enumeration classification

**Private advisory workflow:**

1. Create a draft advisory (private to repository collaborators)
2. Optionally add private forks of collaborators to coordinate a fix
3. Develop and test the fix privately
4. Request a CVE ID if needed
5. Publish the advisory when the fix is released

**Published advisory effects:**

- Appears in the **GitHub Advisory Database**
- Triggers Dependabot alerts for all repositories using the affected package and version
- Appears on the npm, RubyGems or other registry page for the affected package

---

## Private Vulnerability Reporting

Private vulnerability reporting gives security researchers a safe, structured way to report vulnerabilities directly to you without making them public.

**Enable:**

Settings → Security → Private vulnerability reporting → Enable.

When enabled, a **Report a vulnerability** button appears on the repository's Security tab.

**How reporters use it:**

1. Go to the repository → Security → **Report a vulnerability**
2. Fill in the vulnerability report form (similar to an advisory)
3. Submit - only repository maintainers can see it

**What you see:**

Security → Advisories → a new private draft advisory with the reporter's details. You can collaborate with the reporter privately, develop a fix and publish when ready.

Without private vulnerability reporting, researchers often post vulnerabilities publicly in issues, which notifies attackers before a fix is available. Private reporting keeps the vulnerability secret during the fix period.

---

## Security Policies

A `SECURITY.md` file tells users and researchers how to report security vulnerabilities in your project.

**Create `SECURITY.md`:**

Place in the repository root, `docs/` or `.github/`. GitHub shows a link to it on the repository's Security tab and when someone attempts to open a public issue (it warns them to use the security channel instead).

**Example `SECURITY.md`:**

```markdown
# Security Policy

## Supported versions

| Version | Supported |
|---------|-----------|
| 2.x     | ✅ |
| 1.x     | ❌ |

## Reporting a vulnerability

Please do not report security vulnerabilities through public GitHub issues.

Instead, use one of these channels:

- **GitHub private vulnerability reporting:** Click "Report a vulnerability"
  on the Security tab of this repository
- **Email:** security@example.com (response within 48 hours)

Please include:
- A description of the vulnerability
- Steps to reproduce
- Potential impact
- Any suggestions for a fix

We will acknowledge your report within 48 hours, provide an update within
5 business days and aim to release a fix within 30 days.

We will credit you in the security advisory unless you prefer anonymity.
```

---

## GitHub Advanced Security (GHAS)

Since April 2025, GitHub Advanced Security is split into two products available on Team and Enterprise plans:

### GitHub Secret Protection ($19/month per active committer)

- Secret scanning for private repositories
- Push protection for private repositories
- AI-powered generic password and credential detection
- Custom secret scanning patterns (up to 500 per organisation, 100 per repo)
- Base64-encoded secret detection (added November 2025)
- Validity checks for detected secrets
- Delegated bypass for push protection

### GitHub Code Security ($30/month per active committer)

- Code scanning (CodeQL) for private repositories
- Copilot Autofix for private repositories
- Dependency review (shows vulnerabilities in PRs before merge)
- Security campaigns - bulk triage and fix of code scanning alerts across an organisation
- Premium Dependabot auto-triage rules (automatically dismiss low-risk alerts)

**Active committer:** A user who has made at least one commit to a repository with GHAS enabled in the past 90 days.

**Free trial:** Both products offer a 30-day free trial.

---

## Security Overview (Organisations)

Organisation admins have access to a **Security overview** dashboard showing the security posture across all repositories.

**Navigate to:** Organisation → Security tab.

**What it shows:**

- Which repositories have each security feature enabled or disabled
- Count of open alerts by type and severity across all repositories
- Repositories with the most unaddressed alerts
- Coverage report showing what percentage of repositories are protected
- Alert trend graphs over time

**Security campaigns:**

Create a campaign to coordinate fixing a specific type of alert across multiple repositories. Assign owners, set deadlines and track progress. Available with GitHub Code Security.

**Enable security features at scale:**

Use **Security configurations** to create a bundle of security settings and apply them to multiple repositories at once. Settings → Configurations → New configuration → choose which features to enable → apply to repository patterns.

---

## Try It Yourself

**Step 1.** Enable Dependabot alerts and security updates:

Go to any repository → Settings → Security → Enable Dependabot alerts and Dependabot security updates.

**Step 2.** Add a Dependabot version updates config:

Create `.github/dependabot.yml`:

```yaml
version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

Commit and push. Dependabot will open PRs weekly to keep your Actions versions current.

**Step 3.** Enable secret scanning and push protection:

Settings → Security → Secret scanning → Enable.
Settings → Security → Secret scanning → Push protection → Enable.

**Step 4.** Test push protection (safely):

Create a test file with a fake secret pattern:

```bash
echo "FAKE_AWS_KEY=AKIAIOSFODNN7EXAMPLE" > test-secret.txt
git add test-secret.txt
git commit -m "test: try to push a fake secret"
git push
```

The push should be blocked. Delete the file, amend or reset the commit, and push cleanly.

**Step 5.** Enable code scanning with default setup:

Settings → Security → Code scanning → Set up → Default → Enable CodeQL.

GitHub will run an initial scan and show results in Security → Code scanning alerts within a few minutes.

**Step 6.** Create a `SECURITY.md`:

```markdown
# Security Policy

## Reporting a vulnerability

Please use the GitHub private vulnerability reporting feature on the
Security tab of this repository.
```

Commit and push. Go to your Security tab and verify the link to `SECURITY.md` appears.

---

## Common Mistakes

**Dismissing Dependabot alerts without investigating.**

Dismissed alerts are not fixed - they just stop notifying you. Only dismiss if you have a specific reason: the vulnerable code path is not reachable in production, there is no fix yet (set a note to re-examine when one arrives) or it is a test dependency that is never shipped.

**Rotating secrets after detecting them without cleaning history.**

Finding a secret in a scan alert means it is in the repository's history. Simply deleting the file in a new commit does not remove it from history. Use `git filter-repo` to remove it from history and then rotate the credential immediately. Even after removing it from history, treat it as fully compromised.

**Not enabling push protection.**

Secret scanning after the fact is useful but push protection prevents the leak from ever happening. Enable push protection - it is the higher-value feature. The only cost is an occasional false positive that requires a bypass click.

**Not having a `SECURITY.md`.**

Without a security policy, researchers who find vulnerabilities have nowhere to report them except public issues. A `SECURITY.md` with private vulnerability reporting redirects them to a safe channel.

**Ignoring code scanning alerts.**

Code scanning alerts represent real security vulnerabilities in your code. If an alert is a false positive, dismiss it with a reason. If it is real, prioritise fixing it. Accumulated unaddressed alerts make your security posture progressively worse.

**Over-relying on automated security without code review.**

Security tools find known patterns. A creative attacker may exploit a vulnerability that no existing rule detects. Automated tools complement code review - they do not replace it.

---

## Summary

- GitHub's security suite includes: dependency graph, Dependabot alerts, Dependabot security and version updates, secret scanning, push protection, code scanning (CodeQL), Copilot Autofix and security advisories
- **Dependabot alerts** notify you of vulnerable dependencies; **security updates** auto-create fix PRs; **version updates** keep all dependencies current via `.github/dependabot.yml`
- **Secret scanning** detects credentials from 200+ providers across commits, issues and PRs; **push protection** blocks them before they land
- **CodeQL** performs semantic code analysis to find security vulnerabilities; supports 12+ languages
- **Copilot Autofix** generates AI-powered fix suggestions for code scanning alerts - free for public repos
- **Security advisories** provide private coordination for vulnerability disclosure; GitHub is a CVE Numbering Authority
- **Private vulnerability reporting** gives researchers a safe channel to report issues without public disclosure
- **SECURITY.md** tells users how to report vulnerabilities
- Since April 2025, GHAS is split into **GitHub Secret Protection** ($19/committer/month) and **GitHub Code Security** ($30/committer/month), available on Team and Enterprise plans
- Most features are free for public repositories

---

## Sources and Further Reading

- [GitHub security features](https://docs.github.com/en/code-security/getting-started/github-security-features) - complete overview of all security features
- [Dependabot documentation](https://docs.github.com/en/code-security/dependabot) - full Dependabot reference including `dependabot.yml` syntax
- [Secret scanning documentation](https://docs.github.com/en/code-security/secret-scanning) - secret scanning and push protection guide
- [Code scanning documentation](https://docs.github.com/en/code-security/code-scanning) - CodeQL and code scanning reference
- [GitHub Advisory Database](https://github.com/advisories) - browse all known vulnerabilities
- [Security advisories guide](https://docs.github.com/en/code-security/security-advisories/working-with-repository-security-advisories/about-repository-security-advisories) - creating and publishing advisories
- [GitHub Advanced Security](https://docs.github.com/en/get-started/learning-about-github/about-github-advanced-security) - GHAS features and pricing

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
