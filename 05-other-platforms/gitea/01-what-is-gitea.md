# What is Gitea?

**Difficulty:** 🟢 Beginner | **Time:** 25 minutes

Gitea is a self-hosted Git platform written in Go. It provides a web interface for Git repository hosting, pull requests, issues, project boards, CI/CD and a package registry - all the features of a modern Git forge - in a single binary that runs on minimal hardware. A Raspberry Pi with 512 MB of RAM can run a fully functional Gitea instance for a small team.

Understanding Gitea in 2026 requires understanding its history: a fork of an older project, a period of community-driven growth, a controversial shift to commercial ownership and the split that created Forgejo. That history shapes everything from the licence to the governance to the feature roadmap.

---

## Table of Contents

1. [Origins: Gogs and the first fork](#origins-gogs-and-the-first-fork)
2. [Gitea's growth years (2016-2022)](#giteas-growth-years-2016-2022)
3. [The 2022 governance controversy](#the-2022-governance-controversy)
4. [CommitGo and the open-core model](#commitgo-and-the-open-core-model)
5. [Gitea in 2026: the current state](#gitea-in-2026-the-current-state)
6. [What Gitea provides](#what-gitea-provides)
7. [Who uses Gitea and why](#who-uses-gitea-and-why)
8. [Gitea vs the alternatives](#gitea-vs-the-alternatives)
9. [Key concepts before you start](#key-concepts-before-you-start)
10. [Try It Yourself](#try-it-yourself)
11. [Common Mistakes](#common-mistakes)
12. [Summary](#summary)
13. [Sources](#sources)

---

## Origins: Gogs and the first fork

Gitea's story begins with **Gogs**, a project started by Jiahua Chen (unknwon) in 2014. Gogs stood for "Go Git Service" and had a simple goal: a lightweight, self-hosted GitHub alternative written in Go. At the time, the only serious self-hosted Git option was GitLab, which required significant resources and a complex Ruby on Rails stack. Gogs filled a gap for developers and small teams who wanted their own Git hosting without the overhead.

Gogs succeeded quickly. Its combination of low resource requirements, simple installation (a single binary with SQLite support) and a clean GitHub-like interface made it popular. The project accumulated thousands of stars on GitHub and tens of thousands of installations worldwide.

However, Gogs had a structural problem: it was a one-person project. Jiahua Chen maintained it alone and was selective about accepting contributions. Features could take months to merge, pull requests languished and the maintainer's availability was unpredictable. The project's governance - or lack thereof - frustrated contributors who wanted to move faster.

In **November 2016**, a group of contributors forked Gogs to create **Gitea**. The reasons: they wanted a more open, community-driven governance model where contributions were accepted more readily and decisions were made collectively. The initial Gitea commit was a fork of Gogs 0.9.97.

The fork was not hostile. Jiahua Chen was notified and both projects continued in parallel. Gogs continues to exist today as a simpler, more conservative project under Jiahua Chen's stewardship. Gitea diverged significantly over the following years, adding features, refactoring the codebase and adopting more modern tooling.

---

## Gitea's growth years (2016-2022)

From 2016 to 2022, Gitea operated as a genuine community open source project under the MIT licence. Development was fast. The project introduced:

- A significantly refactored codebase compared to Gogs
- Gitea Actions (GitHub Actions-compatible CI/CD)
- A built-in package registry supporting 20+ package types
- Improved migration tooling to import from GitHub, GitLab and Bitbucket
- LDAP and OAuth2 integration
- Repository mirroring
- Better API coverage
- Organisation-level management
- A modern, actively maintained frontend

The community grew. Multiple core contributors emerged from different countries and backgrounds. The project moved from GitHub to its own Gitea instance at `gitea.com`, then later to `about.gitea.com`. The governance model was informal but functional: a small team of maintainers reviewed and merged contributions.

By 2022, Gitea had become the most popular self-hosted Git platform after GitLab, with hundreds of thousands of active installations and millions of monthly visits to its public instance.

---

## The 2022 governance controversy

In **October 2022**, two of Gitea's lead maintainers - **Lunny Xiao** and **Matti Ranta** - incorporated a company called **Gitea Limited** in the United Kingdom without informing the broader contributor community. Simultaneously, the domain names `gitea.com` and `gitea.io` and the project's trademarks were transferred to this new company.

The community discovered this when they noticed ownership changes on the domains. An **open letter signed by 48 contributors** was published, demanding that:

1. The assets (domain names, trademarks) be transferred to a non-profit foundation
2. The governance model be made transparent
3. Conflicts of interest be disclosed

Gitea Limited declined to transfer the assets to community control.

The controversy revealed a fundamental tension in open source projects: code is MIT-licensed (anyone can fork it), but infrastructure like domain names and trademarks are controlled by whoever holds them. The contributors could fork the code freely but could not reclaim the brand.

The community response was swift. Within weeks, a new project called **Forgejo** was announced in **December 2022**, hosted under **Codeberg e.V.** (a German non-profit). Codeberg's own platform switched from running Gitea to running the Forgejo fork. The Forgejo story is told in the `forgejo/` folder.

---

## CommitGo and the open-core model

The Gitea Ltd story did not end with the Forgejo fork. In late 2023, a US subsidiary called **CommitGo, Inc.** (incorporated in Delaware) was formed. In March 2024, CommitGo launched **Gitea Enterprise** - a proprietary tier built on top of the open source Gitea codebase.

Gitea Enterprise adds features not available in the community edition:

- **Branch Protection Inheritance**: inherit branch protection rules from parent organisations
- **Dependency Scanning**: automated vulnerability detection in dependencies
- **IP Allowlist**: restrict access by IP address
- **SAML Authentication**: enterprise SSO via SAML 2.0
- **Audit Logs**: comprehensive security audit trail

These features are exclusively available to Gitea Enterprise customers - they are not contributed back to the open source project.

The governance now operates through a **Technical Oversight Committee (TOC)** with six members: three appointed by CommitGo and three elected by the community. TOC members receive $500/month from OpenCollective. The `about.gitea.com` website represents the CommitGo commercial identity.

This open-core model - free community edition plus paid proprietary enterprise tier - is similar to GitLab's (though GitLab's split is much more transparent and the free tier more capable). Whether you view it as legitimate monetisation or as an exploitation of community contributions depends on your perspective.

The key practical fact: the community **Gitea** (v1.25.x) is free, open source and MIT-licensed. The proprietary features in **Gitea Enterprise** are not available without a commercial licence. The open source project continues to receive active development.

---

## Gitea in 2026: the current state

### Version and releases

Gitea's current stable version is **v1.25.5** (March 2026). The 1.25 major release shipped in October 2025. Version 1.26 is in development.

The `1.x` versioning scheme is deliberate: Gitea has not bumped to `2.0` despite significant changes, partly to avoid the version-number gap with Forgejo (which jumped to `7.0` then `14.0`). The two projects' version numbers are no longer meaningful to compare.

### Gitea Enterprise

CommitGo also releases **Gitea Enterprise** on a separate version scheme (`25.4.x` in 2025-2026, using year-based versioning). Enterprise is a commercial product layered on top of the open source community edition.

### The frontend rewrite

Starting in Gitea v1.21 (late 2023), the frontend was rewritten from jQuery/Fomantic UI to **HTMX + Tailwind CSS**. This was a significant undertaking that improved performance and maintainability but introduced some regressions during the transition. By v1.24-v1.25, the new frontend is stable and noticeably faster.

### Recent features

Notable additions since v1.22:

**v1.25 (October 2025):**

- 3D/CAD file preview (STL, OBJ format rendering in the browser)
- Streamed repository archives (faster downloads of large repos)
- Fork-and-edit workflow from the web UI
- Email notifications for Gitea Actions results
- Enhanced SSH commit signing support

**v1.24 (June 2025):**

- Global 2FA enforcement (admins can require all users to have 2FA)
- File tree sidebar in repository view
- Material design file icons
- Anonymous access to private repos (for specific use cases)
- Choose which email to use when committing via the web UI

**v1.22 (mid-2024):**

- Dropped MySQL 5.7 and PostgreSQL 10/11 support (minimum PostgreSQL 12, MySQL 8.0)

---

## What Gitea provides

### Core Git hosting

- Unlimited repositories (public, private, internal)
- SSH and HTTPS clone
- Web-based code browsing with syntax highlighting
- File editing via the web
- Commit history, blame view and diff viewer
- Repository topics and descriptions
- Star and watch functionality
- Repository forks with fork relationship tracking

### Code review: pull requests

- Pull requests with diff view
- Inline comments on specific lines
- Review approval system
- Draft pull requests
- Protected branches
- Required reviewers
- Auto-merge
- Rebase, squash and merge commit strategies

### Issues and project management

- Issues with labels, milestones and assignees
- Issue templates
- Issue dependencies (blocks/is blocked by)
- Kanban-style project boards
- Reactions on issues and comments
- Cross-repository references

### CI/CD: Gitea Actions

- GitHub Actions-compatible workflow syntax
- Self-hosted runner (act_runner)
- Workflow files in `.gitea/workflows/` or `.github/workflows/`
- Actions marketplace compatibility (use actions from GitHub's marketplace)
- Matrix builds
- Artifact storage

### Package registry

Gitea includes one of the most complete self-hosted package registries available:

- Alpine, Arch, Cargo, Chef, Composer, Conan, Conda, Container/OCI, CRAN, Debian, Generic, Go, Helm, Maven, npm, NuGet, Pub, PyPI, RPM, RubyGems, Swift, Vagrant and more

### Organisation management

- Organisations with teams and permissions
- Team-level repository access control
- Organisation-level Gitea Actions variables and secrets
- Fork policy per organisation

### User management (admin)

- Local accounts
- LDAP and Active Directory integration
- SAML (Gitea Enterprise only)
- OAuth2 providers (GitHub, GitLab, Google, custom)
- Registration restrictions (require email domain, admin approval)

---

## Who uses Gitea and why

### Small teams and individuals who want self-hosted Git

Gitea's primary audience is developers and small teams who:

- Want control over their code (not on GitHub, GitLab.com or Bitbucket)
- Need a Git platform that runs on modest hardware
- Run home labs, developer environments or air-gapped networks
- Want a simple, fast, low-maintenance installation

### Organisations with compliance or sovereignty requirements

Teams that cannot use cloud services - due to data residency requirements, air-gapped environments or internal policy - find Gitea's minimal footprint attractive compared to running GitLab Community Edition on the same infrastructure.

### Raspberry Pi and edge deployments

Gitea is probably the only production-quality Git platform that comfortably runs on a Raspberry Pi 4. 512 MB RAM is sufficient for a small team. This opens use cases that are impossible with other platforms: embedded systems teams, offline workshop environments, rural or bandwidth-constrained deployments.

### Home lab and learning

Many developers run Gitea at home purely for learning and private personal projects. The quick Docker setup makes it accessible to anyone who wants to understand self-hosted Git hosting without significant commitment.

---

## Gitea vs the alternatives

### Gitea vs Forgejo

The most commonly asked comparison. They share the same codebase origin (Forgejo forked from Gitea in December 2022 and became a hard fork in February 2024). They are no longer drop-in replacements for each other.

Key differences in 2026:

- **Licence**: Gitea is MIT. Forgejo switched to GPLv3+ in v9.0.
- **Governance**: Gitea is controlled by CommitGo (for-profit). Forgejo operates under Codeberg e.V. (non-profit).
- **Federation**: Forgejo actively develops ActivityPub federation. Gitea has stalled on federation.
- **Windows support**: Gitea supports Windows. Forgejo dropped Windows support in 2024.
- **Enterprise tier**: Gitea has a proprietary enterprise add-on. Forgejo has no paid tier.

A full comparison is in [10-gitea-vs-forgejo.md](10-gitea-vs-forgejo.md).

### Gitea vs Gogs

Gitea is a fork of Gogs with years of additional development. Gitea has far more features (Actions, package registry, OAuth2, better UI). Gogs is simpler and more conservative. For new deployments, Gitea is the better choice unless extreme simplicity is the priority.

### Gitea vs GitLab CE

GitLab Community Edition is far more feature-complete (built-in CI/CD with no separate runner daemon, security scanning, advanced merge request features, epics, roadmaps). It also requires significantly more resources (minimum 4 cores, 4 GB RAM for CE, typically 8 GB RAM recommended). Gitea is the right choice when resources are limited or simplicity is valued. GitLab CE is the right choice when features matter more than footprint.

### Gitea vs GitHub/Bitbucket/Azure DevOps

These are cloud platforms with different trade-offs from self-hosted solutions. Gitea offers self-hosting, data control and zero ongoing subscription cost. Cloud platforms offer managed infrastructure, richer ecosystems and better AI tooling. The choice depends on your operational requirements.

---

## Key concepts before you start

### Single binary

Gitea ships as a single executable binary. It embeds its web server, template engine, database migrations and all other dependencies. You do not need Apache, Nginx, Node.js or any other runtime. The binary handles everything. (You do need a database - SQLite is bundled for small deployments, PostgreSQL or MySQL for production.)

### app.ini

All Gitea configuration lives in a file called `app.ini`, typically located at `/etc/gitea/app.ini` or `$GITEA_WORK_DIR/custom/conf/app.ini`. This is a standard INI-format file. Understanding `app.ini` is essential for serious Gitea administration.

### Work directory

Gitea uses a **work directory** (default: `/var/lib/gitea` on Linux) to store:

- Repository data (Git objects)
- Attachment uploads
- LFS objects
- Log files
- Template customisations
- The SQLite database (if used)

Back up the work directory and the database to fully back up a Gitea instance.

### act_runner

Gitea Actions requires a separate component called **act_runner**. The act_runner is a standalone Go binary that registers with your Gitea instance and executes workflow jobs. It is based on a fork of nektos/act. Unlike GitHub Actions where Microsoft provides hosted runners, Gitea users must run their own act_runner instances.

---

## Try It Yourself

**Exercise 1 - Explore Gitea.com**

Visit [gitea.com](https://gitea.com) (CommitGo's hosted Gitea instance). Create a free account and explore:

- The repository creation flow
- The pull request interface
- The Actions tab on a repository
- The package registry

**Exercise 2 - Try the official demo**

Gitea maintains a demo instance at [demo.gitea.com](https://demo.gitea.com) (check the current URL as it may change). Log in with `gitea`/`gitea` (or the current demo credentials shown on the page). Explore the admin panel to see what administration looks like.

**Exercise 3 - Compare with Forgejo**

Open [codeberg.org](https://codeberg.org) (which runs Forgejo) alongside Gitea.com. Note the similarities and differences in the interface. Both platforms share the same origin codebase and much of the UI is similar, but differences are accumulating.

---

## Common Mistakes

**Confusing Gitea with Forgejo**

Both platforms are alive, actively maintained and share a similar interface. If you are reading documentation, check whether it is specifically for Gitea or Forgejo - some things differ. If you are choosing between them, read [10-gitea-vs-forgejo.md](10-gitea-vs-forgejo.md) before deciding.

**Assuming SAML is available in the community edition**

SAML authentication is a Gitea Enterprise (paid) feature. The community edition supports LDAP and OAuth2 for external authentication but not SAML. If your organisation requires SAML SSO, you need either Gitea Enterprise or Forgejo (which includes SAML in the open source edition).

**Expecting federation features like Forgejo**

Gitea began federation work and received an NLnet grant for it. However, active federation development has largely moved to Forgejo. Do not choose Gitea expecting meaningful federation capabilities - choose Forgejo for that.

**Treating Gitea and GitLab CE as equivalent alternatives**

They are both self-hosted Git platforms but at very different feature levels. GitLab CE has native CI/CD (no separate runner daemon to configure), security scanning, epics, roadmaps, advanced merge request approvals and much more. Gitea is simpler, smaller and lighter. Match the platform to your actual requirements.

---

## Summary

Gitea is a lightweight, self-hosted Git platform written in Go. It originated as a fork of Gogs in 2016, created by contributors who wanted a more open governance model. For six years it operated as a genuine community project.

In 2022, two maintainers incorporated Gitea Limited and transferred the project's domain names and trademarks to this for-profit entity without community consent. The community's response was to create Forgejo, a separate fork under Codeberg e.V.'s non-profit umbrella. CommitGo (the US successor to Gitea Limited) subsequently launched Gitea Enterprise with proprietary-only features including SAML authentication, audit logs and IP allowlisting.

In 2026, Gitea community edition is at v1.25.x, MIT-licensed and actively developed. It provides Git hosting, pull requests, issues, Gitea Actions (GitHub Actions-compatible CI/CD), a package registry supporting 20+ formats and an extensive REST API. It runs on minimal hardware including Raspberry Pi.

Key trade-offs compared to Forgejo: Gitea is MIT-licensed (Forgejo is GPLv3+), has Windows support (Forgejo dropped it), has no federation development (Forgejo's flagship differentiator) and has a commercial enterprise tier. The choice between them involves weighing licence philosophy, required features and governance preferences.

---

## Sources

- [Gitea: Official website](https://about.gitea.com)
- [Gitea: Blog - CommitGo Launches Gitea Enterprise](https://blog.gitea.com/gitea-enterprise/)
- [Gitea: Release notes v1.25.0](https://blog.gitea.com/release-of-1.25.0/)
- [Gitea: Release notes v1.24.0](https://blog.gitea.com/release-of-1.24.0/)
- [Forgejo: First monthly report (December 2022)](https://forgejo.org/2022-12-26-monthly-update/)
- [Open letter to Gitea owners](https://gitea-open-letter.coding.social/updates/)
- [Wikipedia: Forgejo](https://en.wikipedia.org/wiki/Forgejo)
- [LWN: Forgejo makes a full break from Gitea](https://lwn.net/Articles/963095/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
