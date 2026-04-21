# All Azure DevOps Features Reference

**Difficulty:** 🟢 Beginner | **Time:** Reference document - use as needed

This file is the complete reference for every Azure DevOps Services feature. Use it to quickly find where a feature lives, what licence it requires and which file in this folder covers it in depth. Features are organised by service, matching the Azure DevOps navigation structure.

---

## Table of Contents

1. [Licence quick reference](#licence-quick-reference)
2. [Azure Boards](#azure-boards)
3. [Azure Repos](#azure-repos)
4. [Azure Pipelines](#azure-pipelines)
5. [Azure Artifacts](#azure-artifacts)
6. [Azure Test Plans](#azure-test-plans)
7. [Organisation and administration](#organisation-and-administration)
8. [Security and permissions](#security-and-permissions)
9. [Integrations and extensions](#integrations-and-extensions)
10. [Azure DevOps Server (on-premises)](#azure-devops-server-on-premises)
11. [Quick navigation](#quick-navigation)
12. [Feature comparison with GitHub and GitLab](#feature-comparison-with-github-and-gitlab)

---

## Licence quick reference

| Badge | Licence | Cost (2026) |
|---|---|---|
| **Free** | Free tier | $0, up to 5 Basic users |
| **Basic** | Basic access | $6/user/month beyond 5 free |
| **Test Plans** | Basic + Test Plans | $52/user/month |
| **VS** | Visual Studio subscriber | Included in VS subscription |
| **Server** | Azure DevOps Server licence | Annual licence required |

Stakeholder access is free and unlimited - stakeholders can view and update work items but cannot access Repos or run pipelines.

---

## Azure Boards

| Feature | Licence | Where to find it | Course file |
|---|---|---|---|
| Work items (create, view, update) | **Free** | Boards > Work Items | [04-azure-boards.md](04-azure-boards.md) |
| Kanban board | **Free** | Boards > Boards | [04-azure-boards.md](04-azure-boards.md) |
| Backlog | **Free** | Boards > Backlogs | [04-azure-boards.md](04-azure-boards.md) |
| Sprints / Iterations | **Free** | Boards > Sprints | [04-azure-boards.md](04-azure-boards.md) |
| Sprint capacity planning | **Free** | Boards > Sprints > Capacity | [04-azure-boards.md](04-azure-boards.md) |
| Sprint burndown chart | **Free** | Boards > Sprints > Analytics | [04-azure-boards.md](04-azure-boards.md) |
| Velocity chart | **Free** | Boards > Analytics | [04-azure-boards.md](04-azure-boards.md) |
| Work item queries (WIQL) | **Free** | Boards > Queries | [04-azure-boards.md](04-azure-boards.md) |
| Saved queries | **Free** | Boards > Queries | [04-azure-boards.md](04-azure-boards.md) |
| Dashboards and widgets | **Free** | Overview > Dashboards | [04-azure-boards.md](04-azure-boards.md) |
| Cumulative flow diagram | **Free** | Analytics widget | [04-azure-boards.md](04-azure-boards.md) |
| Delivery plans (multi-team timeline) | **Free** | Boards > Delivery Plans | [04-azure-boards.md](04-azure-boards.md) |
| Agile process template | **Free** | Project creation | [04-azure-boards.md](04-azure-boards.md) |
| Scrum process template | **Free** | Project creation | [04-azure-boards.md](04-azure-boards.md) |
| CMMI process template | **Free** | Project creation | [04-azure-boards.md](04-azure-boards.md) |
| Basic process template | **Free** | Project creation | [04-azure-boards.md](04-azure-boards.md) |
| Custom work item types | **Basic** | Organisation settings > Process | [04-azure-boards.md](04-azure-boards.md) |
| Link work items to commits/PRs | **Free** | Work item > Links tab | [04-azure-boards.md](04-azure-boards.md) |
| Work item tags | **Free** | Work item form | [04-azure-boards.md](04-azure-boards.md) |
| Area paths | **Free** | Project settings > Boards > Areas | [04-azure-boards.md](04-azure-boards.md) |
| Iteration paths | **Free** | Project settings > Boards > Iterations | [04-azure-boards.md](04-azure-boards.md) |
| Teams | **Free** | Project settings > Teams | [04-azure-boards.md](04-azure-boards.md) |
| Power BI integration | **Free** | Analytics > Power BI | [04-azure-boards.md](04-azure-boards.md) |
| GitHub work item linking (AB# syntax) | **Free** | Commit message / PR | [13-api-and-integrations.md](13-api-and-integrations.md) |

---

## Azure Repos

| Feature | Licence | Where to find it | Course file |
|---|---|---|---|
| Git repositories | **Free** | Repos > Files | [03-azure-repos.md](03-azure-repos.md) |
| Unlimited private repositories | **Free** | Repos | [03-azure-repos.md](03-azure-repos.md) |
| Clone via HTTPS | **Free** | Repos > Clone button | [03-azure-repos.md](03-azure-repos.md) |
| Clone via SSH | **Free** | Repos > Clone > SSH | [02-creating-an-account.md](02-creating-an-account.md) |
| Pull requests | **Free** | Repos > Pull requests | [03-azure-repos.md](03-azure-repos.md) |
| Inline code comments | **Free** | PR > Files tab | [03-azure-repos.md](03-azure-repos.md) |
| Suggested changes | **Free** | PR > Files tab | [03-azure-repos.md](03-azure-repos.md) |
| PR approval / vote | **Free** | PR overview | [03-azure-repos.md](03-azure-repos.md) |
| PR templates | **Free** | `.azuredevops/pull_request_template.md` | [03-azure-repos.md](03-azure-repos.md) |
| Draft pull requests | **Free** | PR creation | [03-azure-repos.md](03-azure-repos.md) |
| Work item linking in PRs | **Free** | PR > Work Items | [03-azure-repos.md](03-azure-repos.md) |
| Branch creation from work item | **Free** | Work item > Development > Create branch | [04-azure-boards.md](04-azure-boards.md) |
| Branch policies (all types) | **Free** | Project settings > Repos > Policies | [08-branch-policies.md](08-branch-policies.md) |
| Require minimum reviewers | **Free** | Branch policy | [08-branch-policies.md](08-branch-policies.md) |
| Build validation policy | **Free** | Branch policy | [08-branch-policies.md](08-branch-policies.md) |
| Comment resolution policy | **Free** | Branch policy | [08-branch-policies.md](08-branch-policies.md) |
| Linked work item policy | **Free** | Branch policy | [08-branch-policies.md](08-branch-policies.md) |
| Merge type restriction | **Free** | Branch policy | [08-branch-policies.md](08-branch-policies.md) |
| Automatically include reviewers | **Free** | Branch policy | [08-branch-policies.md](08-branch-policies.md) |
| Branch locks | **Free** | Repos > Branches > Lock | [08-branch-policies.md](08-branch-policies.md) |
| Cross-repository policies | **Free** | Project settings > Repos > Policies | [08-branch-policies.md](08-branch-policies.md) |
| Code search | **Free** (requires extension on Server) | Search bar (global) | [11-extensions-and-marketplace.md](11-extensions-and-marketplace.md) |
| Git Large File Storage (LFS) | **Free** | Configured via .gitattributes | [03-azure-repos.md](03-azure-repos.md) |
| Repository import from GitHub/GitLab | **Free** | Repos > Import a repository | [03-azure-repos.md](03-azure-repos.md) |
| TFVC (Team Foundation Version Control) | **Free** | Repos (TFVC mode) | [03-azure-repos.md](03-azure-repos.md) |
| GitHub Advanced Security (GHAS) for ADO | **Paid add-on** | Project settings > Repos > Advanced Security | [13-api-and-integrations.md](13-api-and-integrations.md) |
| Commit status API | **Free** | REST API | [13-api-and-integrations.md](13-api-and-integrations.md) |
| Multiple repos per project | **Free** | Repos > dropdown > New repository | [03-azure-repos.md](03-azure-repos.md) |

---

## Azure Pipelines

| Feature | Licence | Where to find it | Course file |
|---|---|---|---|
| YAML pipelines | **Free** | Pipelines > New pipeline | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Classic pipelines (visual editor, legacy) | **Free** | Pipelines > New pipeline > Classic | [05-azure-pipelines.md](05-azure-pipelines.md) |
| 1 free Microsoft-hosted parallel job | **Free** | Included | [05-azure-pipelines.md](05-azure-pipelines.md) |
| 1,800 free CI minutes/month | **Free** | Included for private projects | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Unlimited minutes for public projects | **Free** | Included | [05-azure-pipelines.md](05-azure-pipelines.md) |
| 1 free self-hosted parallel job | **Free** | Included | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Additional hosted jobs | $40/month each | Billing settings | [05-azure-pipelines.md](05-azure-pipelines.md) |
| ubuntu-latest agent (Ubuntu 24.04) | **Free** | `vmImage: ubuntu-latest` | [05-azure-pipelines.md](05-azure-pipelines.md) |
| windows-latest agent (Windows Server 2025) | **Free** | `vmImage: windows-latest` | [05-azure-pipelines.md](05-azure-pipelines.md) |
| macos-latest agent (macOS 15) | **Free** | `vmImage: macos-latest` | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Self-hosted agents (Windows, Mac, Linux) | **Free** | Org settings > Agent pools | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Managed DevOps Pools | **Free** (pay for VMs) | Org settings > Managed DevOps Pools | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Multi-stage pipelines | **Free** | `stages:` in YAML | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Parallel jobs within a pipeline | **Free** (uses parallel job slots) | Multiple jobs in a stage | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Pipeline templates | **Free** | `template:` in YAML | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Pipeline variables | **Free** | Pipeline settings | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Variable groups | **Free** | Pipelines > Library | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Azure Key Vault variable groups | **Free** | Pipelines > Library | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Environments | **Free** | Pipelines > Environments | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Deployment approvals | **Free** | Environments > Approvals and checks | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Deployment strategies (runOnce, rolling, canary) | **Free** | YAML `strategy:` | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Service connections | **Free** | Project settings > Service connections | [13-api-and-integrations.md](13-api-and-integrations.md) |
| Workload identity federation (OIDC) | **Free** | Service connection creation | [13-api-and-integrations.md](13-api-and-integrations.md) |
| Pipeline caching | **Free** | `Cache@2` task | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Pipeline artifacts | **Free** | `PublishPipelineArtifact@1` | [05-azure-pipelines.md](05-azure-pipelines.md) |
| GitHub repository trigger | **Free** | Pipeline source selection | [13-api-and-integrations.md](13-api-and-integrations.md) |
| Scheduled triggers | **Free** | `schedules:` in YAML | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Manual pipeline triggers | **Free** | Pipelines > Run pipeline | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Conditions | **Free** | `condition:` in YAML | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Test result publishing | **Free** | `PublishTestResults@2` task | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Code coverage publishing | **Free** | `PublishCodeCoverageResults@2` | [05-azure-pipelines.md](05-azure-pipelines.md) |
| Checks and gates (approval gates) | **Free** | Environments > Approvals and checks | [05-azure-pipelines.md](05-azure-pipelines.md) |

---

## Azure Artifacts

| Feature | Licence | Where to find it | Course file |
|---|---|---|---|
| Package feeds | **Free** | Artifacts > Create Feed | [06-azure-artifacts.md](06-azure-artifacts.md) |
| NuGet packages | **Free** | Feed > Connect to feed > NuGet | [06-azure-artifacts.md](06-azure-artifacts.md) |
| npm packages | **Free** | Feed > Connect to feed > npm | [06-azure-artifacts.md](06-azure-artifacts.md) |
| Python / pip packages | **Free** | Feed > Connect to feed > pip | [06-azure-artifacts.md](06-azure-artifacts.md) |
| Maven packages | **Free** | Feed > Connect to feed > Maven | [06-azure-artifacts.md](06-azure-artifacts.md) |
| Cargo packages (Rust, preview) | **Free** | Feed > Connect to feed > Cargo | [06-azure-artifacts.md](06-azure-artifacts.md) |
| Universal Packages | **Free** | CLI: `az artifacts universal` | [06-azure-artifacts.md](06-azure-artifacts.md) |
| Upstream sources (public registry proxying) | **Free** | Feed settings > Upstream sources | [06-azure-artifacts.md](06-azure-artifacts.md) |
| 2 GiB free storage | **Free** | Included per organisation | [06-azure-artifacts.md](06-azure-artifacts.md) |
| Feed permissions (Reader/Contributor/Owner) | **Free** | Feed settings > Permissions | [06-azure-artifacts.md](06-azure-artifacts.md) |
| Retention policies | **Free** | Feed settings > Retention | [06-azure-artifacts.md](06-azure-artifacts.md) |
| Package recycle bin | **Free** | Feed > Recycle bin | [06-azure-artifacts.md](06-azure-artifacts.md) |
| NuGet credential provider | **Free** | Download from Microsoft | [06-azure-artifacts.md](06-azure-artifacts.md) |

---

## Azure Test Plans

| Feature | Licence | Where to find it | Course file |
|---|---|---|---|
| Test plans and test suites | **Test Plans** | Test Plans > New Test Plan | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Test cases with steps | **Test Plans** | Test Plan > + New Test Case | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Shared steps | **Test Plans** | Test case > Create shared steps | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Parameterised test cases | **Test Plans** | Test case > Parameters | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Manual test execution | **Test Plans** | Test suite > Run | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Test runner (browser-based) | **Test Plans** | Test suite > Run > Run for web application | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Exploratory testing | **Test Plans** | Test and Feedback browser extension | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Associate automated tests with test cases | **Test Plans** | Test case > Associated Automation | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Run automated tests from Test Plans | **Test Plans** | Test suite > Run > Run with options | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Bug filing from test execution | **Test Plans** | Test runner > File a bug | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Test configurations (cross-browser/OS) | **Test Plans** | Test Plans > Configurations | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Test result history per test case | **Test Plans** | Test case > Test Results tab | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Progress reports | **Test Plans** | Test Plan > Charts | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Requirement traceability | **Test Plans** | Work item > Links > Tested by | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Test Plans dashboard widgets | **Test Plans** | Dashboard > Add widget | [07-azure-test-plans.md](07-azure-test-plans.md) |
| Pipeline test result publishing (automated) | **Free** | `PublishTestResults@2` task | [05-azure-pipelines.md](05-azure-pipelines.md) |

---

## Organisation and administration

| Feature | Licence | Where to find it | Course file |
|---|---|---|---|
| Create organisation | **Free** | dev.azure.com | [02-creating-an-account.md](02-creating-an-account.md) |
| Create projects | **Free** | Organisation home > New project | [02-creating-an-account.md](02-creating-an-account.md) |
| 5 free Basic users | **Free** | Included | [02-creating-an-account.md](02-creating-an-account.md) |
| Unlimited Stakeholder users | **Free** | Included | [02-creating-an-account.md](02-creating-an-account.md) |
| Personal Access Tokens | **Free** | User avatar > Personal access tokens | [02-creating-an-account.md](02-creating-an-account.md) |
| SSH keys | **Free** | User avatar > Security > SSH keys | [02-creating-an-account.md](02-creating-an-account.md) |
| Azure AD / Entra ID connection | **Free** | Org settings > Azure Active Directory | [09-security-and-permissions.md](09-security-and-permissions.md) |
| Audit log | **Free** | Org settings > Audit log | [09-security-and-permissions.md](09-security-and-permissions.md) |
| Audit log streaming (SIEM) | **Free** | Org settings > Audit log > Streams | [09-security-and-permissions.md](09-security-and-permissions.md) |
| PAT policies (max lifetime, scope restriction) | **Free** | Org settings > Policies | [09-security-and-permissions.md](09-security-and-permissions.md) |
| Region selection | **Free** | Set at org creation | [02-creating-an-account.md](02-creating-an-account.md) |
| Billing management | **Free** | Org settings > Billing | [02-creating-an-account.md](02-creating-an-account.md) |
| Extension management | **Free** (admin only) | Org settings > Extensions | [11-extensions-and-marketplace.md](11-extensions-and-marketplace.md) |

---

## Security and permissions

| Feature | Licence | Where to find it | Course file |
|---|---|---|---|
| Security groups (default) | **Free** | Project settings > Permissions | [09-security-and-permissions.md](09-security-and-permissions.md) |
| Custom security groups | **Free** | Project settings > Permissions > New group | [09-security-and-permissions.md](09-security-and-permissions.md) |
| Project-level permissions | **Free** | Project settings > Permissions | [09-security-and-permissions.md](09-security-and-permissions.md) |
| Repository-level permissions | **Free** | Project settings > Repos > Security | [09-security-and-permissions.md](09-security-and-permissions.md) |
| Pipeline resource protection | **Free** | Environments / Service connections > Security | [09-security-and-permissions.md](09-security-and-permissions.md) |
| Bypass branch policies permission | **Free** | Repo security > Bypass policies | [08-branch-policies.md](08-branch-policies.md) |
| Azure AD Conditional Access | Requires Azure AD | Azure AD admin portal | [09-security-and-permissions.md](09-security-and-permissions.md) |
| MFA enforcement | Requires Azure AD | Azure AD admin portal | [09-security-and-permissions.md](09-security-and-permissions.md) |

---

## Integrations and extensions

| Feature | Licence | Where to find it | Course file |
|---|---|---|---|
| Azure DevOps REST API | **Free** | dev.azure.com/{org}/_apis | [13-api-and-integrations.md](13-api-and-integrations.md) |
| Service hooks (webhooks) | **Free** | Project settings > Service hooks | [13-api-and-integrations.md](13-api-and-integrations.md) |
| GitHub repository integration | **Free** | Pipeline source selection | [13-api-and-integrations.md](13-api-and-integrations.md) |
| GitHub work item linking (AB# syntax) | **Free** | Org settings > GitHub connections | [13-api-and-integrations.md](13-api-and-integrations.md) |
| Slack notifications | Via extension or service hook | Marketplace or Project settings > Service hooks | [11-extensions-and-marketplace.md](11-extensions-and-marketplace.md) |
| Microsoft Teams notifications | Via extension or service hook | Marketplace | [11-extensions-and-marketplace.md](11-extensions-and-marketplace.md) |
| Azure DevOps Marketplace extensions | **Free** (installs vary) | marketplace.visualstudio.com | [11-extensions-and-marketplace.md](11-extensions-and-marketplace.md) |
| Azure DevOps CLI (`az devops`) | **Free** | Terminal | [12-azure-devops-cli.md](12-azure-devops-cli.md) |
| Service connections | **Free** | Project settings > Service connections | [13-api-and-integrations.md](13-api-and-integrations.md) |
| GitHub Advanced Security for ADO | **Paid add-on** | Project settings > Repos > Advanced Security | [13-api-and-integrations.md](13-api-and-integrations.md) |
| Azure DevOps MCP Server (AI access) | **Free** (preview) | Microsoft Foundry | [01-what-is-azure-devops.md](01-what-is-azure-devops.md) |
| Power BI connector | **Free** | Power BI Desktop | [04-azure-boards.md](04-azure-boards.md) |

---

## Azure DevOps Server (on-premises)

| Feature | Notes | Course file |
|---|---|---|
| On-premises deployment | Requires Server licence | [10-azure-devops-server.md](10-azure-devops-server.md) |
| Active Directory authentication (built-in) | No Azure AD required | [10-azure-devops-server.md](10-azure-devops-server.md) |
| SAML SSO (built-in) | No add-on required | [10-azure-devops-server.md](10-azure-devops-server.md) |
| Air-gapped operation | No internet required | [10-azure-devops-server.md](10-azure-devops-server.md) |
| Upgrade from TFS | Supported upgrade path required | [10-azure-devops-server.md](10-azure-devops-server.md) |
| No hosted agents | All builds use self-hosted agents | [10-azure-devops-server.md](10-azure-devops-server.md) |
| No AI features | AI features are Services-only | [01-what-is-azure-devops.md](01-what-is-azure-devops.md) |
| SQL Server Reporting Services (SSRS) | Legacy reports | [10-azure-devops-server.md](10-azure-devops-server.md) |
| Custom plugin API | Full extension SDK | [10-azure-devops-server.md](10-azure-devops-server.md) |
| Data sovereignty | Complete (your hardware) | [10-azure-devops-server.md](10-azure-devops-server.md) |

---

## Quick navigation

**I want to...**

- **Create my first project** → [02-creating-an-account.md](02-creating-an-account.md)
- **Set up CI/CD** → [05-azure-pipelines.md](05-azure-pipelines.md)
- **Manage code repositories** → [03-azure-repos.md](03-azure-repos.md)
- **Protect the main branch** → [08-branch-policies.md](08-branch-policies.md)
- **Track work items and sprints** → [04-azure-boards.md](04-azure-boards.md)
- **Publish packages** → [06-azure-artifacts.md](06-azure-artifacts.md)
- **Run manual tests** → [07-azure-test-plans.md](07-azure-test-plans.md)
- **Manage team access** → [09-security-and-permissions.md](09-security-and-permissions.md)
- **Use the command line** → [12-azure-devops-cli.md](12-azure-devops-cli.md)
- **Connect to an external service** → [13-api-and-integrations.md](13-api-and-integrations.md)
- **Install extensions** → [11-extensions-and-marketplace.md](11-extensions-and-marketplace.md)
- **Run on-premises** → [10-azure-devops-server.md](10-azure-devops-server.md)
- **Understand what Azure DevOps is** → [01-what-is-azure-devops.md](01-what-is-azure-devops.md)

---

## Feature comparison with GitHub and GitLab

A quick reference for the most commonly asked comparison points. Full comparison in the [bitbucket folder's comparison file](../bitbucket/03-bitbucket-vs-github-vs-gitlab.md).

| Feature | Azure DevOps | GitHub | GitLab |
|---|---|---|---|
| Free private repos | Unlimited | Unlimited | Unlimited |
| Free CI/CD minutes | 1,800/month | 2,000/month | 400/month |
| Hosted Windows agents | Yes | Yes | Limited |
| Hosted macOS agents | Yes | Yes | Limited (paid) |
| AI code completion | Via GitHub Copilot (separate) | GitHub Copilot (paid add-on) | Duo (Premium+) |
| Built-in project management | Azure Boards (strong) | GitHub Projects (lightweight) | GitLab (comprehensive) |
| Package registry | Azure Artifacts | GitHub Packages | GitLab Package Registry |
| Security scanning | GHAzDO (paid add-on) | GHAS ($49/user add-on) | Extensive (Ultimate) |
| Self-hosting (free) | No (Server requires licence) | No | Yes (GitLab CE) |
| SSO | Azure AD (built-in) | Team plan+ | Premium+ |
| JIRA integration | Native (via Boards) | Third-party | Third-party |
| Kanban + sprints | Full (Azure Boards) | Limited (Projects) | Full (Premium+) |
| Test management | Azure Test Plans (paid) | Third-party | Via third-party |
| On-premises option | Azure DevOps Server | GitHub Enterprise Server | GitLab CE/EE |
| API rate limit | 200 req/5 sec/user | 5,000 req/hour | 2,000 req/min |
| MFA enforcement | Via Azure AD | Organisation level | Group level |

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
