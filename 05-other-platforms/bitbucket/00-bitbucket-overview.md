# Bitbucket - Folder Overview

**Difficulty:** 🟢 Beginner | **Time:** 5 minutes

This folder covers Bitbucket - Atlassian's Git hosting platform built for teams that live inside the Jira, Confluence and Trello ecosystem. Bitbucket is the only major Git platform where the Jira integration is not an add-on but a core architectural feature. It is used by hundreds of thousands of teams worldwide, from solo developers on the free plan to enterprises running Bitbucket Data Center on their own infrastructure.

If you are brand new to Bitbucket, start at file 01 and work forward. If you already use GitHub or GitLab, file 03 maps everything you know across to Bitbucket's terminology and features. If you are looking for a specific feature, use file 13 - the complete reference - or the table below to jump straight to the right file.

---

## What is in this folder?

| File | Topic | Level |
|---|---|---|
| [01-what-is-bitbucket.md](01-what-is-bitbucket.md) | Bitbucket's founding story, Atlassian acquisition, history and 2026 state | 🟢 |
| [02-creating-an-account.md](02-creating-an-account.md) | Atlassian account setup, 2FA, SSH keys, API tokens and free tier limits | 🟢 |
| [03-bitbucket-vs-github-vs-gitlab.md](03-bitbucket-vs-github-vs-gitlab.md) | Three-way comparison: features, pricing, CI/CD, self-hosting and ecosystem | 🟡 |
| [04-repositories.md](04-repositories.md) | Creating repos, Projects, visibility, forking, LFS and repository settings | 🟢 |
| [05-pull-requests.md](05-pull-requests.md) | Creating PRs, reviewers, inline comments, merge strategies and PR templates | 🟡 |
| [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) | Complete `bitbucket-pipelines.yml` guide: steps, stages, caches, services and runners | 🔴 |
| [07-branch-permissions.md](07-branch-permissions.md) | Branch restrictions, merge checks, required builds and default reviewers | 🟡 |
| [08-jira-integration.md](08-jira-integration.md) | The deepest Jira integration available: smart commits, development panel and automation | 🟡 |
| [09-security-features.md](09-security-features.md) | IP allowlisting, 2FA enforcement, secret scanning, merge checks and Atlassian Guard | 🟡 |
| [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md) | Cloud vs Data Center: licensing, installation, migration and when to self-host | 🔴 |
| [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) | Workspaces, projects within workspaces, user roles and repository permissions | 🟡 |
| [12-api-and-integrations.md](12-api-and-integrations.md) | Bitbucket REST API v2, webhooks, API tokens and the Atlassian Marketplace | 🔴 |
| [13-all-bitbucket-features-reference.md](13-all-bitbucket-features-reference.md) | Complete reference for every Bitbucket feature with plan requirements and links | 🟢 |

---

## Bitbucket terminology: if you are coming from GitHub or GitLab

Bitbucket shares most Git concepts with GitHub and GitLab but organises things differently at the account and team level. This table maps the key terms.

| GitHub / GitLab | Bitbucket |
|---|---|
| Organisation / Group | Workspace |
| Team / Subgroup | Group (inside a Workspace) |
| Project (GitLab only) | Project (Bitbucket's grouping layer above repos) |
| Repository | Repository |
| Pull request / Merge request | Pull request |
| GitHub Actions / GitLab CI/CD | Bitbucket Pipelines |
| Actions runner / GitLab Runner | Bitbucket Runner |
| Personal access token (PAT) | API token (formerly app password - see note) |
| Branch protection rule | Branch restriction / branch permission |
| Required reviewers | Default reviewers |
| CODEOWNERS | Default reviewers (similar concept) |
| GitHub Pages / GitLab Pages | No equivalent (use Netlify, Cloudflare Pages, or similar) |
| GitHub Copilot / GitLab Duo | Rovo Dev (Atlassian Intelligence) |

> [!NOTE]
> Bitbucket is replacing app passwords with API tokens. New app password creation was blocked in September 2025 and existing app passwords stop working on 9 June 2026. Use API tokens for everything going forward.

---

## Where to start by level

### Never used Bitbucket before

Work through these in order:

1. [01 - What is Bitbucket](01-what-is-bitbucket.md)
2. [02 - Creating an account](02-creating-an-account.md)
3. [04 - Repositories](04-repositories.md)
4. [05 - Pull requests](05-pull-requests.md)

Then move on to Pipelines once you are comfortable with the basics.

### Coming from GitHub or GitLab

The core Git concepts are identical. The main differences are in organisation structure and CI/CD.

- [03 - Bitbucket vs GitHub vs GitLab](03-bitbucket-vs-github-vs-gitlab.md) - read this first
- [05 - Pull requests](05-pull-requests.md) - mostly the same, with a few differences
- [06 - Bitbucket Pipelines](06-bitbucket-pipelines.md) - Bitbucket's CI/CD system
- [11 - Workspaces and permissions](11-workspaces-and-permissions.md) - equivalent to orgs and teams

### Working in a Jira team

If your team uses Jira, Bitbucket's integration is one of its strongest selling points.

- [08 - Jira integration](08-jira-integration.md) - smart commits, development panel and automation
- [05 - Pull requests](05-pull-requests.md) - linking PRs to Jira issues
- [07 - Branch permissions](07-branch-permissions.md) - enforcing quality gates before merge

### Evaluating Bitbucket for your organisation

- [03 - Bitbucket vs GitHub vs GitLab](03-bitbucket-vs-github-vs-gitlab.md) - the full comparison
- [10 - Cloud vs Data Center](10-bitbucket-cloud-vs-data-center.md) - choose the right deployment
- [09 - Security features](09-security-features.md) - enterprise security controls
- [11 - Workspaces and permissions](11-workspaces-and-permissions.md) - access control model

### Setting up CI/CD

- [06 - Bitbucket Pipelines](06-bitbucket-pipelines.md) - the complete pipeline guide
- [07 - Branch permissions](07-branch-permissions.md) - require builds to pass before merge
- [09 - Security features](09-security-features.md) - add security scanning to pipelines

### Not sure where a feature is?

Go straight to [13 - All Bitbucket features reference](13-all-bitbucket-features-reference.md). Every feature is listed with its plan requirement, navigation path and a link to the relevant course file.

---

## Prerequisites

Before starting this folder you should be comfortable with Git basics. If you have not already worked through the `git/` folder, start with at minimum:

- [git/02-installing-git.md](../../02-git/02-installing-git.md)
- [git/04-core-concepts.md](../../02-git/04-core-concepts.md)
- [git/05-basic-commands.md](../../02-git/05-basic-commands.md)
- [git/09-remote-repos.md](../../02-git/09-remote-repos.md)

Familiarity with the `github/` folder is helpful but not required. Where Bitbucket overlaps with GitHub, this folder notes the similarities and differences directly.

---

## Note on Bitbucket plans

Bitbucket Cloud has three pricing tiers as of 2026. Throughout this folder, features are marked with the plan that requires them.

| Plan | Price | Users | Pipeline minutes |
|---|---|---|---|
| **Free** | $0 | Up to 5 | 50/month |
| **Standard** | $3.65/user/month | Unlimited | 2,500/month |
| **Premium** | $7.25/user/month | Unlimited | 3,500/month |

Most features in this folder are available on the Free or Standard plan. Premium features (IP allowlisting, enforced merge checks, 99.9% SLA, advanced security) are clearly marked. Bitbucket Data Center is a separate self-hosted product covered in [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md).

> [!IMPORTANT]
> Atlassian Guard (formerly Atlassian Access) is required for SSO and advanced identity management. Guard Standard costs $4.20/user/month on top of Bitbucket pricing and is separate from any Bitbucket plan.

---

## Note on Bitbucket Cloud vs Data Center

Bitbucket comes in two forms. **Bitbucket Cloud** is the hosted service at bitbucket.org. **Bitbucket Data Center** is the self-hosted version for organisations that need to run their own infrastructure. Bitbucket Server reached end of life on 15 February 2024 and is no longer supported.

Most of this folder covers Bitbucket Cloud. Where Data Center differs significantly, it is noted inline. The full comparison is in [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md).

---

## Note on operating system coverage

All command line examples cover Windows (PowerShell or Command Prompt), Mac (Terminal) and Linux (Bash) wherever they differ. Bitbucket's web interface is identical across all operating systems. OS icons used: 🪟 Windows, 🍎 Mac, 🐧 Linux.

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
