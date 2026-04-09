# Forgejo - Folder Overview

**Difficulty:** 🟢 Beginner | **Time:** 5 minutes

This folder covers Forgejo - a community-driven, self-hosted Git platform that forked from Gitea in December 2022 and became a hard fork in February 2024. Forgejo is steered by Codeberg e.V., a German non-profit, under a fully free software model with no proprietary tier. Its distinguishing features are SAML authentication in the free edition, active ActivityPub federation development and GPLv3+ licensing for new code.

Forgejo and Gitea share a common origin and similar interfaces. If you are choosing between them, read file 01 for the history and context, then see the Gitea folder's [10-gitea-vs-forgejo.md](../gitea/10-gitea-vs-forgejo.md) for the full comparison.

---

## What is in this folder?

| File | Topic | Level |
|---|---|---|
| [01-what-is-forgejo.md](01-what-is-forgejo.md) | Origin, hard fork, governance, licence, Codeberg and current state | 🟢 |
| [02-installation.md](02-installation.md) | Binary, Docker and package install; configuration and act_runner setup | 🟡 |
| [03-creating-an-account.md](03-creating-an-account.md) | Registration, SSH keys, access tokens, 2FA and SAML sign-in | 🟢 |
| [04-repositories-and-code-review.md](04-repositories-and-code-review.md) | Repos, pull requests, branch protection and code review | 🟢 |
| [05-forgejo-actions.md](05-forgejo-actions.md) | GitHub Actions-compatible CI/CD, the Forgejo runner and workflow syntax | 🟡 |
| [06-issues-packages-and-federation.md](06-issues-packages-and-federation.md) | Issues, project boards, packages and ActivityPub federation | 🟡 |
| [07-administration.md](07-administration.md) | Admin panel, LDAP, SAML, OAuth2, user management and maintenance | 🔴 |
| [08-api-and-integrations.md](08-api-and-integrations.md) | REST API, webhooks and third-party integrations | 🟡 |
| [09-all-forgejo-features-reference.md](09-all-forgejo-features-reference.md) | Complete feature reference with navigation paths | 🟢 |

---

## Forgejo terminology: if you are coming from GitHub or GitLab

Forgejo uses GitHub-style naming for most things, identical to Gitea:

| GitHub / GitLab | Forgejo |
|---|---|
| Repository | Repository |
| Pull request / Merge request | Pull request |
| GitHub Actions | Forgejo Actions (compatible syntax) |
| Actions runner | Forgejo runner |
| Branch protection rules | Protected branches |
| GitHub Packages | Forgejo Packages |
| Personal access token | Access token |
| GitHub Pages | No equivalent |
| GitHub Copilot | No AI features |
| Organisation | Organisation |

---

## Where to start

### Never used Forgejo before

1. [01 - What is Forgejo](01-what-is-forgejo.md)
2. [02 - Installation](02-installation.md)
3. [03 - Creating an account](03-creating-an-account.md)
4. [04 - Repositories and code review](04-repositories-and-code-review.md)

### Coming from Gitea

- [01 - What is Forgejo](01-what-is-forgejo.md) - what changed and why
- [03 - Creating an account](03-creating-an-account.md) - SAML is now free
- [06 - Issues, packages and federation](06-issues-packages-and-federation.md) - federation features

### Evaluating Forgejo for an enterprise deployment

- [01 - What is Forgejo](01-what-is-forgejo.md) - governance and licence
- [07 - Administration](07-administration.md) - SAML, LDAP, user management
- [../gitea/10-gitea-vs-forgejo.md](../gitea/10-gitea-vs-forgejo.md) - full comparison

---

## Prerequisites

Comfortable with Git basics. A Linux or Mac machine to install Forgejo on (Forgejo dropped Windows support in 2024).

- [git/02-installing-git.md](../../git/02-installing-git.md)
- [git/05-basic-commands.md](../../git/05-basic-commands.md)
- [git/09-remote-repos.md](../../git/09-remote-repos.md)

---

## Note on Forgejo versions

Forgejo jumped its version numbering when it became a hard fork. The current stable version is **v9.0.x** (April 2026 - check [codeberg.org/forgejo/forgejo/releases](https://codeberg.org/forgejo/forgejo/releases) for the latest). Forgejo's version numbers are not comparable to Gitea's v1.25.x - they use different schemes entirely.

---

## Note on operating system coverage

Forgejo runs on 🐧 Linux (x86_64, ARM, ARM64) and 🍎 macOS. Windows is **not supported** for production deployments. All command examples are Linux/Mac. OS icons: 🍎 Mac, 🐧 Linux.

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
