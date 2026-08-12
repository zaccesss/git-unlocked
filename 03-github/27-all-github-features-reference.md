# All GitHub Features - Reference

**Difficulty:** 🟢 Beginner to 🔴 Advanced | **Time:** Reference - use as needed

This is the complete reference for every GitHub feature. Each entry includes what the feature is, where to find it, what it requires and which file in this course covers it in depth. Use this file as a quick lookup when you know a feature exists but cannot remember where it lives or what it is called.

---

## Table of Contents

- [Code and repositories](#code-and-repositories)
- [Collaboration](#collaboration)
- [Code review](#code-review)
- [Automation and CI/CD](#automation-and-cicd)
- [Security](#security)
- [Project management](#project-management)
- [Publishing and hosting](#publishing-and-hosting)
- [Community](#community)
- [AI features](#ai-features)
- [Profile and identity](#profile-and-identity)
- [Developer tools](#developer-tools)
- [Organisation and teams](#organisation-and-teams)
- [API and integrations](#api-and-integrations)
- [Monetisation](#monetisation)
- [Education](#education)
- [Billing and plans](#billing-and-plans)
- [Quick settings paths](#quick-settings-paths)

---

## Code and Repositories

### Repositories

The fundamental unit of GitHub. Stores code, history, issues, PRs, wikis and settings.

**Create:** Click **+** → **New repository** | `gh repo create`

**Clone:** Green **Code** button → copy URL → `git clone URL` | `gh repo clone OWNER/REPO`

**Fork:** Click **Fork** on any repository to create your own copy under your account

**Visibility:** Public (anyone can view), Private (only collaborators), Internal (Enterprise: anyone in org)

**Archive:** Settings → Danger Zone → Archive this repository. Archived repos are read-only.

**Transfer:** Settings → Danger Zone → Transfer ownership. Moves to another user or org.

**Delete:** Settings → Danger Zone → Delete this repository. Permanent.

**Template repositories:** Settings → General → tick "Template repository". Others can use your repo as a starting point.

**Rename:** Settings → General → Repository name. GitHub creates redirects from the old URL.

**Covered in:** `04-repositories.md`

---

### Branches

Separate lines of development within a repository.

**Create on GitHub:** Branch dropdown → type a new name → Create branch

**Create via CLI:** `git checkout -b branch-name` | `gh api repos/OWNER/REPO/git/refs --method POST`

**Default branch:** Settings → General → Default branch. Usually `main`.

**Delete a branch:** Branch dropdown → Manage branches → Delete icon. Or `git push origin --delete branch-name`.

**Covered in:** `git/06-branching.md`

---

### Branch Protection Rules

Enforce requirements before code can be merged to a protected branch.

**Access:** Settings → Branches → Add branch protection rule

**Common rules:**

- Require pull request before merging (with N required approvals)
- Require status checks to pass (CI must pass before merge)
- Require branches to be up to date before merging
- Require conversation resolution before merging
- Require signed commits
- Require linear history (no merge commits)
- Restrict who can push to matching branches
- Require code owner review

**Rulesets:** The modern replacement. Settings → Rules → Rulesets. More powerful than branch protection - can target multiple branches and tags with conditions.

**Covered in:** `09-branch-protection-and-rulesets.md`

---

### File Editor (Web)

Edit a single file directly on GitHub without cloning.

**Access:** Open any file → click the pencil icon (Edit this file)

**Supports:** Syntax highlighting, basic editing. Commits directly or creates a new branch.

**Limitation:** One file per commit. Use github.dev for multi-file changes.

**Covered in:** `06-editing-on-github.md`

---

### github.dev (Browser Editor)

Full VS Code editor running in the browser tab.

**Access:** Press `.` on any repository. Or change `github.com` to `github.dev` in the URL.

**Can:** Edit multiple files, create branches, commit via Source Control panel, use limited VS Code extensions, search across the repo

**Cannot:** Run a terminal, execute code, install packages

**Covered in:** `26-platform-comparison.md`

---

### Codespaces

Full cloud development environment (Linux VM) accessible from the browser or local VS Code.

**Access:** Green **Code** button → **Codespaces** tab → **Create codespace**

**Free tier:** 120 core-hours/month (180 for students with Developer Pack)

**Configure with:** `.devcontainer/devcontainer.json` for custom environments, extensions and startup commands

**CLI:** `gh codespace create` | `gh codespace code` | `gh codespace ssh`

**Stop when done:** Always stop a Codespace when not using it - it consumes core-hours while running

**Covered in:** `26-platform-comparison.md`

---

### Git LFS (Large File Storage)

Store large binary files outside the Git object database while still tracking them in the repository.

**Install:** `git lfs install` (once per machine)

**Track a file type:** `git lfs track "*.psd"` (adds a rule to `.gitattributes`)

**How it works:** Tracked files are replaced with small pointer files in Git. The actual file content is stored separately on GitHub's LFS servers.

**Limits:** 1 GB free storage, 1 GB free bandwidth per month. GitHub Pro/Team/Enterprise have higher limits.

**Covered in:** `git/22-large-repos.md`

---

### Tags and Releases

Named references to specific commits. Used for versioning.

**Annotated tag:** `git tag -a v1.0.0 -m "Release v1.0.0"` then `git push --tags`

**Lightweight tag:** `git tag v1.0.0` then `git push --tags`

**GitHub Releases:** Built on top of tags. Add release notes, attached binary assets and a published download page.

**Create release:** Go to repository → **Releases** → **Create a new release** | `gh release create v1.0.0`

**Auto-generate notes:** GitHub generates a changelog from merged PRs since the last release - click **Generate release notes** on the release form.

**Covered in:** `git/16-tags-and-releases.md`, `14-github-releases.md`

---

### GitHub Packages

Host packages (npm, Docker, Maven, NuGet, RubyGems, Gradle, containers) directly on GitHub.

**Access:** A repository's **Packages** section on the right side or `github.com/OWNER/REPO/pkgs`

**Authenticate:** `echo TOKEN | docker login ghcr.io -u USERNAME --password-stdin` (containers) or configure `.npmrc` for npm

**Free tier:** 500 MB storage, 1 GB data transfer/month

**Covered in:** `15-github-packages.md`

---

## Collaboration

### Pull Requests

Proposals to merge changes from one branch into another. The central code review and collaboration mechanism.

**Create:** Pull requests tab → **New pull request** | `gh pr create --fill`

**Draft PR:** A PR not yet ready for review. Reviewers are not notified for draft PRs. Toggle with `gh pr ready`.

**Review:** Files changed tab → leave inline comments → Submit review (Approve / Request changes / Comment)

**Merge options:** Merge commit, Squash and merge, Rebase and merge

**Auto-merge:** Automatically merges when all checks and required reviews pass. `gh pr merge --auto`

**Close without merging:** Click **Close pull request** | `gh pr close`

**PR checks:** Shows the status of all CI checks. Failing checks block merge on protected branches.

**Covered in:** `08-pull-requests.md`

---

### Issues

Track bugs, feature requests, questions and tasks.

**Create:** Issues tab → **New issue** | `gh issue create`

**Templates:** `.github/ISSUE_TEMPLATE/` - pre-filled forms for consistent issue reports. Can require specific fields.

**Labels:** Coloured tags. Defaults include `bug`, `documentation`, `enhancement`, `good first issue`, `help wanted`. Create custom labels freely.

**Assignees:** Assign issues to GitHub users. Multiple assignees are supported.

**Milestones:** Group issues and PRs into a versioned checkpoint. Shows progress as a percentage.

**Sub-issues:** Break a parent issue into child issues. Shows progress on the parent.

**Linked PRs:** Link an issue to a PR with keywords: `Closes #42`, `Fixes #42`, `Resolves #42`. The issue closes automatically when the PR merges.

**Transfer:** Move an issue to another repository. `gh issue transfer 42 OWNER/OTHER-REPO`

**Pin:** Pin up to 3 issues to the top of the Issues tab.

**Covered in:** `07-issues.md`

---

### CODEOWNERS

Automatically assign reviewers to a PR based on which files it modifies.

**File location:** `CODEOWNERS`, `.github/CODEOWNERS` or `docs/CODEOWNERS` (checked in that order)

**Syntax:**

```
# Pattern → Owner(s)
*.py              @zaccesss
/docs/            @org/technical-writers
src/auth/         @org/security-team @leaddev
```

**Require CODEOWNER review:** Settings → Branches → Branch protection → tick **Require review from Code Owners**

**Covered in:** `23-collaborators-teams-orgs.md`

---

### Discussions

Forum-style community Q&A, announcements and polls built into a repository or organisation.

**Enable:** Settings → Features → tick **Discussions**

**Categories:** Announcements (maintainer-only), General, Ideas, Q&A (has accepted answers), Show and Tell, Polls

**Accepted answers:** In Q&A discussions, a maintainer or the question author marks one comment as the accepted answer. This earns the answerer progress towards the Galaxy Brain achievement.

**Convert issue to discussion:** Issues can be converted to discussions (and vice versa) if the topic is better suited to open-ended conversation.

**Organisation discussions:** Available at the organisation level (not just per-repository) under the **Discussions** tab on the org page.

**Covered in:** `12-discussions.md`

---

### Wikis

Separate Markdown documentation pages per repository. Stored as a separate Git repository.

**Enable:** Settings → General → Features → tick **Wikis**

**Clone the wiki repo:** `git clone https://github.com/USERNAME/REPO.wiki.git`

**Navigation:** Create a `_Sidebar` page for left navigation. Create a `_Footer` page for a footer on all pages.

**Restrict editing:** Settings → General → tick **Restrict editing to collaborators only**

**Covered in:** `24-advanced.md`

---

### Gists

Lightweight single-file snippets with Git version history.

**Create:** `gist.github.com` | `gh gist create myfile.py --public`

**Public vs secret:** Public gists are indexed by search engines. Secret gists are URL-only (not private - anyone with the URL can see them).

**Embed in web page:** Each gist has a `<script>` embed code for rendering in any HTML page.

**Clone:** `gh gist clone GIST_ID` (gists are full Git repos)

**Covered in:** `24-advanced.md`

---

## Code Review

### Pull Request Reviews

The formal review process on a PR.

**Review states:** Approve, Request changes, Comment (no decision)

**Required reviews:** Set in branch protection rules. How many approvals are needed before merge.

**Dismiss a review:** An admin can dismiss a stale review after new changes are pushed.

**Re-request review:** After making changes, re-request review from the same reviewer.

**Review suggestions:** In a review comment, click **Add suggestion** to propose a specific code change. The PR author can apply it with one click.

**Resolved conversations:** Mark a thread as resolved when the feedback is addressed.

**Covered in:** `08-pull-requests.md`

---

### Code Review Assignments

Automatically assign reviewers to PRs.

**Enable:** Repository settings → Branches → (within a branch protection rule) → Code review assignments

**Round-robin or load balance:** Distribute review requests evenly across a team.

**Require reviews from CODEOWNERS:** Ensures subject matter experts always review the relevant files.

**Covered in:** `08-pull-requests.md`, `23-collaborators-teams-orgs.md`

---

## Automation and CI/CD

### GitHub Actions

Automated workflow engine. Triggered by events (push, PR, schedule, manual dispatch).

**Workflow files:** `.github/workflows/*.yml`

**Key syntax:**

```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm test
```

**Triggers:** `push`, `pull_request`, `schedule` (cron), `workflow_dispatch` (manual), `release`, `issues` and 40+ others

**Runners:** `ubuntu-latest`, `windows-latest`, `macos-latest` (GitHub-hosted) or self-hosted runners

**Secrets in workflows:** `${{ secrets.MY_SECRET }}` - stored in Settings → Secrets and variables → Actions

**Environments:** Named deployment targets with protection rules. Reference with `environment: production`.

**Matrix builds:** Run the same job across multiple OS/language version combinations

**Artefacts:** `actions/upload-artifact` and `actions/download-artifact` to pass files between jobs

**Actions Marketplace:** `github.com/marketplace?type=actions` - thousands of community actions

**Covered in:** `10-github-actions.md`

---

### Environments

Named deployment targets (staging, production) with protection rules and secrets.

**Create:** Settings → Environments → New environment

**Protection rules:** Required reviewers (a specific person must approve before deployment), wait timer, deployment branch restrictions

**Environment secrets:** Only accessible to workflows referencing that specific environment

**Deployment history:** Track every deployment to each environment with status and who deployed

**Covered in:** `10-github-actions.md`

---

### Webhooks

HTTP POST callbacks sent to your server when GitHub events occur.

**Create:** Settings → Webhooks → Add webhook

**Configure:** Payload URL (your endpoint), content type (application/json), secret (for signature verification), which events to subscribe to

**Verify signatures:** Check `X-Hub-Signature-256` header using HMAC-SHA256 with your secret

**Recent deliveries:** Settings → Webhooks → your webhook → Recent Deliveries. View payloads and re-deliver any request.

**Test locally:** Use smee.io or ngrok to relay webhooks to your local server during development

**Covered in:** `24-advanced.md`

---

### GitHub Apps

Production integrations that act as their own identity (`appname[bot]`), not as a user.

**Install:** Settings → Applications → Install a GitHub App | GitHub Marketplace

**Manage installed apps:** Settings → Applications → Installed GitHub Apps

**Create your own:** `github.com/settings/apps` → New GitHub App

**Authentication:** JWT (signed with private key) → exchange for installation access token (valid 1 hour)

**Rate limit:** 5000-15000 requests/hour (higher than PATs)

**Well-known apps:** Dependabot, Renovate, Vercel, Netlify, CircleCI, Datadog, Sentry, Linear

**Covered in:** `24-advanced.md`

---

### Required Workflows (Enterprise)

Organisation-level workflows that must pass on all PRs across all repositories.

**Access:** Organisation Settings → Actions → Required workflows

**Use case:** Mandatory security scanning, licence checks, compliance tests that must pass on every PR regardless of repository

---

## Security

### Dependabot

Automated dependency vulnerability alerts and update PRs.

**Dependabot alerts:** Detects vulnerable dependencies. Auto-enabled on public repos. Settings → Code security → Dependabot alerts.

**Dependabot security updates:** Automatically opens PRs to fix vulnerable dependencies. Settings → Code security → Dependabot security updates.

**Dependabot version updates:** Keeps all dependencies up to date on a schedule. Configure via `.github/dependabot.yml`.

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
```

**Covered in:** `16-security-features.md`

---

### Secret Scanning

Detects secrets (API keys, tokens, credentials) committed to repositories.

**Public repos:** Automatic. GitHub notifies the secret's issuer (AWS, Stripe, GitHub etc.) who may revoke it.

**Private repos:** Enable at Settings → Code security → Secret scanning.

**Push protection:** Blocks pushes containing known secret formats before they reach GitHub. Settings → Code security → Push protection.

**Partner patterns:** 200+ known secret formats from AWS, Google, Microsoft, Stripe, Twilio, Slack and more.

**Custom patterns:** Enterprise feature. Define your own regex patterns for organisation-specific secrets.

**Covered in:** `16-security-features.md`, `25-what-should-not-be-on-github.md`

---

### Code Scanning (CodeQL)

Static analysis to find security vulnerabilities in code.

**Enable:** Security tab → Set up code scanning | Settings → Code security → Code scanning

**CodeQL:** GitHub's query language for code analysis. Finds SQL injection, XSS, path traversal, insecure deserialization and many more vulnerability classes.

**Results:** Security tab → Code scanning alerts. Each alert shows the vulnerable code, the description and suggested fix.

**Copilot Autofix:** When CodeQL finds a vulnerability, Copilot can suggest an automated fix. Click **Fix with Copilot** on any alert.

**Third-party tools:** CodeQL integrates with Checkmarx, Fortify, Snyk, Semgrep and others via SARIF output.

**Covered in:** `16-security-features.md`

---

### Security Advisories

Draft and coordinate disclosure of security vulnerabilities privately before patching.

**Access:** Security tab → Advisories

**Private fork:** GitHub creates a temporary private fork of the repository for developing the patch without public disclosure.

**CVE assignment:** GitHub can request a CVE identifier for your advisory.

**Private vulnerability reporting:** Allow security researchers to submit vulnerabilities privately via the Security tab. Enable at Settings → Code security → Private vulnerability reporting.

**Covered in:** `16-security-features.md`

---

### Deploy Keys

SSH keys that grant access to a single specific repository. Used for deployment automation.

**Add:** Settings → Deploy keys → Add deploy key. Paste the public key.

**Read-only vs read-write:** Choose whether the key can push (write) or only pull (read).

**Rotation:** Generate a new key pair, add the new public key, update the server to use the new private key, then remove the old deploy key.

**Covered in:** `23-collaborators-teams-orgs.md`

---

## Project Management

### GitHub Projects (v2)

Flexible project boards with table, board and roadmap views.

**Create:** Profile/organisation page → **Projects** → **New project** | A repository's **Projects** tab

**Views:**

- **Board:** Kanban-style columns. Drag cards between columns.
- **Table:** Spreadsheet-style with sortable columns. Good for filtering and bulk editing.
- **Roadmap:** Timeline view with date ranges. For release planning and sprints.

**Custom fields:** Text, number, date, single select (status, priority), iteration (sprint cycles)

**Automation:** Auto-add items from linked repositories, auto-close or auto-archive, trigger on PR merge or issue close

**API:** Projects v2 is GraphQL-only. Use `gh api graphql` for automation.

**Covered in:** `13-projects-and-boards.md`

---

### Milestones

Group issues and PRs into named checkpoints (typically releases or sprints).

**Create:** Issues tab → **Milestones** → **New milestone**. Set a title, description and due date.

**Progress:** Shows as a percentage based on open vs closed issues/PRs in the milestone.

**Filter:** The issues list can be filtered by milestone.

**Covered in:** `07-issues.md`

---

### Labels

Colour-coded tags for categorising issues and PRs.

**Default labels:** `bug`, `documentation`, `duplicate`, `enhancement`, `good first issue`, `help wanted`, `invalid`, `question`, `wontfix`

**Create:** Issues tab → **Labels** → **New label**. Choose name, colour and description.

**Clone labels across repos:** `gh label clone SOURCE_OWNER/SOURCE_REPO` - copies all labels from one repo to another.

**Covered in:** `07-issues.md`

---

## Publishing and Hosting

### GitHub Pages

Free static site hosting directly from a repository.

**Enable:** Settings → Pages. Choose source: a branch and folder (`/` root or `/docs`) or a GitHub Actions workflow.

**Custom domain:** Add a `CNAME` file to the repository root, configure your DNS with a CNAME pointing to `USERNAME.github.io`.

**Jekyll:** GitHub Pages has built-in Jekyll support. Place `_config.yml` in the root to use a theme without a build step.

**Actions deployment:** Use `actions/deploy-pages` action for any static site generator (Hugo, Astro, Next.js static export, MkDocs etc.).

**Covered in:** `11-github-pages.md`

---

### GitHub Packages

Package registry for npm, Docker images, Maven, NuGet, RubyGems, Gradle and containers.

**Container Registry (ghcr.io):** Push Docker images to `ghcr.io/OWNER/IMAGE_NAME`.

**npm packages:** Publish as `@OWNER/package-name`. Configure `.npmrc` with `registry=https://npm.pkg.github.com`.

**Authentication:** Use `GITHUB_TOKEN` in Actions or a PAT with `write:packages` scope.

**Free tier:** 500 MB storage, 1 GB data transfer/month

**Covered in:** `15-github-packages.md`

---

## Community

### GitHub Sponsors

Receive recurring financial support from the developer community.

**Apply:** `github.com/sponsors` → **Get sponsored**

**Set up tiers:** Monthly amounts with optional perks (README mention, Discord access, early access etc.)

**GitHub fee:** 0% (as of March 2026). Stripe processing fees apply.

**Sponsor button:** Appears on your profile and repositories once approved. Configure with `.github/FUNDING.yml`.

**Public Sponsor badge:** Anyone who sponsors a developer publicly earns the Public Sponsor achievement.

**Covered in:** `24-advanced.md`

---

## AI Features

### GitHub Copilot

AI code completion and chat assistant powered by GPT-5, Claude Sonnet and Gemini models.

**Free tier:** 2000 completions/month, 50 chat messages/month

**Pro:** Unlimited (free for students via Developer Pack)

**Install in VS Code:** Extensions → search "GitHub Copilot" → Install

**Inline completions:** Ghost text appears as you type. `Tab` to accept, `Escape` to dismiss.

**Copilot Chat:** `Ctrl+Alt+I` / `Cmd+Option+I` in VS Code. Ask questions, debug, write tests.

**Context variables:** `#file:filename` in chat to inject a specific file's contents into context.

**Agent mode:** Autonomous multi-step task execution (Pro+ / Business). Runs terminal commands with confirmation.

**Custom instructions:** `.github/copilot-instructions.md` - repository-level standing instructions for all Chat interactions.

**Covered in:** `19-copilot.md`

---

### Copilot on GitHub.com

Ask questions about any repository directly on github.com.

**Access:** Copilot icon (top-right) on any repository, issue or PR page.

**Uses:** "How does authentication work in this project?", summarise a PR, explain a commit diff

**PR auto-descriptions:** Copilot icon next to the PR description field → generates a description from the diff

**Covered in:** `19-copilot.md`

---

### Copilot Autofix

AI-generated fixes for CodeQL security vulnerabilities.

**Access:** Security tab → Code scanning alerts → any alert → **Fix with Copilot**

**Result:** A pull request is opened with Copilot's suggested fix. Review and merge if correct.

**Covered in:** `16-security-features.md`, `19-copilot.md`

---

## Profile and Identity

### Profile Page

Your public developer homepage at `github.com/YOUR_USERNAME`.

**Edit:** Settings → Profile (name, bio, pronouns, company, location, website, social links)

**Pinned repositories:** Pin up to 6 repositories to highlight on your profile. Drag to reorder.

**Contribution graph:** Shows daily commit activity for the past year.

**Achievements:** Badge panel showing earned GitHub achievements.

**Profile README:** A special repository named exactly your username with a `README.md` - renders at the top of your profile.

**Covered in:** `17-profile-and-readme.md`

---

### Achievements

Badges earned by performing specific activities on GitHub.

**Earnable achievements:**

| Achievement         | Requirement                                        |
| ------------------- | -------------------------------------------------- |
| Pull Shark          | 2 PRs merged (tiers: 16, 128, 1024)                |
| YOLO                | Merge a PR with no review                          |
| Quickdraw           | Close an issue or PR within 5 min of opening       |
| Pair Extraordinaire | 1 co-authored PR merged (tiers: 10, 24, 48)        |
| Starstruck          | 16 stars on one repository (tiers: 128, 512, 4096) |
| Galaxy Brain        | 1 accepted Discussion answer (tiers: 8, 16, 32)    |
| Public Sponsor      | Make a public GitHub sponsorship                   |

**Show/hide:** Settings → Profile → Show achievements on my profile

**Covered in:** `18-badges-and-achievements.md`

---

### Verified Commits

GPG or SSH signatures on commits proving they are genuine.

**Set up SSH signing (recommended):** `git config --global gpg.format ssh` then add your key in Settings → SSH and GPG keys → New signing key

**Set up GPG signing:** Install GPG, generate a key, export the public key, add to GitHub, configure git to use it

**Vigilant mode:** Settings → SSH and GPG keys → **Vigilant mode**. Marks all unsigned commits as "Unverified". Use only if you sign every commit.

**Covered in:** `git/20-signing-commits.md`

---

## Developer Tools

### GitHub CLI

Official command-line tool for the GitHub API layer.

**Install:** `winget install GitHub.cli` (🪟) | `brew install gh` (🍎) | `sudo apt install gh` (🐧)

**Authenticate:** `gh auth login` then `gh auth setup-git`

**Key commands:** `gh repo create/clone/fork`, `gh issue create/list/close`, `gh pr create/checkout/merge`, `gh workflow run`, `gh run view --log`, `gh release create`

**Scripting:** `--json` + `--jq` for machine-readable output. `gh api` for direct REST/GraphQL access.

**Extensions:** `gh extension install github/gh-copilot` adds Copilot to the terminal.

**Covered in:** `21-cli.md`

---

### GitHub Desktop

Visual Git client for Windows and Mac.

**Download:** `desktop.github.com`

**Features:** Visual staging and diff, partial staging (line-by-line), commit history graph, merge conflict resolution, branch management

**Covered in:** `03-github-desktop.md`

---

### GitHub Mobile

iOS and Android app for notifications, PR review and issue management.

**Download:** App Store or Google Play → search "GitHub"

**Key features:** Push notifications, PR diff review with inline comments, issue management, repository browsing, built-in 2FA authenticator

**Covered in:** `22-mobile.md`

---

### REST API

Programmatic HTTP access to all GitHub data and actions.

**Base URL:** `https://api.github.com`

**Authentication:** `Authorization: Bearer TOKEN` header

**Rate limit:** 60/hour (unauthenticated), 5000/hour (authenticated)

**Version header:** `X-GitHub-Api-Version: 2022-11-28`

**Pagination:** `?per_page=100&page=2`. Follow `Link` header for next/prev URLs.

**Covered in:** `24-advanced.md`

---

### GraphQL API

Flexible query API for nested data and Projects v2.

**Endpoint:** Always POST to `https://api.github.com/graphql`

**Explorer:** `docs.github.com/en/graphql/overview/explorer` - interactive query builder with autocomplete

**Required for:** GitHub Projects v2 API

**Covered in:** `24-advanced.md`

---

### Personal Access Tokens (PATs)

Authentication tokens for API access.

**Create:** `github.com/settings/tokens`

**Types:** Classic (broad scopes) and Fine-grained (per-repository, preferred)

**Best practice:** Use fine-grained tokens. Select only required repositories and minimum permissions. Always set an expiry date.

**Covered in:** `24-advanced.md`

---

### Advanced Search

Find code, issues, users and repositories with qualifiers.

**Access:** Search bar on any page or `github.com/search`

**Key qualifiers:** `is:`, `label:`, `language:`, `stars:`, `author:`, `org:`, `repo:`, `created:`, `pushed:`, `in:title`, `no:assignee`

**Example:** `is:issue is:open label:"good first issue" language:python no:assignee` - unassigned beginner Python issues

**Covered in:** `24-advanced.md`

---

## Organisation and Teams

### Organisations

Shared accounts for groups and companies. Repository ownership is tied to the org, not an individual.

**Create:** Click **+** → **New organisation**

**Profile:** Public page at `github.com/ORG_NAME` with bio, website, repositories and members

**Roles:** Owner, Member, Billing manager, Security manager, Outside collaborator

**Base permissions:** Organisation Settings → Member privileges → Base permissions. Set to None or Read for most orgs.

**Covered in:** `23-collaborators-teams-orgs.md`

---

### Teams

Named groups of organisation members for managing repository access at scale.

**Create:** Organisation → Teams tab → **New team**

**Mention:** `@ORG_NAME/TEAM_NAME` in issues and PRs notifies all team members

**Visibility:** Visible (all members can see the team) or Secret (team members and owners only)

**Nested teams:** Child teams inherit parent team's repository permissions

**CODEOWNERS:** Reference teams as `@ORG/TEAM_NAME` to auto-assign reviews

**Covered in:** `23-collaborators-teams-orgs.md`

---

### Outside Collaborators

Non-members with access to specific repositories.

**Add:** Repository → Settings → Collaborators and teams → **Add people**

**Use case:** Contractors, external contributors, clients who need one-repository access without full org membership

**View all:** Organisation Settings → People → Outside collaborators

**Covered in:** `23-collaborators-teams-orgs.md`

---

### Audit Log

Record of all significant actions by organisation members and owners.

**Access:** Organisation Settings → Audit log

**What is logged:** Member changes, repository access changes, settings changes, Actions runs, security events

**Search:** Filter by actor, action type, date range. Example: `action:repo.create actor:USERNAME`

**Export:** CSV or JSON. Enterprise: stream in real time to Azure Event Hubs, S3, GCS or Datadog.

**Covered in:** `23-collaborators-teams-orgs.md`

---

## API and Integrations

### Webhooks

HTTP POST callbacks for GitHub events. See [Webhooks](#webhooks) above.

---

### GitHub Apps

Production integrations with their own identity. See [GitHub Apps](#github-apps) above.

---

### OAuth Apps

Let users authorise your web application to act on their behalf via GitHub.

**Create:** `github.com/settings/developers` → OAuth Apps → **New OAuth App**

**Flow:** Redirect user to GitHub → user approves → GitHub redirects back with a code → exchange code for access token

**Covered in:** `24-advanced.md`

---

### Marketplace

Apps and Actions to extend GitHub.

**Access:** `github.com/marketplace`

**Categories:** CI/CD, code quality, security, testing, project management, deployment, IDEs

**Free and paid apps:** Many integrations have free tiers. Paid apps are billed through GitHub.

---

## Monetisation

### GitHub Sponsors

Receive recurring financial support. See [GitHub Sponsors](#github-sponsors) above.

---

### GitHub Marketplace (selling apps)

Publish your GitHub App or Action to the Marketplace.

**Requirements:** Published GitHub App with a logo, description and pricing information

**Pricing models:** Free, flat rate, per-seat, tiered usage

**Access:** `github.com/marketplace` → **List your app**

---

## Education

### Student Developer Pack

100+ free professional tools for verified students.

**Apply:** `education.github.com/pack`

**Includes:** Copilot Pro, JetBrains IDEs, Azure credits, DigitalOcean credits, domains, Canva Pro, Frontend Masters and more

**Duration:** 2 years, renewable while enrolled

**Covered in:** `20-education.md`

---

### GitHub Classroom

Distribute and assess coding assignments via GitHub repositories.

**Access:** `classroom.github.com`

**How it works:** Teacher creates an assignment from a template repo → students click an invitation link → each student gets a private repo → teacher sees all repos in a dashboard

**Autograding:** Run test commands automatically on every push. Results in the dashboard.

**Covered in:** `20-education.md`

---

### Campus Expert Program

Competitive program for student community leaders.

**Apply:** `education.github.com/experts` (opens once or twice yearly)

**Provides:** Training, funding for events, GitHub Expert badge, staff mentorship

**Covered in:** `20-education.md`

---

## Billing and Plans

| Plan           | Price          | Key features                                                                                                      |
| -------------- | -------------- | ----------------------------------------------------------------------------------------------------------------- |
| **Free**       | $0             | Unlimited public and private repos, 2000 Actions minutes/month, 500 MB Packages, 3 collaborators on private repos |
| **Pro**        | $4/month       | 3000 Actions minutes, 2 GB Packages, protected branches, required reviewers, GitHub Insights                      |
| **Team**       | $4/user/month  | All Pro features, org features, 3000 minutes, unlimited collaborators                                             |
| **Enterprise** | $21/user/month | SSO, SAML, audit log streaming, 50000 minutes, GHES, Copilot Enterprise                                           |

**Students:** GitHub Pro free via the Student Developer Pack.

**Billing page:** `github.com/settings/billing`

**Spending limits:** Set a spending limit for Actions and Packages overages at `github.com/settings/billing/spending_limit`

---

## Quick Settings Paths

| Setting                           | URL                                                      |
| --------------------------------- | -------------------------------------------------------- |
| Profile (name, bio, photo)        | `github.com/settings/profile`                            |
| Account security and 2FA          | `github.com/settings/security`                           |
| SSH and GPG keys                  | `github.com/settings/keys`                               |
| Personal Access Tokens            | `github.com/settings/tokens`                             |
| Email addresses                   | `github.com/settings/emails`                             |
| Notification preferences          | `github.com/settings/notifications`                      |
| GitHub Copilot                    | `github.com/settings/copilot`                            |
| Billing and plans                 | `github.com/settings/billing`                            |
| Installed applications            | `github.com/settings/applications`                       |
| OAuth Apps you created            | `github.com/settings/developers`                         |
| GitHub Apps you created           | `github.com/settings/apps`                               |
| Blocked users                     | `github.com/settings/blocked_users`                      |
| Codespaces settings               | `github.com/settings/codespaces`                         |
| **Repository: General**           | `github.com/OWNER/REPO/settings`                         |
| **Repository: Branches**          | `github.com/OWNER/REPO/settings/branches`                |
| **Repository: Actions secrets**   | `github.com/OWNER/REPO/settings/secrets/actions`         |
| **Repository: Actions variables** | `github.com/OWNER/REPO/settings/variables/actions`       |
| **Repository: Environments**      | `github.com/OWNER/REPO/settings/environments`            |
| **Repository: Pages**             | `github.com/OWNER/REPO/settings/pages`                   |
| **Repository: Webhooks**          | `github.com/OWNER/REPO/settings/hooks`                   |
| **Repository: Deploy keys**       | `github.com/OWNER/REPO/settings/keys`                    |
| **Repository: Collaborators**     | `github.com/OWNER/REPO/settings/access`                  |
| **Repository: Code security**     | `github.com/OWNER/REPO/settings/security_analysis`       |
| **Organisation: General**         | `github.com/organizations/ORG/settings/profile`          |
| **Organisation: Members**         | `github.com/orgs/ORG/people`                             |
| **Organisation: Teams**           | `github.com/orgs/ORG/teams`                              |
| **Organisation: Billing**         | `github.com/organizations/ORG/settings/billing/overview` |
| **Organisation: Audit log**       | `github.com/organizations/ORG/settings/audit-log`        |
| **Organisation: Actions**         | `github.com/organizations/ORG/settings/actions/general`  |
| **Organisation: Secrets**         | `github.com/organizations/ORG/settings/secrets/actions`  |
| **Organisation: Copilot**         | `github.com/organizations/ORG/settings/copilot/policy`   |

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
