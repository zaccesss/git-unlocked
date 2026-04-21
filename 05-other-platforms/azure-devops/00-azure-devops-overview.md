# Azure DevOps - Folder Overview

**Difficulty:** 🟢 Beginner | **Time:** 5 minutes

This folder covers Azure DevOps - Microsoft's integrated developer platform for planning, building, testing and deploying software. Azure DevOps is not a single product but a suite of five interconnected services: Boards for project management, Repos for Git hosting, Pipelines for CI/CD, Test Plans for quality assurance and Artifacts for package management. Together they cover the entire software delivery lifecycle under one roof.

Azure DevOps is heavily used in enterprise Microsoft environments - organisations running Azure cloud infrastructure, .NET stacks, Windows workloads and Microsoft identity systems. It is one of the most feature-complete DevOps platforms available and has a substantial free tier that makes it accessible for smaller teams too.

If you are brand new to Azure DevOps, start at file 01 and work forward. If you already use GitHub or GitLab, file 03 maps the terminology and core concepts across. If you are looking for a specific feature, use file 14 - the complete reference.

---

## What is in this folder?

| File | Topic | Level |
|---|---|---|
| [01-what-is-azure-devops.md](01-what-is-azure-devops.md) | History from Visual SourceSafe to Azure DevOps, the five services, market position | 🟢 |
| [02-creating-an-account.md](02-creating-an-account.md) | Microsoft/Azure account setup, organisations, projects, PATs, SSH keys, free tier | 🟢 |
| [03-azure-repos.md](03-azure-repos.md) | Git repos, branch policies, pull requests, code review and TFVC vs Git | 🟡 |
| [04-azure-boards.md](04-azure-boards.md) | Work items, Kanban boards, sprints, backlogs, queries and delivery plans | 🟡 |
| [05-azure-pipelines.md](05-azure-pipelines.md) | Complete YAML pipeline guide: triggers, stages, jobs, agents, variables and deployments | 🔴 |
| [06-azure-artifacts.md](06-azure-artifacts.md) | Package feeds, npm, NuGet, PyPI, Maven, Cargo and Universal Packages | 🟡 |
| [07-azure-test-plans.md](07-azure-test-plans.md) | Manual test plans, test suites, exploratory testing and running automated tests | 🟡 |
| [08-branch-policies.md](08-branch-policies.md) | Requiring PRs, minimum reviewers, build validation, merge strategies | 🟡 |
| [09-security-and-permissions.md](09-security-and-permissions.md) | Organisation, project, team and repo level permissions and security groups | 🟡 |
| [10-azure-devops-server.md](10-azure-devops-server.md) | On-premises installation, system requirements, licensing and upgrading from TFS | 🔴 |
| [11-extensions-and-marketplace.md](11-extensions-and-marketplace.md) | Azure DevOps Marketplace, popular extensions and building your own | 🟡 |
| [12-azure-devops-cli.md](12-azure-devops-cli.md) | The `az devops` CLI: installation, authentication and all major commands | 🟡 |
| [13-api-and-integrations.md](13-api-and-integrations.md) | REST API, service hooks, GitHub integration and service connections | 🔴 |
| [14-all-azure-devops-features-reference.md](14-all-azure-devops-features-reference.md) | Complete reference for every Azure DevOps feature with plan requirements | 🟢 |

---

## Azure DevOps terminology: if you are coming from GitHub or GitLab

| GitHub / GitLab | Azure DevOps |
|---|---|
| Organisation / Group | Organisation |
| Repository / Project | Project (contains repos + boards + pipelines) |
| Repository | Repository (inside a project) |
| Pull request / Merge request | Pull request |
| GitHub Actions / GitLab CI/CD | Azure Pipelines |
| Actions runner / GitLab Runner | Agent (Microsoft-hosted or self-hosted) |
| Branch protection rules | Branch policies |
| Issues / Issue board | Work items / Azure Boards |
| GitHub Projects | Azure Boards (Kanban + sprints) |
| Milestones | Iterations / Sprints |
| Epics | Epics (in work item hierarchy) |
| GitHub Packages / GitLab Package Registry | Azure Artifacts |
| GitHub Advanced Security | Azure DevOps has no equivalent native feature |
| Personal access token (PAT) | Personal access token (PAT) |
| GitHub Copilot | GitHub Copilot (separate - not native to ADO) |

> [!NOTE]
> In Azure DevOps, a **Project** is a higher-level container than a repository. One project can contain multiple repositories, multiple boards, multiple pipelines and its own Artifacts feed. This is different from GitHub where a repository is the primary unit of organisation.

---

## Where to start by level

### Never used Azure DevOps before

Work through these in order:

1. [01 - What is Azure DevOps](01-what-is-azure-devops.md)
2. [02 - Creating an account](02-creating-an-account.md)
3. [03 - Azure Repos](03-azure-repos.md)
4. [04 - Azure Boards](04-azure-boards.md)

Then add Pipelines once you are comfortable with the basics.

### Coming from GitHub or GitLab

The Git concepts are identical. The surrounding toolchain is structured differently.

- [03 - Azure Repos](03-azure-repos.md) - Git hosting with branch policies
- [05 - Azure Pipelines](05-azure-pipelines.md) - CI/CD, equivalent to GitHub Actions
- [04 - Azure Boards](04-azure-boards.md) - project management, equivalent to GitHub Projects
- [09 - Security and permissions](09-security-and-permissions.md) - access control model

### Setting up CI/CD

- [05 - Azure Pipelines](05-azure-pipelines.md) - the complete pipeline guide
- [08 - Branch policies](08-branch-policies.md) - require builds to pass before merge
- [06 - Azure Artifacts](06-azure-artifacts.md) - publish packages from your pipeline

### Working in an enterprise Microsoft environment

- [09 - Security and permissions](09-security-and-permissions.md) - permission model and Azure AD
- [10 - Azure DevOps Server](10-azure-devops-server.md) - on-premises deployment
- [13 - API and integrations](13-api-and-integrations.md) - connecting to other enterprise tools

### Not sure where a feature is?

Go straight to [14 - All Azure DevOps features reference](14-all-azure-devops-features-reference.md).

---

## Prerequisites

Before starting this folder you should be comfortable with Git basics. If you have not already worked through the `git/` folder, start with at minimum:

- [git/02-installing-git.md](../../02-git/02-installing-git.md)
- [git/04-core-concepts.md](../../02-git/04-core-concepts.md)
- [git/05-basic-commands.md](../../02-git/05-basic-commands.md)
- [git/09-remote-repos.md](../../02-git/09-remote-repos.md)

---

## Note on Azure DevOps plans

Azure DevOps Services (cloud) has a free tier and paid extensions. Throughout this folder, features are marked as:

- **Free** - included at no cost (5 Basic users, 1 free parallel job, Basic + Test Plans for Visual Studio subscribers)
- **Basic** - $6/user/month for additional users beyond the 5 free
- **Basic + Test Plans** - $52/user/month for manual testing features
- **Paid parallel jobs** - $40/month per additional Microsoft-hosted parallel job

Most CI/CD and code hosting features are free or included with Basic. Test Plans require the more expensive tier. Azure DevOps Server (on-premises) uses a separate licence model covered in [10-azure-devops-server.md](10-azure-devops-server.md).

---

## Note on Azure DevOps Services vs Server

Azure DevOps comes in two forms:

- **Azure DevOps Services** - the cloud-hosted service at [dev.azure.com](https://dev.azure.com). Managed by Microsoft, continuously updated, no infrastructure to maintain.
- **Azure DevOps Server** - the on-premises version, formerly called Team Foundation Server (TFS). Installed on your own infrastructure. The latest release is simply called "Azure DevOps Server" with no year designation.

Most of this folder covers Azure DevOps Services. Where Server differs significantly, it is noted inline. Full Server coverage is in [10-azure-devops-server.md](10-azure-devops-server.md).

---

## Note on operating system coverage

All command line examples cover Windows (PowerShell), Mac (Terminal) and Linux (Bash) wherever they differ. Azure DevOps's web interface is identical across all operating systems. OS icons used: 🪟 Windows, 🍎 Mac, 🐧 Linux.

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
