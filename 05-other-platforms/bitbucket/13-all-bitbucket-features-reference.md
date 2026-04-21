# All Bitbucket Features Reference

**Difficulty:** 🟢 Beginner | **Time:** Reference document - use as needed

This file is the complete reference for every Bitbucket Cloud feature. Use it to quickly find where a feature lives, which plan it requires, and which file in this folder covers it in depth. Features are organised by category, matching the way they appear in the Bitbucket interface.

---

## Table of Contents

1. [Plan quick reference](#plan-quick-reference)
2. [Repository features](#repository-features)
3. [Code review and pull requests](#code-review-and-pull-requests)
4. [Branch management](#branch-management)
5. [CI/CD - Bitbucket Pipelines](#cicd---bitbucket-pipelines)
6. [Workspace and team management](#workspace-and-team-management)
7. [Security and access control](#security-and-access-control)
8. [Integrations](#integrations)
9. [API and developer tools](#api-and-developer-tools)
10. [Data Center exclusive features](#data-center-exclusive-features)
11. [Deprecated and sunset features](#deprecated-and-sunset-features)
12. [Feature comparison with GitHub and GitLab](#feature-comparison-with-github-and-gitlab)

---

## Plan quick reference

All features in this reference are marked with the minimum plan required:

| Badge | Plan | Price (2026) |
|---|---|---|
| **Free** | Free | $0, up to 5 users |
| **Standard** | Standard | $3.65/user/month |
| **Premium** | Premium | $7.25/user/month |
| **Guard** | Atlassian Guard (add-on) | $4.20/user/month (Guard Standard) |
| **DC** | Data Center only | Annual licence |
| **All** | All plans | Free and above |

---

## Repository features

| Feature | Plan | Where to find it | Course file |
|---|---|---|---|
| Create repository | **All** | Workspace sidebar > + | [04-repositories.md](04-repositories.md) |
| Private repositories | **All** | Repository settings > Repository details | [04-repositories.md](04-repositories.md) |
| Public repositories | **All** | Repository settings > Repository details | [04-repositories.md](04-repositories.md) |
| Unlimited repositories | **All** | No limit on any plan | [04-repositories.md](04-repositories.md) |
| Clone via SSH | **All** | Repository > Clone > SSH | [02-creating-an-account.md](02-creating-an-account.md) |
| Clone via HTTPS | **All** | Repository > Clone > HTTPS | [02-creating-an-account.md](02-creating-an-account.md) |
| Fork a repository | **All** | Repository > ... menu > Fork | [04-repositories.md](04-repositories.md) |
| Repository transfer | **All** | Repository settings > Transfer repository | [04-repositories.md](04-repositories.md) |
| Repository deletion | **All** | Repository settings > Delete repository | [04-repositories.md](04-repositories.md) |
| Import from GitHub/GitLab | **All** | Workspace > + > Import repository | [04-repositories.md](04-repositories.md) |
| Git Large File Storage (LFS) | **All** | Enabled per-repository via .gitattributes | [04-repositories.md](04-repositories.md) |
| LFS storage 1 GB | **Free** | Included | [04-repositories.md](04-repositories.md) |
| LFS storage 5 GB | **Standard** | Included | [04-repositories.md](04-repositories.md) |
| LFS storage 10 GB | **Premium** | Included | [04-repositories.md](04-repositories.md) |
| Branching model (Gitflow) | **All** | Repository settings > Branching model | [04-repositories.md](04-repositories.md) |
| Repository variables | **All** | Repository settings > Repository variables | [04-repositories.md](04-repositories.md) |
| Deployment environments | **All** | Repository settings > Deployments | [04-repositories.md](04-repositories.md) |
| Deployment permissions (manual gate) | **Premium** | Repository settings > Deployments > environment | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Repository access keys (SSH) | **All** | Repository settings > Access keys | [09-security-features.md](09-security-features.md) |
| Webhooks (repository level) | **All** | Repository settings > Webhooks | [12-api-and-integrations.md](12-api-and-integrations.md) |
| Repository permissions | **All** | Repository settings > User and group access | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Bitbucket Packages (container registry) | **Standard** | Repository > Packages | [00-bitbucket-overview.md](00-bitbucket-overview.md) |
| Secret scanning | **Standard** | Repository > Security | [09-security-features.md](09-security-features.md) |
| Native Issues | Being sunset | Repository > Issues (legacy) | [01-what-is-bitbucket.md](01-what-is-bitbucket.md) |
| Wiki | Being sunset | Repository > Wiki (legacy) | [01-what-is-bitbucket.md](01-what-is-bitbucket.md) |

---

## Code review and pull requests

| Feature | Plan | Where to find it | Course file |
|---|---|---|---|
| Create pull request | **All** | Repository > Pull requests > Create | [05-pull-requests.md](05-pull-requests.md) |
| Draft pull requests | **All** | Create PR > Mark as draft | [05-pull-requests.md](05-pull-requests.md) |
| Pull request templates | **All** | `.bitbucket/pull-request-description-template.md` | [05-pull-requests.md](05-pull-requests.md) |
| Inline comments on code | **All** | PR > Diff tab > hover over line | [05-pull-requests.md](05-pull-requests.md) |
| Resolve comment threads | **All** | PR > comment > Resolve | [05-pull-requests.md](05-pull-requests.md) |
| PR tasks (action items) | **All** | PR > comment > task icon | [05-pull-requests.md](05-pull-requests.md) |
| Approve a pull request | **All** | PR > Approve button | [05-pull-requests.md](05-pull-requests.md) |
| Revoke approval | **All** | PR > reviewer dropdown > Revoke | [05-pull-requests.md](05-pull-requests.md) |
| Decline a pull request | **All** | PR > Decline button | [05-pull-requests.md](05-pull-requests.md) |
| Merge commit strategy | **All** | PR > Merge > Merge commit | [05-pull-requests.md](05-pull-requests.md) |
| Squash merge strategy | **All** | PR > Merge > Squash | [05-pull-requests.md](05-pull-requests.md) |
| Fast-forward merge strategy | **All** | PR > Merge > Fast-forward | [05-pull-requests.md](05-pull-requests.md) |
| Close source branch on merge | **All** | PR > merge options | [05-pull-requests.md](05-pull-requests.md) |
| Default reviewers | **All** | Repository settings > Default reviewers | [05-pull-requests.md](05-pull-requests.md) |
| Required approvals (advisory) | **Free/Standard** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Required approvals (enforced) | **Premium** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Author cannot approve own PR | **Premium** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Reset approvals on new commits | **Premium** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| No unresolved tasks merge check | **Premium** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| No failed builds merge check | **Premium** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Jira issue linking in PRs | **All** | Automatic when issue key in branch/title | [08-jira-integration.md](08-jira-integration.md) |
| Build status on PRs | **All** | Automatic via Pipelines or status API | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| AI pull request descriptions (Rovo Dev) | **Standard** | PR create form > AI generate | [01-what-is-bitbucket.md](01-what-is-bitbucket.md) |

---

## Branch management

| Feature | Plan | Where to find it | Course file |
|---|---|---|---|
| Create branch (web) | **All** | Repository > Branches > Create branch | [04-repositories.md](04-repositories.md) |
| Create branch from Jira | **All** | Jira issue > Development panel > Create branch | [08-jira-integration.md](08-jira-integration.md) |
| Delete branch (web) | **All** | Repository > Branches > ... > Delete | [04-repositories.md](04-repositories.md) |
| Branch write restriction | **All** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Branch delete restriction | **All** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Force push restriction | **All** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Merge restriction | **All** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Branch pattern matching (glob) | **All** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Branching model / Gitflow | **All** | Repository settings > Branching model | [04-repositories.md](04-repositories.md) |
| Tags (create/delete) | **All** | Repository > Tags | [04-repositories.md](04-repositories.md) |

---

## CI/CD - Bitbucket Pipelines

Pipelines is a Bitbucket Cloud-only feature. Bitbucket Data Center has no built-in CI/CD.

| Feature | Plan | Where to find it | Course file |
|---|---|---|---|
| Bitbucket Pipelines | **All** | `bitbucket-pipelines.yml` in repo root | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Pipeline minutes 50/month | **Free** | Workspace settings > Plans and billing | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Pipeline minutes 2,500/month | **Standard** | Workspace settings > Plans and billing | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Pipeline minutes 3,500/month | **Premium** | Workspace settings > Plans and billing | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Default pipeline trigger | **All** | `pipelines: default:` in YAML | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Branch-specific pipeline | **All** | `pipelines: branches:` in YAML | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Pull request pipeline | **All** | `pipelines: pull-requests:` in YAML | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Tag pipeline | **All** | `pipelines: tags:` in YAML | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Custom (manual) pipeline | **All** | `pipelines: custom:` in YAML | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Pipeline stages | **All** | `stage:` block in YAML | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Parallel steps | **All** | Multiple `step:` blocks in a `stage:` | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Per-step Docker image | **All** | `image:` in step | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Step size (2x resources) | **All** | `size: 2x` in step | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Caching dependencies | **All** | `definitions: caches:` + `caches:` in step | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Step artifacts | **All** | `artifacts:` in step | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Services (DB, Redis etc.) | **All** | `definitions: services:` + `services:` in step | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Docker-in-Docker | **All** | `docker` service in step | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Pipes (pre-built integrations) | **All** | `pipe:` in step script | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Manual trigger step | **All** | `trigger: manual` on step | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Conditional steps (changeset) | **All** | `condition: changesets:` on step | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| After-script (always runs) | **All** | `after-script:` in step | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Repository variables (secured) | **All** | Repository settings > Repository variables | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Workspace variables | **All** | Workspace settings > Workspace variables | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Deployment environments | **All** | Repository settings > Deployments | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Deployment permissions (gate) | **Premium** | Deployment environment settings | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Self-hosted runners (basic, 100 free) | **All** | Workspace settings > Runners | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Self-hosted runners (premium) | **All** | $15/slot/month | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| OIDC secretless auth | **All** (V5 runners) | `oidc: true` on step | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Shallow clone | **All** | `clone: depth: 1` in YAML | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Re-run failed pipeline | **All** | Pipeline run > Re-run | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| SSH debug access to pipeline | **All** | Pipeline run > SSH | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |

---

## Workspace and team management

| Feature | Plan | Where to find it | Course file |
|---|---|---|---|
| Create workspace | **All** | Avatar > Create workspace | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Workspace name/slug | **All** | Workspace settings > Overview | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Invite members | **All** | Workspace settings > Members > Invite | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Remove members | **All** | Workspace settings > Members | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Free plan: up to 5 members | **Free** | Workspace settings > Members | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Unlimited members | **Standard/Premium** | Workspace settings > Members | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Workspace Owner role | **All** | Workspace settings > Members > role | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Workspace Admin role | **All** | Workspace settings > Members > role | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Workspace Member role | **All** | Workspace settings > Members > role | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Create groups | **All** | Workspace settings > Groups | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Manage group membership | **All** | Workspace settings > Groups > group name | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Grant group repo access | **All** | Repository settings > User and group access | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Create projects | **All** | Workspace > Projects > Create project | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Project permissions | **All** | Project settings > User and group access | [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md) |
| Workspace webhooks | **All** | Workspace settings > Webhooks | [12-api-and-integrations.md](12-api-and-integrations.md) |
| Workspace-level Jira integration | **All** | Workspace settings > Integrations > Jira | [08-jira-integration.md](08-jira-integration.md) |

---

## Security and access control

| Feature | Plan | Where to find it | Course file |
|---|---|---|---|
| Two-factor authentication (personal) | **All** | id.atlassian.com > Security | [02-creating-an-account.md](02-creating-an-account.md) |
| 2FA enforcement (workspace) | **All** | Workspace settings > Security | [09-security-features.md](09-security-features.md) |
| SSH keys (personal) | **All** | Personal settings > SSH keys | [02-creating-an-account.md](02-creating-an-account.md) |
| Repository access keys | **All** | Repository settings > Access keys | [09-security-features.md](09-security-features.md) |
| API tokens | **All** | Personal settings > API tokens | [02-creating-an-account.md](02-creating-an-account.md) |
| API token expiry | **All** | Set when creating token | [02-creating-an-account.md](02-creating-an-account.md) |
| API token permission scopes | **All** | Set when creating token | [02-creating-an-account.md](02-creating-an-account.md) |
| App passwords (deprecated) | Removed June 2026 | Personal settings > App passwords | [02-creating-an-account.md](02-creating-an-account.md) |
| Branch write restriction | **All** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Branch delete restriction | **All** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Force push restriction | **All** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Minimum approvals (advisory) | **Free/Standard** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Minimum approvals (enforced) | **Premium** | Repository settings > Branch permissions | [07-branch-permissions.md](07-branch-permissions.md) |
| Secret scanning | **Standard** | Repository > Security tab | [09-security-features.md](09-security-features.md) |
| IP allowlisting | **Premium** | Workspace settings > Security > IP allowlisting | [09-security-features.md](09-security-features.md) |
| Audit log | **Premium** | Workspace settings > Audit log | [09-security-features.md](09-security-features.md) |
| SAML SSO | **Guard Standard** | Atlassian Guard admin panel | [09-security-features.md](09-security-features.md) |
| SCIM provisioning | **Guard Standard** | Atlassian Guard admin panel | [09-security-features.md](09-security-features.md) |
| Authentication policies | **Guard Standard** | Atlassian Guard admin panel | [09-security-features.md](09-security-features.md) |
| API token management (org-wide) | **Guard Standard** | Atlassian Guard admin panel | [09-security-features.md](09-security-features.md) |
| Anomaly detection | **Guard Premium** | Atlassian Guard admin panel | [09-security-features.md](09-security-features.md) |
| SIEM integration | **Guard Premium** | Atlassian Guard admin panel | [09-security-features.md](09-security-features.md) |
| Data residency (region control) | **Standard+** | Atlassian admin > Data residency | [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md) |
| 99.9% uptime SLA | **Premium** | Contractual | [00-bitbucket-overview.md](00-bitbucket-overview.md) |

---

## Integrations

| Feature | Plan | Where to find it | Course file |
|---|---|---|---|
| Jira development panel | **All** | Jira issue > Development panel | [08-jira-integration.md](08-jira-integration.md) |
| Smart commits | **All** | In commit message syntax | [08-jira-integration.md](08-jira-integration.md) |
| Create branch from Jira | **All** | Jira issue > Create branch | [08-jira-integration.md](08-jira-integration.md) |
| Build status in Jira | **All** | Jira issue > Development panel > Builds | [08-jira-integration.md](08-jira-integration.md) |
| Deployment status in Jira | **All** | Jira issue > Development panel > Deployments | [08-jira-integration.md](08-jira-integration.md) |
| Jira automation triggers | **All** | Jira > Project settings > Automation | [08-jira-integration.md](08-jira-integration.md) |
| Atlassian Marketplace apps | **All** | Workspace settings > Installed apps | [12-api-and-integrations.md](12-api-and-integrations.md) |
| Slack notifications | Via Marketplace or pipe | Marketplace or pipeline config | [12-api-and-integrations.md](12-api-and-integrations.md) |
| Microsoft Teams notifications | Via Marketplace | Marketplace | [12-api-and-integrations.md](12-api-and-integrations.md) |
| SonarQube / SonarCloud | Via Marketplace or pipe | Marketplace or pipeline config | [12-api-and-integrations.md](12-api-and-integrations.md) |
| Snyk security scanning | Via pipe | Pipeline config | [12-api-and-integrations.md](12-api-and-integrations.md) |
| AWS integrations | Via pipes | Pipeline config | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| Azure integrations | Via pipes | Pipeline config | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| GCP integrations | Via pipes | Pipeline config | [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md) |
| SourceTree (free GUI client) | **All** | sourcetreeapp.com (separate download) | IDEs folder |

---

## API and developer tools

| Feature | Plan | Where to find it | Course file |
|---|---|---|---|
| REST API v2 | **All** | api.bitbucket.org/2.0 | [12-api-and-integrations.md](12-api-and-integrations.md) |
| OAuth 2.0 | **All** | Workspace settings > OAuth consumers | [12-api-and-integrations.md](12-api-and-integrations.md) |
| API tokens | **All** | Personal settings > API tokens | [12-api-and-integrations.md](12-api-and-integrations.md) |
| Workspace access tokens | **All** | Workspace settings > Access tokens | [12-api-and-integrations.md](12-api-and-integrations.md) |
| Repository webhooks | **All** | Repository settings > Webhooks | [12-api-and-integrations.md](12-api-and-integrations.md) |
| Workspace webhooks | **All** | Workspace settings > Webhooks | [12-api-and-integrations.md](12-api-and-integrations.md) |
| Commit status API | **All** | POST /repositories/{ws}/{repo}/commit/{sha}/statuses/build | [12-api-and-integrations.md](12-api-and-integrations.md) |
| Pipelines API (trigger/monitor) | **All** | POST /repositories/{ws}/{repo}/pipelines/ | [12-api-and-integrations.md](12-api-and-integrations.md) |
| Pull requests API | **All** | /repositories/{ws}/{repo}/pullrequests | [12-api-and-integrations.md](12-api-and-integrations.md) |
| API rate limit | **All** | 1,000 requests/hour (authenticated) | [12-api-and-integrations.md](12-api-and-integrations.md) |
| API interactive docs | **All** | developer.atlassian.com/cloud/bitbucket/rest | [12-api-and-integrations.md](12-api-and-integrations.md) |

---

## Data Center exclusive features

These features are only available in Bitbucket Data Center (self-hosted). They do not exist in Bitbucket Cloud.

| Feature | Notes | Course file |
|---|---|---|
| LDAP/Active Directory authentication | Built into DC, no add-on required | [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md) |
| SAML SSO (built-in) | Built into DC, no Guard required | [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md) |
| Active-Active clustering | Multiple app nodes for high availability | [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md) |
| Smart Mirroring | Read-only replica nodes at geographic locations | [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md) |
| Custom plugin API | Full plugin development with Atlassian SDK | [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md) |
| Atlassian Marketplace (DC apps) | Separate DC-specific app ecosystem | [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md) |
| Crowd integration | Central SSO across all self-hosted Atlassian products | [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md) |
| Air-gapped deployment | No internet connection required | [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md) |
| Full data sovereignty | All data on your own hardware | [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md) |
| Upgrade control | You choose when to upgrade | [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md) |

---

## Deprecated and sunset features

These features existed in Bitbucket but have been removed or are being removed.

| Feature | Status | Replacement | Notes |
|---|---|---|---|
| App passwords | Removed June 2026 | API tokens | New creation blocked September 2025 |
| Mercurial repositories | Removed May 2020 | Git | All Hg repos deleted May 2020 |
| Bitbucket Server | End of life February 2024 | Bitbucket Data Center | No more patches |
| API v1 | Removed 2019 | API v2 | All v1 endpoints return 404 |
| Native Issues | Being sunset (Cloud) | Jira | Migrate issues to Jira |
| Native Wiki | Being sunset (Cloud) | Confluence | Migrate wikis to Confluence |
| OAuth 1.0 | Removed March 2026 | OAuth 2.0 | All OAuth 1.0 flows removed |
| OAuth implicit grant | Removed March 2026 | OAuth 2.0 auth code flow | |
| Email-based commenting | Removed March 2025 | Web interface commenting | |
| Cross-workspace APIs (some) | Sunset April 14 2026 | Repository-scoped APIs | Check Bitbucket changelog |
| Pre-V5 self-hosted runners | June 2026 (monthly) / December 2026 (annual) | V5 runners | Upgrade before deadline |
| Bamboo Data Center (Atlassian CI) | Winding down | Bitbucket Pipelines (Cloud) | Check Atlassian announcements |
| Elasticsearch for DC | Removed in DC 9.0 | OpenSearch | Migrate to OpenSearch |
| Windows support for DC | Removed in DC 8.0 | Linux only | DC runs Linux only |

---

## Feature comparison with GitHub and GitLab

A quick reference for the most commonly asked comparison points. Full comparison in [03-bitbucket-vs-github-vs-gitlab.md](03-bitbucket-vs-github-vs-gitlab.md).

| Feature | Bitbucket | GitHub | GitLab |
|---|---|---|---|
| Free private repos | Unlimited | Unlimited | Unlimited |
| Free users | 5 per workspace | Unlimited | Unlimited |
| Free CI/CD minutes | 50/month | 2,000/month | 400/month |
| Jira integration | Native (deepest) | Third-party | Third-party |
| Pages (static hosting) | None | Yes (free) | Yes (free) |
| AI code completion | Rovo Dev (Standard+) | Copilot (paid add-on) | Duo (Premium+) |
| Security scanning | Basic (Standard+) | GHAS ($49/user add-on) | Extensive (Ultimate) |
| Self-hosting (free) | No | No | Yes (GitLab CE) |
| Built-in CI/CD | Pipelines (Cloud only) | Actions | CI/CD (comprehensive) |
| Package registry | Yes (Standard+, limited) | Yes (GitHub Packages) | Yes (extensive) |
| SSO | Guard add-on ($4.20/user) | Team plan+ | Premium+ |
| macOS/Windows runners | Self-hosted only | Yes (hosted) | Limited |
| Open source community | Minimal | Dominant | Moderate |
| Suggested code changes in PR | No | Yes | Yes |
| Merge queues | No | Enterprise | Premium+ |
| Mobile app | Limited | Full-featured | Moderate |

---

## Quick navigation: find a feature

**I want to...**

- **Set up my account** → [02-creating-an-account.md](02-creating-an-account.md)
- **Create my first repository** → [04-repositories.md](04-repositories.md)
- **Set up CI/CD** → [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md)
- **Protect my main branch** → [07-branch-permissions.md](07-branch-permissions.md)
- **Connect to Jira** → [08-jira-integration.md](08-jira-integration.md)
- **Compare Bitbucket to GitHub** → [03-bitbucket-vs-github-vs-gitlab.md](03-bitbucket-vs-github-vs-gitlab.md)
- **Understand self-hosting** → [10-bitbucket-cloud-vs-data-center.md](10-bitbucket-cloud-vs-data-center.md)
- **Manage team access** → [11-workspaces-and-permissions.md](11-workspaces-and-permissions.md)
- **Use the API** → [12-api-and-integrations.md](12-api-and-integrations.md)
- **Secure my workspace** → [09-security-features.md](09-security-features.md)
- **Review pull requests** → [05-pull-requests.md](05-pull-requests.md)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
