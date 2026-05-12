# All Gitea Features Reference

**Difficulty:** 🟢 Beginner | **Time:** Reference document - use as needed

This is the complete feature reference for Gitea community edition. Use it to quickly locate features, find where they live in the interface, understand any requirements and jump to the relevant course file. Features are organised by area.

---

## Table of Contents

1. [Gitea editions quick reference](#gitea-editions-quick-reference)
2. [Repositories](#repositories)
3. [Code review and pull requests](#code-review-and-pull-requests)
4. [Issues and project management](#issues-and-project-management)
5. [Gitea Actions (CI/CD)](#gitea-actions-cicd)
6. [Package registry](#package-registry)
7. [User and organisation features](#user-and-organisation-features)
8. [Authentication and security](#authentication-and-security)
9. [Administration](#administration)
10. [API and integrations](#api-and-integrations)
11. [Gitea vs Forgejo quick comparison](#gitea-vs-forgejo-quick-comparison)
12. [Quick navigation](#quick-navigation)

---

## Gitea editions quick reference

| Badge | Meaning |
|---|---|
| **Free** | Included in Gitea community edition (MIT licence) |
| **Enterprise** | Gitea Enterprise only (CommitGo commercial licence) |
| **Admin** | Requires instance administrator privileges |

---

## Repositories

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| Create repository | + icon > New Repository | **Free** | [04-repositories.md](04-repositories.md) |
| Public/private/internal visibility | Repository creation form | **Free** | [04-repositories.md](04-repositories.md) |
| Initialise with README, .gitignore, licence | Repository creation form | **Free** | [04-repositories.md](04-repositories.md) |
| Clone via HTTPS | Repository page > Clone button | **Free** | [04-repositories.md](04-repositories.md) |
| Clone via SSH | Repository page > Clone button > SSH tab | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Web-based code editor | Repository > file > Edit (pencil icon) | **Free** | [04-repositories.md](04-repositories.md) |
| Web-based file creation | Repository > + > New File | **Free** | [04-repositories.md](04-repositories.md) |
| Syntax-highlighted code browser | Repository > Code tab | **Free** | [04-repositories.md](04-repositories.md) |
| Blame view | File view > Blame | **Free** | [04-repositories.md](04-repositories.md) |
| Commit history | Repository > Commits tab | **Free** | [04-repositories.md](04-repositories.md) |
| Commit diff view | Any commit link | **Free** | [04-repositories.md](04-repositories.md) |
| Branch list | Repository > Branches | **Free** | [04-repositories.md](04-repositories.md) |
| Tag list | Repository > Tags | **Free** | [04-repositories.md](04-repositories.md) |
| Repository topics | Repository page > gear by Topics | **Free** | [04-repositories.md](04-repositories.md) |
| Repository fork | Repository page > Fork button | **Free** | [04-repositories.md](04-repositories.md) |
| Repository mirroring (pull) | + > New Migration | **Free** | [04-repositories.md](04-repositories.md) |
| Repository mirroring (push) | Settings > Mirror > Push Mirrors | **Free** | [04-repositories.md](04-repositories.md) |
| Import/migrate from GitHub | + > New Migration > GitHub | **Free** | [04-repositories.md](04-repositories.md) |
| Import/migrate from GitLab | + > New Migration > GitLab | **Free** | [04-repositories.md](04-repositories.md) |
| Import/migrate from Bitbucket | + > New Migration > Bitbucket | **Free** | [04-repositories.md](04-repositories.md) |
| Import/migrate from Gitea/Forgejo | + > New Migration > Gitea | **Free** | [04-repositories.md](04-repositories.md) |
| Generic Git import | + > New Migration > Git | **Free** | [04-repositories.md](04-repositories.md) |
| Git LFS support | `.gitattributes` + server config | **Free** | [04-repositories.md](04-repositories.md) |
| Releases | Repository > Releases | **Free** | [04-repositories.md](04-repositories.md) |
| Release asset attachments | Release creation form | **Free** | [04-repositories.md](04-repositories.md) |
| Repository wiki | Repository > Wiki tab | **Free** | [04-repositories.md](04-repositories.md) |
| Repository star | Star button (top right) | **Free** | [04-repositories.md](04-repositories.md) |
| Repository watch | Watch button (eye icon) | **Free** | [04-repositories.md](04-repositories.md) |
| Archive repository | Settings > Danger Zone > Archive | **Free** | [04-repositories.md](04-repositories.md) |
| Transfer repository | Settings > Danger Zone > Transfer | **Free** | [04-repositories.md](04-repositories.md) |
| Delete repository | Settings > Danger Zone > Delete | **Free** | [04-repositories.md](04-repositories.md) |
| 3D/CAD file preview (STL, OBJ) | File view (automatic) | **Free** (v1.25+) | [04-repositories.md](04-repositories.md) |
| File tree sidebar | Repository code view | **Free** (v1.24+) | [04-repositories.md](04-repositories.md) |

---

## Code review and pull requests

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| Create pull request | Repository > Pull Requests > New | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Draft pull requests | PR creation > Mark as draft | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Inline line comments | PR > Files Changed > + icon on line | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Multi-line comments | PR > Files Changed > drag across lines | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Code suggestions | PR comment > suggest changes icon | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Review submission (approve/reject) | PR > Files Changed > Review changes | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Resolve conversations | Comment > Resolve Conversation | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Dismiss review | PR sidebar > dismiss icon | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Merge commit strategy | PR merge dropdown | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Squash merge strategy | PR merge dropdown | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Rebase merge strategy | PR merge dropdown | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Auto-merge | PR merge dropdown > Auto-merge | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Protected branches | Repository Settings > Protected Branches | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Required approvals on protected branch | Protected branch settings | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Required status checks on protected branch | Protected branch settings | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Dismiss stale approvals | Protected branch settings | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| PR templates | `.gitea/PULL_REQUEST_TEMPLATE.md` | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Multiple PR templates | `.gitea/PULL_REQUEST_TEMPLATE/` directory | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |
| Cross-fork pull requests | PR creation > compare across forks | **Free** | [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) |

---

## Issues and project management

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| Create issue | Repository > Issues > New Issue | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Labels (create, apply, filter) | Issues > Labels | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Organisation-level labels | Organisation > Settings > Labels | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Milestones | Issues > Milestones | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Assignees | Issue sidebar > Assignees | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| @mentions | Any comment or description | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Issue templates | `.gitea/ISSUE_TEMPLATE/` | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Auto-close via commit message | `Closes #123` in commit | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Pinned issues | Issue > Pin Issue (maintainers) | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Emoji reactions | Comment > smiley face icon | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Issue locking | Issue > Lock (maintainers) | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Issue dependencies (blocked by) | Issue sidebar > Dependencies | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Cross-repository references | `owner/repo#123` in any comment | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Kanban project boards | Repository > Projects | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Organisation projects | Organisation > Projects | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Project board columns | Project board > Add column | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |
| Add issues to project | Issue sidebar > Projects | **Free** | [07-issues-and-projects.md](07-issues-and-projects.md) |

---

## Gitea Actions (CI/CD)

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| Workflow files | `.gitea/workflows/*.yml` | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| GitHub-compatible workflow syntax | Same as GitHub Actions YAML | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Push trigger | `on: push:` | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Pull request trigger | `on: pull_request:` | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Schedule trigger (cron) | `on: schedule:` | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Manual trigger (workflow_dispatch) | `on: workflow_dispatch:` | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Release trigger | `on: release:` | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Multi-job workflows | `jobs:` with multiple jobs | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Job dependencies | `needs:` keyword | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Matrix builds | `strategy: matrix:` | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Conditional steps | `if:` keyword | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Workflow artefacts | `upload-artifact` / `download-artifact` | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Dependency caching | `actions/cache` | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Repository secrets | Settings > Actions > Secrets | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Organisation secrets | Organisation > Settings > Actions > Secrets | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Repository variables | Settings > Actions > Variables | **Free** (v1.22+) | [06-gitea-actions.md](06-gitea-actions.md) |
| Use GitHub Marketplace actions | `uses: actions/checkout@v4` | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Self-hosted act_runner | External binary, separate install | **Free** | [02-installation.md](02-installation.md) |
| Instance-wide runners | Site Admin > Runners | **Free** (**Admin**) | [06-gitea-actions.md](06-gitea-actions.md) |
| Repository runners | Settings > Actions > Runners | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Runner labels | Set at runner registration | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |

---

## Package registry

| Package type | Registry URL pattern | Edition | Course file |
|---|---|---|---|
| Container/Docker (OCI) | `gitea.example.com/{owner}` | **Free** | [08-packages.md](08-packages.md) |
| npm | `/api/packages/{owner}/npm/` | **Free** | [08-packages.md](08-packages.md) |
| PyPI | `/api/packages/{owner}/pypi/` | **Free** | [08-packages.md](08-packages.md) |
| NuGet (.NET) | `/api/packages/{owner}/nuget/` | **Free** | [08-packages.md](08-packages.md) |
| Maven (Java) | `/api/packages/{owner}/maven/` | **Free** | [08-packages.md](08-packages.md) |
| Cargo (Rust) | `/api/packages/{owner}/cargo/` | **Free** | [08-packages.md](08-packages.md) |
| Go modules | `/api/packages/{owner}/go/` | **Free** | [08-packages.md](08-packages.md) |
| Helm charts | `/api/packages/{owner}/helm/` | **Free** | [08-packages.md](08-packages.md) |
| Debian packages | `/api/packages/{owner}/debian/` | **Free** | [08-packages.md](08-packages.md) |
| Alpine packages | `/api/packages/{owner}/alpine/` | **Free** | [08-packages.md](08-packages.md) |
| RPM packages | `/api/packages/{owner}/rpm/` | **Free** | [08-packages.md](08-packages.md) |
| RubyGems | `/api/packages/{owner}/rubygems/` | **Free** | [08-packages.md](08-packages.md) |
| Generic packages | `/api/packages/{owner}/generic/` | **Free** | [08-packages.md](08-packages.md) |
| Arch packages | `/api/packages/{owner}/arch/` | **Free** | [08-packages.md](08-packages.md) |
| Composer (PHP) | `/api/packages/{owner}/composer/` | **Free** | [08-packages.md](08-packages.md) |
| Conda | `/api/packages/{owner}/conda/` | **Free** | [08-packages.md](08-packages.md) |
| CRAN (R) | `/api/packages/{owner}/cran/` | **Free** | [08-packages.md](08-packages.md) |
| Pub (Dart/Flutter) | `/api/packages/{owner}/pub/` | **Free** | [08-packages.md](08-packages.md) |
| Swift | `/api/packages/{owner}/swift/` | **Free** | [08-packages.md](08-packages.md) |
| Vagrant | `/api/packages/{owner}/vagrant/` | **Free** | [08-packages.md](08-packages.md) |
| Package browsing (web UI) | User/org profile > Packages | **Free** | [08-packages.md](08-packages.md) |
| Delete package versions | Package page > Delete version | **Free** | [08-packages.md](08-packages.md) |

---

## User and organisation features

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| User registration | Instance homepage > Register | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| User profile | Avatar > Your Profile | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| SSH key management | Settings > SSH / GPG Keys | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Access token creation | Settings > Applications | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Two-factor authentication | Settings > Security | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Email notification settings | Settings > Notifications | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| UI theme selection | Settings > Appearance | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Language selection | Settings > Appearance | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Create organisation | + icon > New Organisation | **Free** | [09-administration.md](09-administration.md) |
| Organisation teams | Organisation > Teams | **Free** | [09-administration.md](09-administration.md) |
| Team repository permissions | Team > Repositories | **Free** | [09-administration.md](09-administration.md) |
| Organisation member management | Organisation > Members | **Free** | [09-administration.md](09-administration.md) |
| Organisation-level secrets | Organisation > Settings > Actions | **Free** | [06-gitea-actions.md](06-gitea-actions.md) |
| Fork policy per organisation | Organisation > Settings | **Free** | [09-administration.md](09-administration.md) |

---

## Authentication and security

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| Local username/password auth | Default | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| LDAP authentication | Site Admin > Authentication Sources > LDAP | **Free** | [09-administration.md](09-administration.md) |
| Active Directory authentication | Site Admin > Authentication Sources > LDAP | **Free** | [09-administration.md](09-administration.md) |
| OAuth2 sign-in (GitHub, GitLab, Google, Microsoft) | Site Admin > Authentication Sources > OAuth2 | **Free** | [09-administration.md](09-administration.md) |
| OpenID Connect (OIDC) | Site Admin > Authentication Sources > OAuth2 | **Free** | [09-administration.md](09-administration.md) |
| SAML 2.0 SSO | N/A in community edition | **Enterprise** | [10-gitea-vs-forgejo.md](10-gitea-vs-forgejo.md) |
| Two-factor authentication (TOTP) | Settings > Security | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Global 2FA enforcement | app.ini `REQUIRE_SIGNIN_2FA` | **Free** (**Admin**) (v1.24+) | [09-administration.md](09-administration.md) |
| GPG commit signing | Settings > SSH / GPG Keys | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Audit logs | N/A in community edition | **Enterprise** | [10-gitea-vs-forgejo.md](10-gitea-vs-forgejo.md) |
| IP allowlist | N/A in community edition | **Enterprise** | [10-gitea-vs-forgejo.md](10-gitea-vs-forgejo.md) |
| Dependency scanning | N/A in community edition | **Enterprise** | [10-gitea-vs-forgejo.md](10-gitea-vs-forgejo.md) |
| Branch protection inheritance | N/A in community edition | **Enterprise** | [10-gitea-vs-forgejo.md](10-gitea-vs-forgejo.md) |

---

## Administration

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| Admin panel | Avatar > Site Administration | **Admin** | [09-administration.md](09-administration.md) |
| Create user (admin) | Site Admin > Users > Create User | **Admin** | [09-administration.md](09-administration.md) |
| Suspend user | Site Admin > Users > Edit > deactivate | **Admin** | [09-administration.md](09-administration.md) |
| Delete user | Site Admin > Users > Edit > Delete | **Admin** | [09-administration.md](09-administration.md) |
| Impersonate user | Site Admin > Users > Impersonate | **Admin** | [09-administration.md](09-administration.md) |
| Manage all organisations | Site Admin > Organisations | **Admin** | [09-administration.md](09-administration.md) |
| View all repositories | Site Admin > Repositories | **Admin** | [09-administration.md](09-administration.md) |
| Repository integrity check | Site Admin > Git Repositories > fsck | **Admin** | [09-administration.md](09-administration.md) |
| Email send log | Site Admin > Emails | **Admin** | [09-administration.md](09-administration.md) |
| Queue monitoring | Site Admin > Monitoring | **Admin** | [09-administration.md](09-administration.md) |
| Backup (gitea dump) | CLI: `gitea dump` | **Admin** | [09-administration.md](09-administration.md) |
| Restore (gitea restore) | CLI: `gitea restore` | **Admin** | [09-administration.md](09-administration.md) |
| Doctor check | CLI: `gitea doctor check` | **Admin** | [09-administration.md](09-administration.md) |
| Database migration | CLI: `gitea migrate` | **Admin** | [09-administration.md](09-administration.md) |
| Prometheus metrics | `/metrics` endpoint | **Admin** | [09-administration.md](09-administration.md) |
| Health check | `/-/health` endpoint | **Free** | [09-administration.md](09-administration.md) |

---

## API and integrations

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| REST API | `/api/v1/` | **Free** | [11-api-and-integrations.md](11-api-and-integrations.md) |
| Swagger documentation | `/api/swagger` | **Free** | [11-api-and-integrations.md](11-api-and-integrations.md) |
| Webhooks | Repository Settings > Webhooks | **Free** | [11-api-and-integrations.md](11-api-and-integrations.md) |
| Organisation webhooks | Organisation > Settings > Webhooks | **Free** | [11-api-and-integrations.md](11-api-and-integrations.md) |
| Slack webhook format | Webhook > Add Webhook > Slack | **Free** | [11-api-and-integrations.md](11-api-and-integrations.md) |
| Discord webhook format | Webhook > Add Webhook > Discord | **Free** | [11-api-and-integrations.md](11-api-and-integrations.md) |
| Drone CI integration | External (native Gitea support) | **Free** | [11-api-and-integrations.md](11-api-and-integrations.md) |
| Jenkins integration | External (Gitea plugin) | **Free** | [11-api-and-integrations.md](11-api-and-integrations.md) |
| Renovate bot | External (Gitea platform support) | **Free** | [11-api-and-integrations.md](11-api-and-integrations.md) |
| Terraform provider | External (community provider) | **Free** | [11-api-and-integrations.md](11-api-and-integrations.md) |
| ActivityPub federation | Not available in Gitea | N/A (Forgejo only) | [10-gitea-vs-forgejo.md](10-gitea-vs-forgejo.md) |

---

## Gitea vs Forgejo quick comparison

| Feature | Gitea (community) | Forgejo |
|---|---|---|
| Licence | MIT | MIT (older code), GPLv3+ (new code from v9+) |
| Governance | CommitGo Inc. (for-profit) + elected TOC | Codeberg e.V. (non-profit) |
| SAML authentication | Enterprise (paid) | Free (built-in) |
| Windows support | Yes | No (dropped 2024) |
| ActivityPub federation | Stalled/minimal | Actively developed |
| Enterprise tier | Yes (CommitGo) | No |
| Version numbering | v1.25.x (April 2026) | v14.x (April 2026) |
| Default git hosting | gitea.com | codeberg.org |
| Core UI | Very similar | Very similar |
| REST API | Compatible for core operations | Compatible for core operations |

---

## Quick navigation

**I want to...**

- **Install Gitea** → [02-installation.md](02-installation.md)
- **Set up my account and SSH keys** → [03-creating-an-account.md](03-creating-an-account.md)
- **Create and manage repositories** → [04-repositories.md](04-repositories.md)
- **Set up code review workflows** → [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md)
- **Configure CI/CD** → [06-gitea-actions.md](06-gitea-actions.md)
- **Track work with issues and boards** → [07-issues-and-projects.md](07-issues-and-projects.md)
- **Host private packages** → [08-packages.md](08-packages.md)
- **Administer a Gitea instance** → [09-administration.md](09-administration.md)
- **Decide between Gitea and Forgejo** → [10-gitea-vs-forgejo.md](10-gitea-vs-forgejo.md)
- **Use the REST API or webhooks** → [11-api-and-integrations.md](11-api-and-integrations.md)
- **Understand Gitea's history** → [01-what-is-gitea.md](01-what-is-gitea.md)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
