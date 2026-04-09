# Changelog

All notable changes to git-unlocked are documented here.

This project follows [Semantic Versioning](https://semver.org). The format is based on [Keep a Changelog](https://keepachangelog.com).

---

## Version format

```
MAJOR.MINOR.PATCH

MAJOR - significant restructure or complete section overhaul
MINOR - new files, new sections or meaningful content additions
PATCH - typo fixes, small corrections, link updates
```

| Tag       | Meaning                                 |
| --------- | --------------------------------------- |
| `Added`   | New files or sections                   |
| `Updated` | Existing content improved or expanded   |
| `Fixed`   | Typos, errors or broken links corrected |
| `Removed` | Content removed                         |
| `Renamed` | Files or folders renamed                |

---

## [Unreleased]

In progress or planned - not yet in a release.

- All `ides/` folder content
- All `terminal/` folder content
- All `real-world/` folder content
- All `reference/` folder content
- All `resources/` folder content
- `first-contribution/` sandbox
- `assets/` banner and diagrams
- `introduction/02-how-to-use-this-course.md` - full rewrite once all folders complete
- Codespaces dev container setup
- Root-level setup documentation

---

## [0.4.0] - 2026-04-09

### Added

- `gitlab/00-gitlab-overview.md` - folder navigation with terminology mapping, level-based learning paths and tier notes
- `gitlab/01-what-is-gitlab.md` - founding story (Zaporozhets 2011, Sijbrandij 2012), Y Combinator W2015, the Microsoft/GitHub moment (June 2018, 100K repos migrated), IPO October 2021 NASDAQ GTLB, Bill Staples as CEO (December 2024), Siva Padisetty as CTO (January 2026), 40M+ users, 50%+ Fortune 100, GitLab 18.10, single-application philosophy, CE vs EE, plans, community programmes
- `gitlab/02-creating-an-account.md` - signup and username rules, identity verification (risk-based, credit card for shared runners), profile setup, TOTP setup, WebAuthn hardware keys, passkeys (GA 18.9, passwordless sign-in), SSH key generation on all three OS, PAT creation with full scope table, free tier limits (5 users/private group, 400 compute minutes, 10 GiB/project, 3 top-level groups post January 2026), credit card requirement explanation
- `gitlab/03-gitlab-vs-github.md` - philosophical comparison (single application vs marketplace), full feature comparison tables, CI/CD comparison (400 vs 2,000 free minutes), security features comparison, AI comparison (Duo vs Copilot), pricing tables, pricing controversy (merge approvals/CODEOWNERS paywalled, $19 to $29 Premium increase), self-hosting comparison (CE free vs GHE $21/user), community and ecosystem, when to choose each platform, using both together
- `gitlab/04-projects-and-namespaces.md` - project vs repository distinction, namespace types (personal/group), creating groups, subgroups (up to 20 levels deep), role inheritance, creating projects (blank/template/import), project settings by section, visibility levels (Public/Internal/Private), forking and fork visibility, syncing forks, project templates (built-in and group-level custom), Web IDE, importing from GitHub, archiving/transferring/deleting, comparison with GitHub orgs and teams
- `gitlab/05-merge-requests.md` - creating MRs (terminal link, UI, from issue, glab CLI), MR interface tabs (Overview/Commits/Pipelines/Changes), draft MRs (Draft: prefix), reviewing (inline comments, suggested changes, batch suggestions), resolve threads, approvals (Free vs Premium), approval rules (prevent author approval, reset on new commits), CODEOWNERS (Premium, all syntax including sections/optional sections), merge methods (merge commit/semi-linear/fast-forward/squash), auto-merge, merge trains (Premium), merged results pipelines, MR templates, closing issues automatically, MR dependencies (Premium), reverting, glab CLI commands
- `gitlab/06-gitlab-cicd.md` - how CI/CD works, runners (shared/group/project, all three OS install and register), executors, first pipeline, stages (.pre/.post), jobs (all keywords in detail), script, image/services, before_script/after_script, rules (if/changes/exists/when), workflow rules (duplicate pipeline prevention with auto_cancel), predefined variables (full tables: commit/pipeline/project/MR/registry/user), custom variables (4 levels and precedence), artifacts (paths/expire/when/reports), caching (key/file-based/policy/multiple), needs/DAG, parallel matrix (max 200), extends/hidden jobs, include (local/project/remote/template/component, max 150), environments, dynamic environments, review apps, MR pipelines, security scanning templates (SAST/secret detection/dependency/container), scheduled pipelines, child and parent pipelines, pipeline editor with linting, common mistakes
- `gitlab/07-gitlab-pages.md` - how Pages works (public/ directory, pages: true since 17.5), old vs new job naming, built-in templates, framework pipelines (Jekyll/Hugo/MkDocs/Gatsby/Sphinx/Eleventy), testing build on MRs, URL structure, unique domains, custom domains (CNAME/A record and TXT verification), HTTPS (Let's Encrypt auto), Force HTTPS, access control (restrict to members), limits (100 MiB, 200,000 files), Pages on self-managed, Pages vs GitHub Pages comparison
- `gitlab/08-issues-and-planning.md` - issues (work items framework), creating issues (all methods), all issue fields (assignees, labels, milestones, due date, weight, health status, iteration, epic), confidential issues, sub-issues, task list checkboxes, linked issues (relates to/blocks/blocked by), time tracking, quick actions (complete reference table covering state/assignment/labels/planning/relationships/time/issue management/MR-specific), labels (project vs group), scoped labels (Premium, key::value), milestones (burndown/burnup charts), issue boards (Kanban, list types, board scope, multiple boards), epics (Premium, hierarchy, child epics), roadmaps (Premium, timeline presets, filter options), iterations (Premium, cadences, burndown), issue and MR templates, Service Desk (email-to-issue, template, converting issues), GitLab Wiki (project/group, sidebar/footer, cloning locally), GitLab Snippets (versioned, multi-file up to 10, embed, clone as repository)
- `gitlab/09-security-features.md` - SAST (Semgrep-based, supported languages, Advanced SAST cross-file/function in Ultimate, false positive detection via Duo 18.10), Secret Detection (90+ patterns, push protection, auto-revocation, what to do when found), Dependency Scanning (supported ecosystems, Trivy, CycloneDX SBOM), Container Scanning (Trivy, what it finds, reducing vulnerabilities), IaC Scanning (Terraform/CloudFormation/K8s/Ansible/Dockerfile), DAST v5 browser-based (Ultimate, replaces proxy-based in 17.3), API Security Testing, Fuzz Testing (coverage-guided and web API, Ultimate, fails pipeline on crash), License Compliance (approved/denied policies), Vulnerability Management (Detected/Confirmed/Dismissed/Resolved statuses, creating issues from vulnerabilities, group security dashboard), Security Policies (scan execution, MR approval, pipeline execution), GitLab Advisory Database (GLAD, CVE Numbering Authority), comparison with GitHub security
- `gitlab/10-gitlab-duo.md` - GitLab Duo overview and philosophy, Duo tiers (Core/Pro/Enterprise) with pricing, credits system (12/user Premium, 24/user Ultimate, $1 on-demand), Code Suggestions (inline completions, 25+ languages, accepting/rejecting), Duo Chat (non-agentic vs agentic modes, slash commands, context), Duo Agent Platform (GA January 15 2026, agents vs chat difference, governance), Planner Agent, Security Analyst Agent, Root Cause Analysis, Vulnerability Resolution, AI Code Review, Flows (Developer Flow/Convert to CI/Fix CI/Code Review), AI Catalog, MCP Client (Jira/Slack/Confluence/Grafana), AI models (Anthropic Claude/Google Vertex AI/Fireworks Codestral), IDE extensions (VS Code/JetBrains/Visual Studio/Neovim/Web IDE), privacy (zero-day retention, no training on private code, context exclusion, AI Transparency Center), self-hosted models (vLLM/AWS Bedrock/Azure OpenAI), Duo vs Copilot comparison
- `gitlab/11-self-hosting.md` - CE vs EE (why always install EE), why self-host (data sovereignty, air-gapped, cost at scale, control), system requirements (minimum 4vCPU/8GB, 1K reference: 8vCPU/16GB), reference architectures (1K-50K, HA, Cloud Native Hybrid), Linux package installation (Ubuntu/Debian, RHEL), Docker installation (Docker Compose, pinning versions), Kubernetes Helm chart, initial configuration (gitlab.rb), email/SMTP configuration (Gmail/SendGrid/SES), HTTPS with Let's Encrypt, manual SSL, LDAP configuration, SAML configuration, backup (what is included, what is NOT included, automated cron, object storage), restore (same version required, stop services first), upgrades (upgrade paths, required stops, background migrations, zero-downtime), GitLab Runner on self-managed, Pages on self-managed, Container Registry on self-managed, monitoring (Prometheus, Grafana, health checks), self-managed vs GitLab.com differences table
- `gitlab/12-gitlab-cli.md` - glab overview, installation on all three OS (Homebrew/winget/Scoop/apt/dnf/pacman/snap/binary), authentication (browser OAuth and PAT, environment variables), configuration (global and per-repo), repo commands (clone/create/view/fork/archive/delete/list), MR commands (create with --fill, list with filters, view, checkout, approve, note, merge with options, rebase, close, update), issue commands (create/list/view/close/note/subscribe/transfer), CI/CD pipeline commands (status/view interactive/trace live logs/run/retry/cancel/artifact), variable commands (list/set with masked and protected/delete), release commands (create/list/upload assets), label/snippet/runner/schedule commands, glab api (GET/POST/PUT/DELETE, --paginate, jq integration), glab duo ask, self-managed usage, multiple instances, shell completions (Bash/Zsh/PowerShell), glab vs gh comparison
- `gitlab/13-groups-and-permissions.md` - groups overview (URL namespace/access container/settings container), creating and managing groups (rename/transfer/delete warnings), all roles in detail (Guest/Reporter/Developer/Maintainer/Owner/Planner/Minimal Access/Security Manager), role inheritance through subgroups (elevation but not reduction), adding and removing members, membership expiry best practices, group access tokens (Premium GitLab.com, any licence self-managed, bot user, use cases), project access tokens, deploy tokens (scopes/using/vs access tokens), deploy keys (generating/shared deploy keys), SAML SSO (why use it, GitLab.com group-level setup, attribute mapping, enforcement options, self-managed instance-level), SCIM provisioning (Entra ID/Okta/OneLogin), group CI/CD variables (cascade, environment-scoped variables, common patterns), group runners (inheritance, controlling access), visibility levels and implications, audit events (what is logged, filtering, exporting), structuring a real organisation (three patterns with pros/cons)
- `gitlab/14-gitlab-api-and-integrations.md` - REST API overview (base URL, versioning, HTTP status codes), making API requests (curl examples, project ID vs path), authentication (PATs, group/project access tokens, OAuth 2.0 flows, CI_JOB_TOKEN, HTTPS Git operations), pagination (offset-based and keyset, fetching all pages in bash), key REST endpoints (projects/MRs/issues/CI/users/groups/releases), GraphQL API (endpoint, interactive explorer, example queries, mutations, REST vs GraphQL guide), webhooks (scope/events/configuration/payload/verifying authenticity/auto-disable after 4 failures), built-in integrations (Jira/Slack GitLab app with slash commands/Microsoft Teams/Jenkins/Datadog and others), Container Registry (authentication/build and push in CI/managing images/cleanup policies/self-managed config), Package Registry (npm/PyPI/Maven/Generic with code examples), API in CI/CD pipelines (job token, masked variables, release-cli), rate limits (current limits table, handling 429 with retry), python-gitlab library

### Updated

- `CHANGELOG.md` - added v0.4.0 entry for gitlab/ folder
- `ROADMAP.md` - marked gitlab/ as complete, updated current status to v0.4.0

---

## [0.3.0] - 2026-04-09

### Added

- `github/18-badges-and-achievements.md` - every GitHub badge, how to earn each, tier thresholds (bronze/silver/gold), the Arctic Code Vault Contributor badge, the Pro badge
- `github/19-copilot.md` - free tier (2,000 completions/50 chat), Pro, Business and Enterprise, agent mode, Copilot in the CLI, IDE support, privacy settings
- `github/20-education.md` - Student Developer Pack (application, renewal, what is included), GitHub Classroom, Campus Expert programme
- `github/21-cli.md` - gh installation on all three OS, authentication, all major command groups (repo/pr/issue/run/gist/secret), aliases, gh copilot suggest/explain
- `github/22-mobile.md` - iOS and Android apps, review and comment on PRs, manage notifications, push protection on mobile, limitations vs desktop
- `github/23-collaborators-teams-orgs.md` - adding collaborators, organisations, teams (nested teams, team permissions, CODEOWNERS), outside collaborators, repository roles
- `github/24-advanced.md` - REST API, GraphQL API, webhooks, GitHub Apps vs OAuth Apps, GitHub Sponsors, Gists, GitHub Search (advanced syntax), GitHub Wikis
- `github/25-what-should-not-be-on-github.md` - secrets, PII, large binary files, generated files, credentials, what to do if you accidentally pushed sensitive data, git-filter-repo
- `github/26-platform-comparison.md` - GitHub Web vs Desktop vs Mobile vs CLI vs Codespaces - what each surface can and cannot do, side-by-side comparison table
- `github/27-all-github-features-reference.md` - complete reference for every GitHub feature with settings paths and course links

### Updated

- `github/00-github-overview.md` - updated table to include files 18-27, added new learning path sections

---

## [0.2.0] - 2026-03-22

### Added

- `CODE_OF_CONDUCT.md` - Contributor Covenant 3.0
- `SECURITY.md` - security reporting policy
- `SUPPORT.md` - how to get help
- `.editorconfig` - consistent editor settings across all IDEs
- `.markdownlint.json` - markdown linting rules
- `.github/workflows/markdownlint.yml` - automated markdown linting on every PR
- `.github/workflows/linkcheck.yml` - weekly automated link checking
- `.github/ISSUE_TEMPLATE/bug_report.md` - structured bug reports
- `.github/ISSUE_TEMPLATE/content_suggestion.md` - structured content suggestions
- `.github/ISSUE_TEMPLATE/config.yml` - issue template config with Discussions link
- `.github/PULL_REQUEST_TEMPLATE.md` - PR checklist
- Contributors section in README using contrib.rocks
- Open in GitHub Codespaces badge in README

### Updated

- All files updated to new formatting standard - GitHub alert syntax replaces emoji callouts, minimal emoji throughout, proper bold and italic usage
- `introduction/03-setting-up.md` - GitHub and GitLab account creation added as Steps 2 and 3, full step reorder, new common mistakes
- `CONTRIBUTING.md` - new emoji policy, callout syntax guide, branding rules
- `README.md` - Contributor Covenant badge, Codespaces button, contrib.rocks grid, cleaner structure

---

## [0.1.0] - 2026-03-22

### Added

- Initial repository at [github.com/zaccessss/git-unlocked](https://github.com/zaccessss/git-unlocked)
- `README.md`, `LICENSE`, `CONTRIBUTING.md`, `CHANGELOG.md`, `ROADMAP.md`, `FAQ.md`, `HALL_OF_FAME.md`
- All folder structure with `.gitkeep` files
- `introduction/01-welcome.md`
- `introduction/02-how-to-use-this-course.md`
- `introduction/03-setting-up.md`

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
