# All GitHub Features - Reference

**Difficulty:** 🟢 Beginner to 🔴 Advanced | **Time:** Reference - use as needed

This is the complete reference for GitHub features. Every feature is listed with a brief description, where to find it and links to the relevant file in this course. Use this as a quick lookup when you know something exists but cannot remember where to find it.

---

## Table of Contents

- [Code and repositories](#code-and-repositories)
- [Collaboration](#collaboration)
- [Automation and CI/CD](#automation-and-cicd)
- [Security](#security)
- [Project management](#project-management)
- [Publishing and community](#publishing-and-community)
- [AI features](#ai-features)
- [Profile and identity](#profile-and-identity)
- [Developer tools](#developer-tools)
- [Organisation and teams](#organisation-and-teams)
- [Billing and plans](#billing-and-plans)
- [Quick settings reference](#quick-settings-reference)

---

## Code and Repositories

### Repositories

The fundamental unit of GitHub. A repository stores your code, history, issues, PRs and settings in one place.

- Create: Click **+** → **New repository** or `gh repo create`
- Clone: Green **Code** button → copy URL → `git clone URL` or `gh repo clone OWNER/REPO`
- Fork: Click **Fork** on any repository to create your own copy
- Archive: **Settings → Danger Zone → Archive**
- Transfer: **Settings → Danger Zone → Transfer**
- Delete: **Settings → Danger Zone → Delete**
- Covered in: `03-repositories.md`

### Branches

Separate lines of development within a repository.

- Create on GitHub: branch dropdown → type new name
- Default branch: **Settings → General → Default branch**
- Branch protection rules: **Settings → Branches**
- Rulesets (modern replacement): **Settings → Rules → Rulesets**
- Covered in: `git/06-branching.md`, `09-branch-protection-and-rulesets.md`

### File editor (web)

Edit files directly on GitHub without cloning.

- Access: click any file → pencil icon
- Covered in: `04-editing-on-github.md`

### github.dev

VS Code in the browser. Press `.` on any repository.

- No terminal, no running code - editing and committing only
- Covered in: `26-platform-comparison.md`

### Codespaces

Full cloud development environment.

- Access: Green **Code** button → **Codespaces** tab
- 120 free core-hours/month (180 for students)
- Covered in: `26-platform-comparison.md`

### Git LFS (Large File Storage)

Store large binary files outside the Git object database.

- Enable: `git lfs install` then `git lfs track "*.psd"`
- Covered in: `git/22-large-repos.md`

### Releases

Tagged snapshots of a repository with downloadable assets.

- Create: **Releases** section → **Create a new release** or `gh release create`
- Covered in: `14-github-releases.md`

### Tags

Named references to specific commits. Used for version numbers.

- Create: `git tag -a v1.0.0 -m "message"` then `git push --tags`
- Covered in: `git/16-tags-and-releases.md`

---

## Collaboration

### Pull Requests

Proposals to merge changes from one branch into another. The primary code review mechanism.

- Create: **Pull requests** tab → **New pull request** or `gh pr create`
- Review: **Files changed** tab → leave comments
- Merge: **Merge pull request** button or `gh pr merge`
- Covered in: `08-pull-requests.md`

### Issues

Track bugs, feature requests and tasks.

- Create: **Issues** tab → **New issue** or `gh issue create`
- Labels: coloured tags for categorising issues
- Milestones: group issues for a release or sprint
- Templates: pre-filled forms for consistent issue reports
- Sub-issues: break a parent issue into tracked child issues
- Covered in: `07-issues.md`

### Code Review

Inline comments on pull request diffs.

- Request reviewers: right panel in a PR → **Reviewers**
- Required reviewers: set in branch protection rules
- CODEOWNERS: auto-assign reviewers based on file ownership
- Covered in: `08-pull-requests.md`, `23-collaborators-teams-orgs.md`

### Discussions

Forum-style community Q&A inside a repository or organisation.

- Enable: **Settings → Features → Discussions**
- Categories: Announcements, General, Ideas, Q&A, Polls
- Answered questions earn you the Galaxy Brain achievement
- Covered in: `12-discussions.md`

### Wikis

Separate Markdown documentation pages per repository.

- Enable: **Settings → Features → Wikis**
- The Wiki is a separate Git repository: `github.com/USER/REPO.wiki.git`
- Covered in: `24-advanced.md`

### Gists

Lightweight single-file snippets for sharing code.

- Create: `gist.github.com` or `gh gist create`
- Public gists are searchable; secret gists are URL-only
- Covered in: `24-advanced.md`

---

## Automation and CI/CD

### GitHub Actions

Automated workflow engine. Runs on events (push, PR, schedule).

- Workflow files: `.github/workflows/*.yml`
- Triggers: `push`, `pull_request`, `schedule`, `workflow_dispatch` and many more
- Runners: `ubuntu-latest`, `windows-latest`, `macos-latest` or self-hosted
- Marketplace: thousands of community actions at `github.com/marketplace`
- Covered in: `10-github-actions.md`

### Environments

Named deployment targets (staging, production) with protection rules.

- Create: **Settings → Environments**
- Add required reviewers, deployment branch restrictions
- Reference in workflows with `environment: production`
- Covered in: `10-github-actions.md`

### Secrets and Variables

Encrypted values available to Actions workflows.

- Repository secrets: **Settings → Secrets and variables → Actions → New repository secret**
- Organisation secrets: **Organisation Settings → Secrets**
- Environment secrets: scoped to a specific environment
- Reference in workflows: `${{ secrets.MY_SECRET }}`
- Covered in: `10-github-actions.md`

### Webhooks

HTTP POST callbacks sent to your server when GitHub events happen.

- Create: **Settings → Webhooks → Add webhook**
- Always verify the `X-Hub-Signature-256` header
- Covered in: `24-advanced.md`

### GitHub Apps

Integrations that act as their own identity rather than as a user.

- Install: **Settings → Applications** or from GitHub Marketplace
- Create your own: `github.com/settings/apps`
- Covered in: `24-advanced.md`

---

## Security

### Dependabot

Automated dependency updates and security alerts.

- **Dependabot alerts:** notifies you of vulnerable dependencies
- **Dependabot security updates:** auto-opens PRs to update vulnerable packages
- **Dependabot version updates:** keeps dependencies up to date on a schedule
- Enable: **Settings → Code security → Dependabot**
- Configure version updates: `.github/dependabot.yml`
- Covered in: `16-security-features.md`

### Secret Scanning

Detects secrets (API keys, tokens) committed to your repository.

- Runs automatically on public repositories
- Enable for private repositories: **Settings → Code security → Secret scanning**
- **Push protection:** blocks pushes containing known secret formats
- Covered in: `16-security-features.md`

### Code Scanning (CodeQL)

Static analysis to find security vulnerabilities in your code.

- Enable: **Settings → Code security → Code scanning**
- Uses the CodeQL engine (or third-party tools)
- Results appear in the **Security** tab
- Covered in: `16-security-features.md`

### Security Advisories

Draft and disclose security vulnerabilities privately before patching.

- Access: **Security** tab → **Advisories**
- Private fork for patching: GitHub creates a temporary private fork
- Covered in: `16-security-features.md`

### Branch Protection Rules

Enforce workflow requirements before code can merge to a branch.

- Require approvals, status checks, signed commits, linear history
- Access: **Settings → Branches → Add rule**
- Rulesets supersede branch protection rules for newer repositories
- Covered in: `09-branch-protection-and-rulesets.md`

### Private Vulnerability Reporting

Allow security researchers to report vulnerabilities privately.

- Enable: **Settings → Code security → Private vulnerability reporting**
- Reporters can submit via the **Security** tab without opening a public issue
- Covered in: `16-security-features.md`

---

## Project Management

### GitHub Projects

Kanban boards, tables and roadmaps for planning work.

- Create: **Projects** tab on your profile or organisation → **New project**
- Views: Board, Table, Roadmap
- Custom fields: text, number, date, single select, iteration
- Automation: built-in triggers and GitHub Actions integration
- Covered in: `13-projects-and-boards.md`

### Milestones

Group issues and PRs into named checkpoints.

- Create: **Issues** tab → **Milestones** → **New milestone**
- Shows progress as a percentage of closed items
- Covered in: `07-issues.md`

### Labels

Colour-coded tags for categorising issues and PRs.

- Default labels: `bug`, `documentation`, `enhancement`, `good first issue`, etc.
- Create custom labels: **Issues** tab → **Labels** → **New label**
- Covered in: `07-issues.md`

---

## Publishing and Community

### GitHub Pages

Free static site hosting from a repository.

- Enable: **Settings → Pages → Source**
- Custom domain: add a `CNAME` file and configure DNS
- Supports Jekyll, plain HTML/CSS/JS or any static site generator via Actions
- Covered in: `11-github-pages.md`

### GitHub Sponsors

Receive recurring financial support from the community.

- Apply: `github.com/sponsors`
- Set tiers and perks
- GitHub takes 0% (as of March 2026)
- Covered in: `24-advanced.md`

### GitHub Marketplace

Apps and Actions available to install or purchase.

- Browse: `github.com/marketplace`
- Categories: CI/CD, code quality, security, project management, deployment
- Covered in: `10-github-actions.md`

---

## AI Features

### GitHub Copilot

AI code completion and chat assistant.

- Inline completions in your editor as you type
- Copilot Chat: ask questions, explain code, write tests
- Copilot Edits: multi-file changes
- Agent mode: autonomous multi-step task completion
- Free tier: 2000 completions/month, 50 chat messages/month
- Pro free for students via the Developer Pack
- Covered in: `19-copilot.md`

### Copilot on GitHub.com

Ask questions about a repository directly on GitHub.

- Click the Copilot icon on any repository page
- Summarise PRs, explain code, find relevant files
- Covered in: `19-copilot.md`

---

## Profile and Identity

### Profile Page

Your public developer homepage at `github.com/USERNAME`.

- Edit: **Settings → Profile**
- Pinned repositories: shown prominently on your profile
- Contribution graph: green squares showing daily activity
- Covered in: `17-profile-and-readme.md`

### Profile README

A Markdown file that renders at the top of your profile.

- Create a public repository named exactly your username
- Add a `README.md` to the root
- Covered in: `17-profile-and-readme.md`

### Achievements

Badges earned by doing specific things on GitHub.

- Show/hide: **Settings → Profile → Show achievements**
- Earnable: Pull Shark, YOLO, Quickdraw, Pair Extraordinaire, Starstruck, Galaxy Brain, Public Sponsor
- Covered in: `18-badges-and-achievements.md`

### Verified commits

GPG or SSH signatures on commits proving they came from you.

- Set up: `git config --global gpg.format ssh` then add your key to GitHub
- Covered in: `git/20-signing-commits.md`

---

## Developer Tools

### GitHub CLI

Command-line interface for GitHub.

- Install: `winget install GitHub.cli` (🪟) / `brew install gh` (🍎) / `apt install gh` (🐧)
- Authenticate: `gh auth login`
- Covered in full: `21-cli.md`

### GitHub Mobile

iOS and Android app.

- Download from the App Store or Google Play
- Best for notifications, PR review and issue management
- Covered in full: `22-mobile.md`

### GitHub Desktop

GUI Git client for Windows and Mac.

- Download: `desktop.github.com`
- Visual staging, diff view and branch management
- Covered in: `03-github-desktop.md`

### REST API

Programmatic access to all GitHub data and actions.

- Base URL: `https://api.github.com`
- Auth: Bearer token in the `Authorization` header
- Rate limit: 5000 requests/hour when authenticated
- Covered in: `24-advanced.md`

### GraphQL API

Flexible query language for nested GitHub data.

- Endpoint: `https://api.github.com/graphql`
- Required for Projects v2 API
- Explorer: `docs.github.com/en/graphql/overview/explorer`
- Covered in: `24-advanced.md`

### Personal Access Tokens

Authentication tokens for API and CLI access.

- Create: `github.com/settings/tokens`
- Use fine-grained tokens with limited scope and an expiry date
- Covered in: `24-advanced.md`

### Advanced Search

Find code, issues, users and repositories with qualifiers.

- Access: search bar on any GitHub page or `github.com/search`
- Key qualifiers: `is:`, `language:`, `stars:`, `author:`, `label:`, `org:`
- Covered in: `24-advanced.md`

---

## Organisation and Teams

### Organisations

Shared accounts for groups and companies.

- Create: **+** → **New organisation**
- Own repositories independently of any individual account
- Covered in: `23-collaborators-teams-orgs.md`

### Teams

Groups of organisation members for managing repository access.

- Create: Organisation → **Teams** tab → **New team**
- Grant teams repository access in bulk
- Mention with `@ORG/TEAM_NAME`
- Covered in: `23-collaborators-teams-orgs.md`

### CODEOWNERS

Auto-assign reviewers based on which files changed.

- File location: `CODEOWNERS`, `.github/CODEOWNERS` or `docs/CODEOWNERS`
- Covered in: `23-collaborators-teams-orgs.md`

### Audit Log

Record of all significant actions in an organisation.

- Access: Organisation **Settings → Audit log**
- Covered in: `23-collaborators-teams-orgs.md`

---

## Billing and Plans

| Plan | Price | Key features |
|---|---|---|
| Free | $0 | Unlimited public and private repos, 2000 Actions minutes/month |
| Pro | $4/month | 3000 Actions minutes, protected branches, Copilot Pro |
| Team | $4/user/month | Organisation features, 3000 minutes, advanced branch protection |
| Enterprise | $21/user/month | SSO, audit log streaming, compliance features, 50000 minutes |

Students get **GitHub Pro free** via the Student Developer Pack.

**Billing page:** `github.com/settings/billing`

---

## Quick Settings Reference

| Setting | Where to find it |
|---|---|
| Profile info (name, bio, location) | `github.com/settings/profile` |
| Account security and 2FA | `github.com/settings/security` |
| SSH and GPG keys | `github.com/settings/keys` |
| Personal Access Tokens | `github.com/settings/tokens` |
| Email addresses | `github.com/settings/emails` |
| Notifications | `github.com/settings/notifications` |
| GitHub Copilot | `github.com/settings/copilot` |
| Billing | `github.com/settings/billing` |
| Applications (OAuth and GitHub Apps) | `github.com/settings/applications` |
| Developer settings (Apps you own) | `github.com/settings/developers` |
| Repository: general | `github.com/OWNER/REPO/settings` |
| Repository: branches | `github.com/OWNER/REPO/settings/branches` |
| Repository: secrets | `github.com/OWNER/REPO/settings/secrets/actions` |
| Repository: Pages | `github.com/OWNER/REPO/settings/pages` |
| Repository: webhooks | `github.com/OWNER/REPO/settings/hooks` |
| Organisation: members | `github.com/orgs/ORG/people` |
| Organisation: teams | `github.com/orgs/ORG/teams` |
| Organisation: settings | `github.com/organizations/ORG/settings` |
| Organisation: audit log | `github.com/organizations/ORG/settings/audit-log` |

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
