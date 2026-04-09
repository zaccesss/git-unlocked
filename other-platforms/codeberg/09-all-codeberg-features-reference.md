# All Codeberg Features Reference

**Difficulty:** 🟢 Beginner | **Time:** Reference document - use as needed

Complete reference for Codeberg features. Codeberg runs Forgejo, so most Forgejo features apply. This reference focuses on what is specific to Codeberg as a hosted service: limits, unique features and the Codeberg-specific tools (Woodpecker CI, Codeberg Pages).

---

## Table of Contents

1. [Platform quick reference](#platform-quick-reference)
2. [Account features](#account-features)
3. [Repositories and code](#repositories-and-code)
4. [Code review and pull requests](#code-review-and-pull-requests)
5. [Issues and project management](#issues-and-project-management)
6. [CI/CD: Woodpecker CI](#cicd-woodpecker-ci)
7. [Codeberg Pages](#codeberg-pages)
8. [Package registry and releases](#package-registry-and-releases)
9. [Organisations and teams](#organisations-and-teams)
10. [Authentication and security](#authentication-and-security)
11. [Federation (ActivityPub)](#federation-activitypub)
12. [API and integrations](#api-and-integrations)
13. [Codeberg vs GitHub vs GitLab.com quick comparison](#codeberg-vs-github-vs-gitlabcom-quick-comparison)
14. [Quick navigation](#quick-navigation)

---

## Platform quick reference

| Aspect | Detail |
|---|---|
| **Platform** | Forgejo (community fork of Gitea) |
| **Operator** | Codeberg e.V. (German non-profit) |
| **Free for** | Open source projects |
| **Private repos** | Personal accounts: yes. Org accounts: requires financial support |
| **CI/CD** | Woodpecker CI (opt-in, separate service at ci.codeberg.org) |
| **Static hosting** | Codeberg Pages (`pages` branch) |
| **Package registry** | 23+ formats (Forgejo package registry) |
| **AI features** | None |
| **Mobile app** | None |
| **Data jurisdiction** | EU (Germany) |
| **Funding model** | Donations (no ads, no tracking) |

---

## Account features

| Feature | Where to find it | Notes | Course file |
|---|---|---|---|
| Registration | codeberg.org > Register | Email verification required | [02-creating-an-account.md](02-creating-an-account.md) |
| Username | Chosen at registration | Also your federated identity `@user@codeberg.org` | [02-creating-an-account.md](02-creating-an-account.md) |
| Profile page | codeberg.org/username | Public profile with repos, activity | [02-creating-an-account.md](02-creating-an-account.md) |
| SSH keys | Settings > SSH / GPG Keys | ed25519 recommended | [02-creating-an-account.md](02-creating-an-account.md) |
| Access tokens | Settings > Applications | Scoped tokens; copy once | [02-creating-an-account.md](02-creating-an-account.md) |
| Two-factor authentication | Settings > Security | TOTP + recovery codes | [02-creating-an-account.md](02-creating-an-account.md) |
| Federated identity | On profile page | `@username@codeberg.org` in Fediverse | [02-creating-an-account.md](02-creating-an-account.md) |
| Email privacy | Settings > Privacy | No-reply address for web commits | [02-creating-an-account.md](02-creating-an-account.md) |
| Notification settings | Settings > Notifications | Per-repository watch levels | [02-creating-an-account.md](02-creating-an-account.md) |
| Theme / language | Settings > Appearance | Light, Dark, Auto and others | [02-creating-an-account.md](02-creating-an-account.md) |

---

## Repositories and code

| Feature | Where to find it | Notes | Course file |
|---|---|---|---|
| Create repository | + > New Repository | Public recommended for open source | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Clone via HTTPS | Repository > Clone > HTTPS | Use access token as password | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Clone via SSH | Repository > Clone > SSH | Requires SSH key on account | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Import from GitHub | + > New Migration > GitHub | Imports code + issues + PRs + releases | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Import from GitLab/Gitea | + > New Migration | Code + metadata | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Pull mirror (sync from upstream) | + > New Migration > enable Mirror | Read-only on Codeberg | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Push mirror (sync to external) | Settings > Mirror > Push Mirrors | Keep GitHub copy in sync | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Repository topics | Repository page > gear | Improves Explore discoverability | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Web-based code editor | File view > Edit (pencil) | For small changes | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Repository fork | Repository > Fork button | Anyone can fork public repos | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Git LFS | `.gitattributes` + account config | Fair use limits apply | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Repository wiki | Repository > Wiki tab | Markdown; cloneable as Git repo | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Archive repository | Settings > Danger Zone | Read-only, preserved | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Transfer repository | Settings > Danger Zone | Breaks existing URLs | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |

---

## Code review and pull requests

| Feature | Where to find it | Notes | Course file |
|---|---|---|---|
| Create pull request | Repository > Pull Requests > New | Banner appears after branch push | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Draft pull request | PR creation > Mark as draft | Signal work in progress | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Inline code comments | PR > Files Changed > + icon | Line-level review feedback | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Code suggestions | PR comment > suggestion block | Author applies with one click | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Review (approve/reject) | PR > Review changes button | Approve, request changes or comment | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Auto-merge | PR > merge dropdown > Auto-merge | Merges when conditions met | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Squash merge | PR merge dropdown | Clean linear history | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Rebase merge | PR merge dropdown | Linear with all commits | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| Protected branches | Settings > Protected Branches | Push protection, required approvals | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |
| PR templates | `.forgejo/PULL_REQUEST_TEMPLATE.md` | Pre-fills PR description | [03-repositories-and-code-review.md](03-repositories-and-code-review.md) |

---

## Issues and project management

| Feature | Where to find it | Notes | Course file |
|---|---|---|---|
| Create issue | Repository > Issues > New Issue | Public repos: anyone with Codeberg account | [04-issues-and-projects.md](04-issues-and-projects.md) |
| Labels | Issues > Labels | Create standard sets | [04-issues-and-projects.md](04-issues-and-projects.md) |
| Organisation labels | Organisation > Issues > Labels | Shared across all org repos | [04-issues-and-projects.md](04-issues-and-projects.md) |
| Milestones | Issues > Milestones | Group issues for releases | [04-issues-and-projects.md](04-issues-and-projects.md) |
| Assignees | Issue sidebar | Single or multiple assignees | [04-issues-and-projects.md](04-issues-and-projects.md) |
| @mentions | Any comment | Sends notification | [04-issues-and-projects.md](04-issues-and-projects.md) |
| Issue templates | `.forgejo/ISSUE_TEMPLATE/` | Bug report, feature request templates | [04-issues-and-projects.md](04-issues-and-projects.md) |
| Auto-close via commit | `Closes #N` in commit/PR | Closes when merged to default branch | [04-issues-and-projects.md](04-issues-and-projects.md) |
| Good first issue label | Label management | Attracts new contributors | [04-issues-and-projects.md](04-issues-and-projects.md) |
| Pinned issues | Issue sidebar > Pin Issue | Up to 3, shown at top of list | [04-issues-and-projects.md](04-issues-and-projects.md) |
| Emoji reactions | Comment > smiley icon | Show agreement without comments | [04-issues-and-projects.md](04-issues-and-projects.md) |
| Issue dependencies | Issue sidebar > Dependencies | Blocked by / blocks relationships | [04-issues-and-projects.md](04-issues-and-projects.md) |
| Kanban project boards | Repository > Projects | Drag-and-drop cards | [04-issues-and-projects.md](04-issues-and-projects.md) |
| Organisation project boards | Organisation > Projects | Cross-repo tracking | [04-issues-and-projects.md](04-issues-and-projects.md) |

---

## CI/CD: Woodpecker CI

| Feature | Where to find it | Notes | Course file |
|---|---|---|---|
| Connect Woodpecker | ci.codeberg.org > Login | OAuth2 with Codeberg account | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Enable repository | ci.codeberg.org > + Add repository | Must enable per repository | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Pipeline file | `.woodpecker.yml` at repo root | Or `.woodpecker/*.yml` | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Step Docker image | `image:` in step | Required for every step | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Shell commands | `commands:` in step | Sequential execution | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Woodpecker plugins | `image:` with plugin Docker image | Configured via `settings:` | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Push trigger | Default or `when: event: push` | Fires on every push | [05-codeberg-ci.md](05-codeberg-ci.md) |
| PR trigger | `when: event: pull_request` | PR opened or updated | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Tag trigger | `when: event: tag` | On tag push | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Cron trigger | `when: event: cron` | Scheduled runs | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Repository secrets | ci.codeberg.org > repo > Settings > Secrets | Encrypted, masked in logs | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Organisation secrets | ci.codeberg.org > org > Settings | Available to all org repos | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Matrix builds | `matrix:` in pipeline | Multiple param combinations | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Service containers | `services:` in pipeline | Database sidecars for tests | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Dependency caching | Named volumes in steps | Persists on same runner host | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Self-hosted agents | Download woodpecker-agent | Avoid shared resource limits | [05-codeberg-ci.md](05-codeberg-ci.md) |

---

## Codeberg Pages

| Feature | Where to find it | Notes | Course file |
|---|---|---|---|
| Enable Pages | Push to `pages` branch | Branch must be named exactly `pages` | [06-pages.md](06-pages.md) |
| Pages URL (user) | `username.codeberg.page` | Repository named `pages` | [06-pages.md](06-pages.md) |
| Pages URL (project) | `username.codeberg.page/repo-name` | Any other repository | [06-pages.md](06-pages.md) |
| Custom domain | `.domains` file in `pages` branch root | One domain per line | [06-pages.md](06-pages.md) |
| HTTPS | Automatic via Let's Encrypt | Provisioned after DNS is configured | [06-pages.md](06-pages.md) |
| Deploy from CI | Push to `pages` branch from Woodpecker | See pipeline examples | [06-pages.md](06-pages.md) |
| Hugo sites | Build in CI, push `public/` to `pages` | Set correct `baseURL` | [06-pages.md](06-pages.md) |
| MkDocs sites | Build `site/`, push to `pages` | Set `site_url` in mkdocs.yml | [06-pages.md](06-pages.md) |
| SPA support | Copy `index.html` to `404.html` | Client-side routing workaround | [06-pages.md](06-pages.md) |

---

## Package registry and releases

| Feature | Registry URL | Notes | Course file |
|---|---|---|---|
| Container/Docker | `codeberg.org/{owner}/{repo}` | Login with access token | [07-packages-and-releases.md](07-packages-and-releases.md) |
| npm | `/api/packages/{owner}/npm/` | Scope-based configuration | [07-packages-and-releases.md](07-packages-and-releases.md) |
| PyPI | `/api/packages/{owner}/pypi/` | Publish with twine | [07-packages-and-releases.md](07-packages-and-releases.md) |
| NuGet (.NET) | `/api/packages/{owner}/nuget/` | Standard nuget.config | [07-packages-and-releases.md](07-packages-and-releases.md) |
| Maven (Java) | `/api/packages/{owner}/maven/` | Standard pom.xml settings | [07-packages-and-releases.md](07-packages-and-releases.md) |
| Cargo (Rust) | `/api/packages/{owner}/cargo/` | .cargo/config.toml | [07-packages-and-releases.md](07-packages-and-releases.md) |
| Generic packages | `/api/packages/{owner}/generic/` | Any versioned files | [07-packages-and-releases.md](07-packages-and-releases.md) |
| Helm charts | `/api/packages/{owner}/helm/` | OCI or native Helm | [07-packages-and-releases.md](07-packages-and-releases.md) |
| Debian packages | `/api/packages/{owner}/debian/` | APT repository | [07-packages-and-releases.md](07-packages-and-releases.md) |
| 14+ more formats | `/api/packages/{owner}/{type}/` | See Forgejo docs for full list | [07-packages-and-releases.md](07-packages-and-releases.md) |
| Releases | Repository > Releases | Versioned snapshots with assets | [07-packages-and-releases.md](07-packages-and-releases.md) |
| Release assets | Release form > Attachments | Binaries, installers, checksums | [07-packages-and-releases.md](07-packages-and-releases.md) |
| Automated releases | Woodpecker `plugin-gitea-release` | Triggered on tag push | [07-packages-and-releases.md](07-packages-and-releases.md) |

---

## Organisations and teams

| Feature | Where to find it | Notes | Course file |
|---|---|---|---|
| Create organisation | + > New Organisation | Permanent URL slug | [08-organisations-and-teams.md](08-organisations-and-teams.md) |
| Organisation visibility | Settings > General | Public or Private | [08-organisations-and-teams.md](08-organisations-and-teams.md) |
| Organisation members | Organisation > Members | Owner or Member roles | [08-organisations-and-teams.md](08-organisations-and-teams.md) |
| Invite member | Organisation > Members > Invite | Username-based invite | [08-organisations-and-teams.md](08-organisations-and-teams.md) |
| Create team | Organisation > Teams > New Team | Read / Write / Admin per repo set | [08-organisations-and-teams.md](08-organisations-and-teams.md) |
| Team permissions | Team settings | None / Read / Write / Admin | [08-organisations-and-teams.md](08-organisations-and-teams.md) |
| Organisation repositories | + > New Repo > Owner = org | All team access rules apply | [08-organisations-and-teams.md](08-organisations-and-teams.md) |
| Shared labels | Organisation > Issues > Labels | Available in all org repos | [08-organisations-and-teams.md](08-organisations-and-teams.md) |
| Organisation webhooks | Organisation Settings > Webhooks | Fires for all org repo events | [08-organisations-and-teams.md](08-organisations-and-teams.md) |
| Organisation packages | `/api/packages/org-name/...` | Shared across all org repos | [08-organisations-and-teams.md](08-organisations-and-teams.md) |
| Private org repos | Requires Codeberg financial support | Not available on free tier | [08-organisations-and-teams.md](08-organisations-and-teams.md) |
| Bot/service accounts | Separate Codeberg account | Recommended for CI tokens | [08-organisations-and-teams.md](08-organisations-and-teams.md) |

---

## Authentication and security

| Feature | Where to find it | Notes | Course file |
|---|---|---|---|
| Username + password | codeberg.org login | Standard local auth | [02-creating-an-account.md](02-creating-an-account.md) |
| Two-factor authentication (TOTP) | Settings > Security | Use Aegis, Authy or similar | [02-creating-an-account.md](02-creating-an-account.md) |
| Recovery codes | 2FA setup screen | Save offline, one-time use | [02-creating-an-account.md](02-creating-an-account.md) |
| SSH keys | Settings > SSH / GPG Keys | ed25519 preferred | [02-creating-an-account.md](02-creating-an-account.md) |
| GPG commit signing | Settings > SSH / GPG Keys | Sign commits cryptographically | [02-creating-an-account.md](02-creating-an-account.md) |
| Access tokens | Settings > Applications | Scoped; treat as passwords | [02-creating-an-account.md](02-creating-an-account.md) |
| OAuth2 sign-in | Not available at codeberg.org | Cannot sign in via GitHub OAuth | [02-creating-an-account.md](02-creating-an-account.md) |
| SAML SSO | Not available on codeberg.org hosted service | Self-hosted Forgejo only | [../forgejo/07-administration.md](../forgejo/07-administration.md) |

---

## Federation (ActivityPub)

| Feature | Status | Notes | Course file |
|---|---|---|---|
| Federated user identity | Active | `@username@codeberg.org` | [../forgejo/06-issues-packages-and-federation.md](../forgejo/06-issues-packages-and-federation.md) |
| WebFinger discovery | Active | Find users from Mastodon etc. | [../forgejo/08-api-and-integrations.md](../forgejo/08-api-and-integrations.md) |
| Follow repository across instances | Active | Receive cross-instance notifications | [../forgejo/06-issues-packages-and-federation.md](../forgejo/06-issues-packages-and-federation.md) |
| Cross-instance forking | In development | Not yet production-ready | [../forgejo/06-issues-packages-and-federation.md](../forgejo/06-issues-packages-and-federation.md) |
| Cross-instance PR submission | In development | Future capability | [../forgejo/06-issues-packages-and-federation.md](../forgejo/06-issues-packages-and-federation.md) |

---

## API and integrations

| Feature | URL / Location | Notes | Course file |
|---|---|---|---|
| REST API | `https://codeberg.org/api/v1/` | Forgejo-compatible | [../forgejo/08-api-and-integrations.md](../forgejo/08-api-and-integrations.md) |
| Swagger documentation | `https://codeberg.org/api/swagger` | Interactive explorer | [../forgejo/08-api-and-integrations.md](../forgejo/08-api-and-integrations.md) |
| Webhooks | Repository Settings > Webhooks | Push, PR, issue, release events | [../forgejo/08-api-and-integrations.md](../forgejo/08-api-and-integrations.md) |
| Organisation webhooks | Organisation Settings > Webhooks | Fires for all org repos | [08-organisations-and-teams.md](08-organisations-and-teams.md) |
| Woodpecker CI integration | ci.codeberg.org | OAuth2 with Codeberg | [05-codeberg-ci.md](05-codeberg-ci.md) |
| Renovate bot | External (platform: gitea) | Works with Codeberg | [../forgejo/08-api-and-integrations.md](../forgejo/08-api-and-integrations.md) |
| ArgoCD | External Git source | Use HTTPS or SSH URL | [../forgejo/08-api-and-integrations.md](../forgejo/08-api-and-integrations.md) |

---

## Codeberg vs GitHub vs GitLab.com quick comparison

| Feature | Codeberg | GitHub | GitLab.com |
|---|---|---|---|
| Operator | Non-profit (Germany) | Microsoft (US) | GitLab Inc. (US) |
| Free private repos | Personal: yes. Org: requires donation | Yes | Yes |
| CI/CD | Woodpecker CI (external) | GitHub Actions (built-in) | GitLab CI (built-in) |
| Static hosting | Codeberg Pages (`pages` branch) | GitHub Pages | GitLab Pages |
| AI features | None | Copilot (paid), PR summaries | Duo (paid) |
| Package registry | 23+ formats | 6 formats | 22+ formats |
| Mobile app | None | iOS and Android | iOS and Android |
| Data jurisdiction | EU (Germany) | US | US |
| Tracking / advertising | None | GitHub telemetry | GitLab telemetry |
| Funding | Donations | Microsoft revenue | Enterprise revenue |
| SAML SSO | Self-hosted Forgejo only | Enterprise plans | Premium/Ultimate |
| Custom domains (Pages) | Yes (`.domains` file) | Yes | Yes |
| Issue tracker | Full (Forgejo) | Full | Full |
| Forks and PRs | Full | Full | Full (MRs) |
| ActivityPub federation | Yes (Forgejo) | No | No |
| Open source platform | Yes (Forgejo, GPLv3+) | No | Partially (CE) |
| Community size | Small but growing | Enormous | Large |

---

## Quick navigation

**I want to...**

- **Understand what Codeberg is** → [01-what-is-codeberg.md](01-what-is-codeberg.md)
- **Create my account** → [02-creating-an-account.md](02-creating-an-account.md)
- **Create repositories and review code** → [03-repositories-and-code-review.md](03-repositories-and-code-review.md)
- **Manage issues and project boards** → [04-issues-and-projects.md](04-issues-and-projects.md)
- **Set up CI/CD** → [05-codeberg-ci.md](05-codeberg-ci.md)
- **Host a static site** → [06-pages.md](06-pages.md)
- **Publish packages or releases** → [07-packages-and-releases.md](07-packages-and-releases.md)
- **Set up an organisation** → [08-organisations-and-teams.md](08-organisations-and-teams.md)
- **Use the API or webhooks** → [../forgejo/08-api-and-integrations.md](../forgejo/08-api-and-integrations.md)
- **Learn about federation** → [../forgejo/06-issues-packages-and-federation.md](../forgejo/06-issues-packages-and-federation.md)
- **Migrate from GitHub** → [03-repositories-and-code-review.md](03-repositories-and-code-review.md)
- **Compare Codeberg with GitHub** → this file (quick comparison table above)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
