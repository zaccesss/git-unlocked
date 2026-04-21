# All Forgejo Features Reference

**Difficulty:** 🟢 Beginner | **Time:** Reference document - use as needed

Complete reference for Forgejo features. Use it to locate features, understand requirements and jump to the relevant course file. Features marked **Forgejo exclusive** are not available in Gitea community edition.

---

## Table of Contents

1. [Editions quick reference](#editions-quick-reference)
2. [Repositories and code](#repositories-and-code)
3. [Code review and pull requests](#code-review-and-pull-requests)
4. [Issues and project management](#issues-and-project-management)
5. [Forgejo Actions (CI/CD)](#forgejo-actions-cicd)
6. [Package registry](#package-registry)
7. [User and organisation features](#user-and-organisation-features)
8. [Authentication and security](#authentication-and-security)
9. [Federation (ActivityPub)](#federation-activitypub)
10. [Administration](#administration)
11. [API and integrations](#api-and-integrations)
12. [Forgejo vs Gitea quick reference](#forgejo-vs-gitea-quick-reference)
13. [Quick navigation](#quick-navigation)

---

## Editions quick reference

| Badge | Meaning |
|---|---|
| **Free** | Included in Forgejo community edition (MIT + GPLv3+) |
| **Admin** | Requires instance administrator privileges |
| **Forgejo exclusive** | Not available in Gitea community edition |

Forgejo has no paid enterprise tier. All features are free.

---

## Repositories and code

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| Create repository | + icon > New Repository | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Public/private/internal visibility | Repository creation form | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Initialise with README, .gitignore, licence | Repository creation form | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Clone via HTTPS | Repository page > Clone button | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Clone via SSH | Repository page > Clone > SSH | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Web-based code editor | Repository > file > Edit (pencil) | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Syntax-highlighted code browser | Repository > Code tab | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Blame view | File view > Blame | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Commit history | Repository > Commits | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Branch list | Repository > Branches | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Tag list | Repository > Tags | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Repository topics | Repository page > gear by Topics | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Repository fork | Repository page > Fork button | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Pull mirror (periodic sync from upstream) | + > New Migration | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Push mirror (sync to external) | Settings > Mirror > Push Mirrors | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Migrate from GitHub | + > New Migration > GitHub | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Migrate from GitLab | + > New Migration > GitLab | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Migrate from Gitea/Forgejo | + > New Migration > Gitea | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Generic Git migration | + > New Migration > Git | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Git LFS support | `.gitattributes` + server config | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Releases with asset attachments | Repository > Releases | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Repository wiki | Repository > Wiki tab | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Archive repository | Settings > Danger Zone > Archive | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Transfer repository | Settings > Danger Zone > Transfer | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |

---

## Code review and pull requests

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| Create pull request | Repository > Pull Requests > New | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Draft pull requests | PR creation > Mark as draft | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Inline line comments | PR > Files Changed > + icon | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Multi-line comments | PR > Files Changed > drag across lines | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Code suggestions (apply with one click) | PR comment > suggestion block | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Review (approve/reject/comment) | PR > Files Changed > Review changes | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Resolve conversations | Comment > Resolve Conversation | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Merge commit strategy | PR merge dropdown | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Squash merge strategy | PR merge dropdown | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Rebase merge strategy | PR merge dropdown | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Auto-merge | PR merge dropdown > Auto-merge | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Protected branches | Repository Settings > Protected Branches | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Required approvals | Protected branch settings | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Required status checks | Protected branch settings | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Dismiss stale approvals | Protected branch settings | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| PR templates | `.forgejo/PULL_REQUEST_TEMPLATE.md` | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |
| Cross-fork pull requests | PR creation > compare across forks | **Free** | [04-repositories-and-code-review.md](04-repositories-and-code-review.md) |

---

## Issues and project management

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| Create issue | Repository > Issues > New Issue | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Labels | Issues > Labels | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Organisation labels | Organisation > Settings > Labels | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Milestones | Issues > Milestones | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Assignees | Issue sidebar > Assignees | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| @mentions | Any comment or description | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Issue templates | `.forgejo/ISSUE_TEMPLATE/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Auto-close via commit | `Closes #123` in commit message | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Pinned issues | Issue sidebar > Pin Issue | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Emoji reactions | Comment > smiley face icon | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Issue locking | Issue sidebar > Lock | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Issue dependencies (blocked by) | Issue sidebar > Dependencies | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Cross-repository references | `owner/repo#123` in comments | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Kanban project boards | Repository > Projects | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Organisation projects | Organisation > Projects | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |

---

## Forgejo Actions (CI/CD)

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| Workflow files | `.forgejo/workflows/*.yml` | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| GitHub-compatible workflow syntax | YAML (same as GitHub Actions) | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Push, PR, schedule, dispatch, release triggers | `on:` in YAML | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Multi-job workflows | `jobs:` with dependencies | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Matrix builds | `strategy: matrix:` | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Conditional steps | `if:` keyword | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Workflow artefacts | `upload-artifact` / `download-artifact` | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Dependency caching | `actions/cache` | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Repository secrets | Settings > Actions > Secrets | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Organisation secrets | Organisation > Settings > Actions | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Repository variables | Settings > Actions > Variables | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Use GitHub Marketplace actions | `uses: actions/checkout@v4` | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Self-hosted Forgejo runner | Binary from codeberg.org/forgejo/runner | **Free** | [02-installation.md](02-installation.md) |
| Instance-wide runners | Site Admin > Runners | **Free** (**Admin**) | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Runner labels | Set at runner registration | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |
| Automatic FORGEJO_TOKEN secret | Available in all workflow runs | **Free** | [05-forgejo-actions.md](05-forgejo-actions.md) |

---

## Package registry

| Package type | Notes | Edition | Course file |
|---|---|---|---|
| Container/Docker (OCI) | `forgejo.example.com/{owner}/{repo}` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| npm | `/api/packages/{owner}/npm/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| PyPI | `/api/packages/{owner}/pypi/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| NuGet (.NET) | `/api/packages/{owner}/nuget/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Maven (Java) | `/api/packages/{owner}/maven/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Cargo (Rust) | `/api/packages/{owner}/cargo/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Go modules | `/api/packages/{owner}/go/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Helm charts | `/api/packages/{owner}/helm/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Debian packages | `/api/packages/{owner}/debian/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Alpine packages | `/api/packages/{owner}/alpine/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| RPM packages | `/api/packages/{owner}/rpm/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| RubyGems | `/api/packages/{owner}/rubygems/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Generic packages | `/api/packages/{owner}/generic/` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| 20+ additional formats | Same as Gitea package registry | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |

---

## User and organisation features

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| User registration | Instance homepage > Register | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| User profile | Avatar > Your Profile | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| SSH key management | Settings > SSH / GPG Keys | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Access token creation | Settings > Applications | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Two-factor authentication (TOTP) | Settings > Security | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Federated identity on profile | Profile page (when federation enabled) | **Free** (**Forgejo exclusive**) | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Follow remote repository across instances | Via federation | **Free** (**Forgejo exclusive**) | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Create organisation | + icon > New Organisation | **Free** | [07-administration.md](07-administration.md) |
| Organisation teams | Organisation > Teams | **Free** | [07-administration.md](07-administration.md) |
| Team repository permissions | Team > Repositories | **Free** | [07-administration.md](07-administration.md) |

---

## Authentication and security

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| Local username/password auth | Default | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| LDAP authentication | Site Admin > Authentication Sources > LDAP | **Free** | [07-administration.md](07-administration.md) |
| Active Directory authentication | Site Admin > Authentication Sources > LDAP | **Free** | [07-administration.md](07-administration.md) |
| **SAML 2.0 SSO** | app.ini `[saml]` section | **Free** **Forgejo exclusive** | [07-administration.md](07-administration.md) |
| OAuth2 sign-in (GitHub, GitLab, Google) | Site Admin > Authentication Sources > OAuth2 | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| OpenID Connect (OIDC) | Site Admin > Authentication Sources > OAuth2 | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Two-factor authentication (TOTP) | Settings > Security | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |
| Global 2FA enforcement | app.ini `REQUIRE_SIGNIN_2FA` | **Free** (**Admin**) | [07-administration.md](07-administration.md) |
| GPG commit signing | Settings > SSH / GPG Keys | **Free** | [03-creating-an-account.md](03-creating-an-account.md) |

---

## Federation (ActivityPub)

All federation features are **Forgejo exclusive** - not available in Gitea community edition.

| Feature | Where to find it | Status | Course file |
|---|---|---|---|
| Enable federation | app.ini `[federation] ENABLED = true` | **Free** | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Federated user identity (`@user@instance`) | User profile when federation enabled | Stable | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| NodeInfo endpoint | `/.well-known/nodeinfo` | Stable | [08-api-and-integrations.md](08-api-and-integrations.md) |
| WebFinger endpoint | `/.well-known/webfinger` | Stable | [08-api-and-integrations.md](08-api-and-integrations.md) |
| Follow repository on remote instance | Via web UI or API | Stable | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Cross-instance notifications | Via federation | Stable | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| ActivityPub user actor endpoint | `/api/v1/activitypub/user-id/{id}` | Stable | [08-api-and-integrations.md](08-api-and-integrations.md) |
| ActivityPub repository actor | `/api/v1/activitypub/repository-id/{id}` | Stable | [08-api-and-integrations.md](08-api-and-integrations.md) |
| Cross-instance forking | Via federation | In development | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Cross-instance PR submission | Via federation | In development | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Cross-instance issue commenting | Via federation | In development | [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) |
| Block specific federated instances | app.ini `BLOCKED_INSTANCES` | **Free** (**Admin**) | [07-administration.md](07-administration.md) |

---

## Administration

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| Admin panel | Avatar > Site Administration | **Admin** | [07-administration.md](07-administration.md) |
| Create user | Site Admin > Users > Create User | **Admin** | [07-administration.md](07-administration.md) |
| Suspend user | Site Admin > Users > Edit > deactivate | **Admin** | [07-administration.md](07-administration.md) |
| Impersonate user | Site Admin > Users > Impersonate | **Admin** | [07-administration.md](07-administration.md) |
| Manage all organisations | Site Admin > Organisations | **Admin** | [07-administration.md](07-administration.md) |
| SAML configuration | app.ini `[saml]` | **Admin** | [07-administration.md](07-administration.md) |
| Repository integrity check | Site Admin > Git Repositories > fsck | **Admin** | [07-administration.md](07-administration.md) |
| Queue monitoring | Site Admin > Monitoring | **Admin** | [07-administration.md](07-administration.md) |
| Backup | CLI: `forgejo dump` | **Admin** | [07-administration.md](07-administration.md) |
| Doctor check | CLI: `forgejo doctor check` | **Admin** | [07-administration.md](07-administration.md) |
| Prometheus metrics | `/metrics` endpoint | **Admin** | [07-administration.md](07-administration.md) |
| Health check | `/-/health` | **Free** | [07-administration.md](07-administration.md) |

---

## API and integrations

| Feature | Where to find it | Edition | Course file |
|---|---|---|---|
| REST API | `/api/v1/` | **Free** | [08-api-and-integrations.md](08-api-and-integrations.md) |
| Swagger documentation | `/api/swagger` | **Free** | [08-api-and-integrations.md](08-api-and-integrations.md) |
| Webhooks | Repository Settings > Webhooks | **Free** | [08-api-and-integrations.md](08-api-and-integrations.md) |
| Organisation webhooks | Organisation > Settings > Webhooks | **Free** | [08-api-and-integrations.md](08-api-and-integrations.md) |
| ActivityPub API endpoints | `/api/v1/activitypub/` | **Free** | [08-api-and-integrations.md](08-api-and-integrations.md) |
| Renovate bot integration | External (using `platform: gitea`) | **Free** | [08-api-and-integrations.md](08-api-and-integrations.md) |
| Woodpecker CI | External (native Forgejo support) | **Free** | [08-api-and-integrations.md](08-api-and-integrations.md) |
| ArgoCD integration | External (as Git repository source) | **Free** | [08-api-and-integrations.md](08-api-and-integrations.md) |
| Terraform provider | External (community provider) | **Free** | [08-api-and-integrations.md](08-api-and-integrations.md) |

---

## Forgejo vs Gitea quick reference

| Feature | Forgejo | Gitea community edition |
|---|---|---|
| Licence | MIT (old code) + GPLv3+ (new code) | MIT |
| Governance | Codeberg e.V. (non-profit) | CommitGo Inc. (for-profit) + TOC |
| SAML authentication | **Free** | Enterprise (paid) |
| Windows support | No (dropped 2024) | Yes |
| ActivityPub federation | Active development | Minimal/stalled |
| Enterprise tier | None (all features free) | Yes (proprietary paid add-on) |
| Audit logs | Not yet implemented | Enterprise only |
| IP allowlist | Not yet implemented | Enterprise only |
| SAML | Free, built-in | Enterprise only |
| Core UI | Very similar | Very similar |
| REST API | Mostly compatible | Mostly compatible |
| Package registry | 23+ formats (same) | 23+ formats (same) |
| Actions syntax | GitHub-compatible | GitHub-compatible |
| Default runner | forgejo-runner (codeberg.org/forgejo/runner) | act_runner (gitea project) |
| Workflow file location | `.forgejo/workflows/` (preferred) | `.gitea/workflows/` (preferred) |

---

## Quick navigation

**I want to...**

- **Install Forgejo** → [02-installation.md](02-installation.md)
- **Set up my account, SSH keys, SAML** → [03-creating-an-account.md](03-creating-an-account.md)
- **Manage repositories and code review** → [04-repositories-and-code-review.md](04-repositories-and-code-review.md)
- **Configure CI/CD** → [05-forgejo-actions.md](05-forgejo-actions.md)
- **Track work with issues and boards** → [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md)
- **Publish packages** → [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md)
- **Learn about federation** → [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md)
- **Administer the instance (SAML, LDAP, users)** → [07-administration.md](07-administration.md)
- **Use the API or webhooks** → [08-api-and-integrations.md](08-api-and-integrations.md)
- **Understand Forgejo's history and governance** → [01-what-is-forgejo.md](01-what-is-forgejo.md)
- **Decide between Forgejo and Gitea** → [../gitea/10-gitea-vs-forgejo.md](../gitea/10-gitea-vs-forgejo.md)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
