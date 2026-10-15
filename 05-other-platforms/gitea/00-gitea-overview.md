# Gitea - Folder Overview

**Difficulty:** 🟢 Beginner | **Time:** 5 minutes

This folder covers Gitea - a lightweight, self-hosted Git platform written in Go. Gitea is designed for teams and individuals who want to run their own Git hosting without the heavyweight infrastructure requirements of GitLab or Bitbucket Data Center. It runs on everything from a Raspberry Pi to a production Linux server, requires minimal resources and installs in minutes.

Gitea is an open source project with a complex governance history. In 2022, its parent organisation incorporated a for-profit company (Gitea Limited, later CommitGo Inc.) and adopted an open-core model. This governance dispute directly led to the creation of Forgejo (covered in the next folder). Understanding both products helps you choose between them.

If you are new to Gitea, start at file 01 and work forward. If you already run another Git platform and are evaluating Gitea, file 01 covers the history and file 10 compares Gitea and Forgejo directly.

---

## What is in this folder?

| File | Topic | Level |
|---|---|---|
| [01-what-is-gitea.md](01-what-is-gitea.md) | History, the Gitea Ltd controversy, open-core model and current state | 🟢 |
| [02-installation.md](02-installation.md) | Binary, Docker and package install on all OS, app.ini configuration and initial setup | 🟡 |
| [03-creating-an-account.md](03-creating-an-account.md) | Registration, SSH keys, access tokens, 2FA and OAuth2 providers | 🟢 |
| [04-repositories.md](04-repositories.md) | Creating repos, mirroring from GitHub/GitLab, LFS, releases and topics | 🟢 |
| [05-pull-requests-and-code-review.md](05-pull-requests-and-code-review.md) | Pull requests, reviewers, inline comments and protected branches | 🟡 |
| [06-gitea-actions.md](06-gitea-actions.md) | GitHub Actions-compatible CI/CD, act_runner setup and workflow syntax | 🟡 |
| [07-issues-and-projects.md](07-issues-and-projects.md) | Issues, labels, milestones, Kanban projects and issue templates | 🟢 |
| [08-packages.md](08-packages.md) | Built-in package registry: Docker, npm, PyPI, Maven, NuGet, Cargo and more | 🟡 |
| [09-administration.md](09-administration.md) | Admin panel, user management, instance settings, LDAP, SAML and OAuth2 | 🔴 |
| [10-gitea-vs-forgejo.md](10-gitea-vs-forgejo.md) | The 2022 governance split, what diverged, licence differences, which to choose | 🟡 |
| [11-api-and-integrations.md](11-api-and-integrations.md) | REST API with Swagger docs, webhooks and integrations | 🟡 |
| [12-all-gitea-features-reference.md](12-all-gitea-features-reference.md) | Complete feature reference with navigation paths | 🟢 |

---

## Gitea terminology: if you are coming from GitHub or GitLab

Gitea uses GitHub-style terminology for most things. Key differences:

| GitHub / GitLab | Gitea |
|---|---|
| Organisation | Organisation |
| Repository | Repository |
| Pull request / Merge request | Pull request |
| GitHub Actions | Gitea Actions (compatible syntax) |
| Actions runner | act_runner |
| Branch protection rules | Protected branches |
| GitHub Packages | Gitea Packages |
| Personal access token | Access token |
| GitHub Pages | No equivalent (use external hosting) |
| GitHub Copilot | No AI features |

---

## Where to start by level

### Never used Gitea before

1. [01 - What is Gitea](01-what-is-gitea.md) - understand what you are installing
2. [02 - Installation](02-installation.md) - get Gitea running
3. [03 - Creating an account](03-creating-an-account.md) - set up your first user
4. [04 - Repositories](04-repositories.md) - start using it

### Evaluating Gitea vs Forgejo

- [01 - What is Gitea](01-what-is-gitea.md) - the governance history
- [10 - Gitea vs Forgejo](10-gitea-vs-forgejo.md) - the direct comparison
- Then see the `forgejo/` folder

### Setting up CI/CD

- [06 - Gitea Actions](06-gitea-actions.md) - the complete CI/CD guide
- [02 - Installation](02-installation.md) - includes act_runner setup

### Running Gitea for a team

- [09 - Administration](09-administration.md) - user management, LDAP, OAuth
- [05 - Pull requests](05-pull-requests-and-code-review.md) - code review workflow

---

## Prerequisites

Before starting this folder you should be comfortable with Git basics and have a Linux or Windows machine (or Docker) to install Gitea on. Cloud VMs, Raspberry Pi and local machines all work.

- [git/02-installing-git.md](../../02-git/02-installing-git.md)
- [git/05-basic-commands.md](../../02-git/05-basic-commands.md)
- [git/09-remote-repos.md](../../02-git/09-remote-repos.md)

---

## Note on Gitea versions

At the time of writing (October 2026) Gitea is at **v28.1**. For years Gitea numbered its releases `1.MAJOR.PATCH` (1.24, 1.25, 1.26, 1.27). With the release that followed 1.27, in September 2026, Gitea dropped the leading `1.` and called it **28.0**. Major releases still arrive every few months and patch releases (28.0.1, 28.1.0) bring fixes and smaller features.

Forgejo has used its own version numbers since it became a hard fork in February 2024 and is at v16 in October 2026. The two projects' numbers are not comparable, so check which project you are reading about before relying on a version number.

---

## Note on operating system coverage

All command line examples cover 🪟 Windows, 🍎 Mac and 🐧 Linux wherever they differ. Gitea runs as a server-side application - the client side (Git commands, browser) is identical across operating systems.

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
