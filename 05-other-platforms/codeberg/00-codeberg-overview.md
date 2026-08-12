# Codeberg - Folder Overview

**Difficulty:** 🟢 Beginner | **Time:** 5 minutes

This folder covers Codeberg - a public Git hosting platform run by Codeberg e.V., a German non-profit organisation. Codeberg is powered by Forgejo and provides free hosting for open source projects, with a strong emphasis on privacy, community governance and freedom from commercial interests. It is the largest public Forgejo instance and home to thousands of open source projects that have migrated from GitHub for philosophical or practical reasons.

Codeberg is not a self-hosted platform - it is a hosted service like GitHub. You sign up, create repositories and use it. No servers to manage, no binaries to install. The difference from GitHub is who runs it and why: a non-profit with a public interest mission rather than a corporation with a commercial one.

---

## What is in this folder?

| File | Topic | Level |
|---|---|---|
| [01-what-is-codeberg.md](01-what-is-codeberg.md) | Codeberg e.V., mission, history, how it differs from GitHub and GitLab.com | 🟢 |
| [02-creating-an-account.md](02-creating-an-account.md) | Registration, profile, SSH keys, access tokens and 2FA | 🟢 |
| [03-repositories-and-code-review.md](03-repositories-and-code-review.md) | Creating repos, pull requests, branch protection and code review | 🟢 |
| [04-issues-and-projects.md](04-issues-and-projects.md) | Issues, labels, milestones, project boards and issue templates | 🟢 |
| [05-codeberg-ci.md](05-codeberg-ci.md) | Woodpecker CI integration, workflow syntax and the Codeberg CI service | 🟡 |
| [06-pages.md](06-pages.md) | Codeberg Pages: free static site hosting from a repository | 🟡 |
| [07-packages-and-releases.md](07-packages-and-releases.md) | Package registry, releases and binary assets | 🟡 |
| [08-organisations-and-teams.md](08-organisations-and-teams.md) | Organisations, teams, permissions and managing contributors | 🟢 |
| [09-all-codeberg-features-reference.md](09-all-codeberg-features-reference.md) | Complete feature reference with navigation paths | 🟢 |

---

## Codeberg terminology: if you are coming from GitHub

Codeberg runs Forgejo, so the interface and terminology are nearly identical to GitHub with a few differences:

| GitHub | Codeberg (Forgejo) |
|---|---|
| Repository | Repository |
| Pull request | Pull request |
| GitHub Actions | Woodpecker CI (separate service) |
| GitHub Pages | Codeberg Pages |
| GitHub Packages | Forgejo Package Registry |
| Gist | Snippet |
| GitHub Sponsors | Codeberg does not have this |
| GitHub Copilot | No AI features |

---

## Where to start

### Never used Codeberg before

1. [01 - What is Codeberg](01-what-is-codeberg.md)
2. [02 - Creating an account](02-creating-an-account.md)
3. [03 - Repositories and code review](03-repositories-and-code-review.md)

### Migrating from GitHub

- [01 - What is Codeberg](01-what-is-codeberg.md) - understand the platform
- [02 - Creating an account](02-creating-an-account.md) - set up your account
- [03 - Repositories and code review](03-repositories-and-code-review.md) - import your repositories

### Setting up a static site

- [06 - Codeberg Pages](06-pages.md) - the complete guide

### Setting up CI/CD

- [05 - Codeberg CI](05-codeberg-ci.md) - Woodpecker CI integration

---

## Prerequisites

Basic Git knowledge. No server administration required - Codeberg is a hosted service.

- [git/02-installing-git.md](../../02-git/02-installing-git.md)
- [git/05-basic-commands.md](../../02-git/05-basic-commands.md)
- [git/09-remote-repos.md](../../02-git/09-remote-repos.md)

---

## Note on Codeberg and Forgejo

Codeberg runs Forgejo. Most features documented in the `forgejo/` folder apply directly to Codeberg. This folder focuses on Codeberg-specific aspects: the hosted service limits, Codeberg Pages (which works differently from self-hosted Forgejo), Woodpecker CI (the CI service Codeberg provides) and the community and governance aspects that make Codeberg distinct.

Where a feature is identical to Forgejo, this folder references the Forgejo documentation rather than repeating it.

---

## Note on operating system coverage

Codeberg is a web service. The client side (Git commands, SSH) works on 🪟 Windows, 🍎 Mac and 🐧 Linux. OS icons are used where command-line examples differ between platforms.

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
