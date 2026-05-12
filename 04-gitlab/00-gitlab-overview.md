# GitLab - Folder Overview

**Difficulty:** 🟢 Beginner | **Time:** 5 minutes

This folder covers GitLab - a complete DevSecOps platform that bundles code hosting, CI/CD pipelines, security scanning, project management, container registries, package hosting and AI assistance into a single application. It is used by over 40 million registered users including more than 50% of the Fortune 100.

If you are brand new to GitLab, start at file 01 and work forward. If you know GitHub already, file 03 maps everything you know across to GitLab's terminology and features. If you are looking for a specific feature, use file 15 - the complete reference - or the table below to jump straight to the right file.

---

## What is in this folder?

| File | Topic | Level |
|---|---|---|
| [01-what-is-gitlab.md](01-what-is-gitlab.md) | GitLab's founding story, history, philosophy, IPO, statistics and 2026 state | 🟢 |
| [02-creating-an-account.md](02-creating-an-account.md) | Signing up, 2FA, passkeys, SSH keys, personal access tokens and free tier limits | 🟢 |
| [03-gitlab-vs-github.md](03-gitlab-vs-github.md) | Deep side-by-side comparison: features, pricing, philosophy and when to choose each | 🟡 |
| [04-projects-and-namespaces.md](04-projects-and-namespaces.md) | Projects, personal and group namespaces, subgroups, visibility levels, forking and settings | 🟢 |
| [05-merge-requests.md](05-merge-requests.md) | Creating and reviewing MRs, draft MRs, approvals, CODEOWNERS, merge methods and merge trains | 🟡 |
| [06-gitlab-cicd.md](06-gitlab-cicd.md) | Complete `.gitlab-ci.yml` guide: stages, jobs, runners, variables, artifacts, caching, DAG and more | 🔴 |
| [07-gitlab-pages.md](07-gitlab-pages.md) | Free static site hosting directly from a GitLab repository | 🟡 |
| [08-issues-and-planning.md](08-issues-and-planning.md) | Issues, labels, milestones, boards, epics, roadmaps, iterations, quick actions and Service Desk | 🟡 |
| [09-security-features.md](09-security-features.md) | SAST, DAST, secret detection, container scanning, dependency scanning, fuzz testing and vulnerability management | 🔴 |
| [10-gitlab-duo.md](10-gitlab-duo.md) | GitLab's AI suite: Code Suggestions, Duo Chat, Agent Platform, credits, IDE extensions and privacy | 🟡 |
| [11-self-hosting.md](11-self-hosting.md) | Community vs Enterprise Edition, system requirements, installation, backup, restore and upgrades | 🔴 |
| [12-gitlab-cli.md](12-gitlab-cli.md) | The `glab` CLI: installation, authentication and all major commands across Windows, Mac and Linux | 🟡 |
| [13-groups-and-permissions.md](13-groups-and-permissions.md) | Groups, subgroups, roles, access tokens, SAML SSO, SCIM, group CI/CD variables and runners | 🟡 |
| [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) | REST API, GraphQL, webhooks, Jira, Slack, container registry and package registry | 🔴 |
| [15-all-gitlab-features-reference.md](15-all-gitlab-features-reference.md) | Complete reference for every GitLab feature with tier requirements, navigation paths and links | 🟢 |

---

## GitLab terminology: if you are coming from GitHub

GitLab and GitHub do the same core job but use different names for some things. This table maps the most important ones.

| GitHub | GitLab |
|---|---|
| Repository | Project |
| Pull request | Merge request (MR) |
| Organisation | Group |
| Team | Subgroup or role |
| GitHub Actions workflow | `.gitlab-ci.yml` pipeline |
| Actions runner | GitLab Runner |
| Branch protection rules | Protected branches |
| Required reviewers (free) | Required approvals (Premium) |
| CODEOWNERS enforcement (free) | CODEOWNERS enforcement (Premium) |
| Gist | Snippet |
| GitHub Copilot | GitLab Duo |
| GitHub Pages | GitLab Pages |
| GitHub Advanced Security ($49/user) | GitLab Ultimate security scanning (included) |
| GitHub Enterprise Server ($21/user) | GitLab CE (free, open source, MIT licence) |

---

## Where to start by level

### Never used GitLab before

Work through these in order:

1. [01 - What is GitLab](01-what-is-gitlab.md)
2. [02 - Creating an account](02-creating-an-account.md)
3. [04 - Projects and namespaces](04-projects-and-namespaces.md)
4. [05 - Merge requests](05-merge-requests.md)

Then come back for CI/CD once you are comfortable with the basics.

### Coming from GitHub

The concepts are identical - the names and some features differ.

- [03 - GitLab vs GitHub](03-gitlab-vs-github.md) - read this first
- [05 - Merge requests](05-merge-requests.md) - the equivalent of pull requests
- [06 - GitLab CI/CD](06-gitlab-cicd.md) - the equivalent of GitHub Actions
- [13 - Groups and permissions](13-groups-and-permissions.md) - the equivalent of orgs and teams

### Want to set up CI/CD

- [06 - GitLab CI/CD](06-gitlab-cicd.md) - the complete pipeline guide
- [07 - GitLab Pages](07-gitlab-pages.md) - deploy a site for free
- [09 - Security features](09-security-features.md) - add security scanning to your pipelines

### Working in an organisation or company

- [13 - Groups and permissions](13-groups-and-permissions.md)
- [05 - Merge requests](05-merge-requests.md) - approvals and CODEOWNERS
- [09 - Security features](09-security-features.md)
- [11 - Self-hosting](11-self-hosting.md) - if your organisation runs its own GitLab instance

### Running your own GitLab server

- [11 - Self-hosting](11-self-hosting.md) - install and operate GitLab on your own infrastructure
- [14 - API and integrations](14-gitlab-api-and-integrations.md) - connect to external tools

### Not sure where a feature is?

Go straight to [15 - All GitLab features reference](15-all-gitlab-features-reference.md). Every feature is listed with its tier requirement, navigation path and a link to the relevant course file.

---

## Prerequisites

Before starting this folder you should be comfortable with Git basics. If you have not already worked through the `git/` folder, start with at minimum:

- [git/02-installing-git.md](../02-git/02-installing-git.md)
- [git/04-core-concepts.md](../02-git/04-core-concepts.md)
- [git/05-basic-commands.md](../02-git/05-basic-commands.md)
- [git/09-remote-repos.md](../02-git/09-remote-repos.md)

Familiarity with the `github/` folder helps but is not required. Where concepts overlap, this folder notes the similarities and differences.

---

## Note on GitLab tiers

Many GitLab features require paid plans. Throughout this folder, features are marked as:

- **Free** - available to all accounts on GitLab.com and all self-managed installations with no licence required
- **Premium** ($29/user/month on GitLab.com) - merge request approvals, epics, roadmaps, scoped labels, iterations, SAML SSO and more
- **Ultimate** (~$99/user/month) - Advanced SAST, DAST, fuzz testing, vulnerability management, security policies, compliance frameworks

Students and qualifying educational institutions can apply for GitLab Ultimate free of charge. Open source projects can also qualify. See [01-what-is-gitlab.md](01-what-is-gitlab.md) for the community programme details.

---

## Note on GitLab.com vs self-managed

GitLab can be used as a cloud service at `gitlab.com` or installed on your own servers. Most features are identical, but some differ:

- **Internal visibility**: available only on self-managed instances
- **SAML SSO**: group-level on GitLab.com, instance-level on self-managed
- **Free tier storage**: 10 GiB per project on GitLab.com, unlimited on self-managed
- **Free tier users**: 5 per private group on GitLab.com, no limit on self-managed
- **Admin area**: only exists on self-managed instances
- **LDAP authentication**: self-managed only

Where differences exist they are noted in the relevant file. Self-hosting is covered fully in [11-self-hosting.md](11-self-hosting.md).

---

## Note on operating system coverage

All command line examples cover Windows (PowerShell), Mac (Terminal) and Linux (Bash) wherever they differ. GitLab's web interface is identical across all operating systems. OS icons used: 🪟 Windows, 🍎 Mac, 🐧 Linux.

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
