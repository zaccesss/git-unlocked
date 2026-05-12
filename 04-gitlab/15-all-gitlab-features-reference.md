# All GitLab Features Reference

**Difficulty:** 🟢 Beginner | **Time:** Reference only - jump to what you need

This file is a complete reference for every significant GitLab feature. Use it when you know what you are looking for but cannot remember where to find it, which tier it requires or which file in this course covers it in depth. Every feature is listed with its tier requirement, the UI navigation path to reach it and a link to the relevant course file.

Features are organised following GitLab's own left sidebar navigation structure so the headings match what you see in the product.

---

## Table of Contents

- [Tier legend](#tier-legend)
- [Plan - project management](#plan---project-management)
- [Code - source control](#code---source-control)
- [Build - CI/CD](#build---cicd)
- [Secure - application security](#secure---application-security)
- [Deploy - releasing software](#deploy---releasing-software)
- [Operate - running software](#operate---running-software)
- [Monitor - observability](#monitor---observability)
- [Analyse - analytics](#analyse---analytics)
- [AI features - GitLab Duo](#ai-features---gitlab-duo)
- [Settings - project level](#settings---project-level)
- [Settings - group level](#settings---group-level)
- [Group-level features](#group-level-features)
- [Self-managed only features](#self-managed-only-features)
- [GitLab.com only features](#gitlabcom-only-features)
- [Terminology quick reference](#terminology-quick-reference)
- [Feature tier matrix](#feature-tier-matrix)

---

## Tier Legend

| Symbol | Tier | Price on GitLab.com |
|---|---|---|
| **F** | Free | $0 |
| **P** | Premium | $29/user/month (annual only) |
| **U** | Ultimate | Contact sales (~$99/user/month) |

Features marked with a single tier are available at that tier and all higher tiers unless stated otherwise.

Duo add-on pricing (on top of base plan):
- **Duo Core**: included free with Premium and Ultimate (GitLab 18.0+)
- **Duo Pro**: $19/user/month, requires Premium or Ultimate
- **Duo Enterprise**: $39/user/month, requires Ultimate only

---

## Plan - Project Management

### Issues

| Feature | Tier | Navigation | Course file |
|---|---|---|---|
| Issues (create, view, manage) | F | Plan -> Work items (filter Type = Issue) | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Issue assignees (multiple) | F | Issue -> right sidebar -> Assignees | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Issue labels | F | Issue -> right sidebar -> Labels | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Issue due dates | F | Issue -> right sidebar -> Due date | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Issue health status | F | Issue -> right sidebar -> Health status | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Confidential issues | F | Issue -> Mark as confidential | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Sub-issues (child work items) | F | Issue -> Child items section | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Issue task lists (checkboxes) | F | Issue description (Markdown `- [ ]`) | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Linked issues (relates to, blocks) | F | Issue -> right sidebar -> Linked items | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Time tracking (/estimate, /spend) | F | Issue -> quick actions | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Quick actions | F | Any issue or MR comment (type /) | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Issue templates | F | .gitlab/issue_templates/ | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Issue boards (Kanban) | F | Plan -> Boards | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Milestones | F | Plan -> Milestones | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Burndown and burnup charts | F | Plan -> Milestones -> specific milestone | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Service Desk | F | Monitor -> Service Desk | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Wiki (project level) | F | Plan -> Wiki | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Snippets (project level) | F | Code -> Snippets | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Personal snippets | F | Your work -> Snippets | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Multiple issue boards per project | P | Plan -> Boards -> board selector | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Scoped labels (key::value) | P | Manage -> Labels (use :: in name) | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Issue weight | P | Issue -> right sidebar -> Weight | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Epics | P | Plan -> Work items (filter Type = Epic) | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Roadmaps (Gantt view of epics) | P | Plan -> Roadmap | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Iterations and iteration cadences | P | Plan -> Iterations | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Epic swimlanes on boards | P | Plan -> Boards -> Enable swimlanes | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Wiki (group level) | P | Group -> Plan -> Wiki | [08-issues-and-planning.md](08-issues-and-planning.md) |

---

## Code - Source Control

### Repositories and merge requests

| Feature | Tier | Navigation | Course file |
|---|---|---|---|
| Git repository hosting | F | Code -> Repository | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Repository file browser | F | Code -> Repository | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Commit history browser | F | Code -> Repository -> commits | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Branches list and management | F | Code -> Repository -> branches | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Tags list | F | Code -> Repository -> tags | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Web editor (single file) | F | Code -> Repository -> file -> Edit | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Web IDE (multi-file browser editor) | F | Code -> Repository -> Web IDE (or . shortcut) | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Forking | F | Project page -> Fork button | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Repository mirroring (push or pull) | F | Settings -> Repository -> Mirroring | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Merge requests | F | Code -> Merge requests | [05-merge-requests.md](05-merge-requests.md) |
| Draft merge requests | F | MR title prefix "Draft:" | [05-merge-requests.md](05-merge-requests.md) |
| Merge request templates | F | .gitlab/merge_request_templates/ | [05-merge-requests.md](05-merge-requests.md) |
| Inline code review comments | F | MR -> Changes tab | [05-merge-requests.md](05-merge-requests.md) |
| Suggested changes | F | MR -> Changes tab -> suggestion fence | [05-merge-requests.md](05-merge-requests.md) |
| Thread resolution | F | MR -> Overview -> Threads | [05-merge-requests.md](05-merge-requests.md) |
| Auto-merge | F | MR -> Set to auto-merge | [05-merge-requests.md](05-merge-requests.md) |
| Closing issues from MRs | F | MR description: Closes #N | [05-merge-requests.md](05-merge-requests.md) |
| Reverting a merged MR | F | Merged MR -> Revert button | [05-merge-requests.md](05-merge-requests.md) |
| Protected branches | F | Settings -> Repository -> Protected branches | [05-merge-requests.md](05-merge-requests.md) |
| Protected tags | F | Settings -> Repository -> Protected tags | [05-merge-requests.md](05-merge-requests.md) |
| Push rules (basic) | F | Settings -> Repository -> Push rules | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Deploy keys | F | Settings -> Repository -> Deploy keys | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Deploy tokens | F | Settings -> Repository -> Deploy tokens | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Required MR approvals | P | Settings -> Merge requests -> Approvals | [05-merge-requests.md](05-merge-requests.md) |
| CODEOWNERS enforcement | P | .gitlab/CODEOWNERS + protected branch rule | [05-merge-requests.md](05-merge-requests.md) |
| Fast-forward merge method | P | Settings -> Merge requests -> Merge method | [05-merge-requests.md](05-merge-requests.md) |
| Merge trains | P | Settings -> Merge requests -> Merge trains | [05-merge-requests.md](05-merge-requests.md) |
| Merged results pipelines | P | Settings -> Merge requests -> Merged results | [05-merge-requests.md](05-merge-requests.md) |
| MR dependencies | P | MR -> right sidebar -> Merge request dependencies | [05-merge-requests.md](05-merge-requests.md) |
| Push rules (advanced regex) | P | Settings -> Repository -> Push rules | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |

---

## Build - CI/CD

| Feature | Tier | Navigation | Course file |
|---|---|---|---|
| CI/CD pipelines | F | Build -> Pipelines | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Pipeline editor with lint | F | Build -> Pipeline editor | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Jobs | F | Build -> Jobs | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Artifacts | F | Job page -> Browse / Download | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Caching | F | .gitlab-ci.yml cache: keyword | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Scheduled pipelines | F | Build -> Pipeline schedules | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Pipeline triggers (API) | F | Settings -> CI/CD -> Pipeline triggers | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| CI/CD variables (project) | F | Settings -> CI/CD -> Variables | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Merge request pipelines | F | .gitlab-ci.yml rules: + CI_PIPELINE_SOURCE | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| DAG pipelines (needs:) | F | .gitlab-ci.yml needs: keyword | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Parallel matrix builds | F | .gitlab-ci.yml parallel: matrix: | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Child and parent pipelines | F | .gitlab-ci.yml trigger: keyword | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Multi-project pipelines | F | .gitlab-ci.yml trigger: project: | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Review apps | F | .gitlab-ci.yml environment: with dynamic names | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Auto DevOps | F | Settings -> CI/CD -> Auto DevOps | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| CI/CD Catalog (components) | F | Explore -> CI/CD Catalog | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Resource groups | F | .gitlab-ci.yml resource_group: | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Project-level runners | F | Settings -> CI/CD -> Runners | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Group-level runners | F | Group -> CI/CD -> Runners | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Protected environments | P | Operate -> Environments -> environment -> Edit | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Deployment approvals | P | Operate -> Environments -> Approval rules | [06-gitlab-cicd.md](06-gitlab-cicd.md) |

---

## Secure - Application Security

| Feature | Tier | Navigation | Course file |
|---|---|---|---|
| SAST (basic, open-source) | F | Secure -> Security configuration -> SAST | [09-security-features.md](09-security-features.md) |
| Secret Detection (pipeline) | F | Secure -> Security configuration -> Secret Detection | [09-security-features.md](09-security-features.md) |
| Secret Detection (push protection) | F | Secure -> Security configuration -> Secret Detection | [09-security-features.md](09-security-features.md) |
| Dependency Scanning (basic) | F | Secure -> Security configuration -> Dependency Scanning | [09-security-features.md](09-security-features.md) |
| Container Scanning (basic) | F | Secure -> Security configuration -> Container Scanning | [09-security-features.md](09-security-features.md) |
| IaC Scanning | F | Secure -> Security configuration -> IaC Scanning | [09-security-features.md](09-security-features.md) |
| MR security widget (basic) | F | MR -> Pipeline tab -> security job results | [09-security-features.md](09-security-features.md) |
| SAST Advanced (cross-file/function) | U | Secure -> Security configuration -> SAST | [09-security-features.md](09-security-features.md) |
| SAST false positive detection (Duo) | U | Secure -> Vulnerability report | [09-security-features.md](09-security-features.md) |
| DAST (browser-based, v5) | U | Secure -> Security configuration -> DAST | [09-security-features.md](09-security-features.md) |
| API Security Testing | U | Secure -> Security configuration -> API Security | [09-security-features.md](09-security-features.md) |
| Coverage-guided Fuzz Testing | U | Secure -> Security configuration -> Fuzz testing | [09-security-features.md](09-security-features.md) |
| Web API Fuzzing | U | Secure -> Security configuration -> API fuzzing | [09-security-features.md](09-security-features.md) |
| License Compliance | U | Secure -> License compliance | [09-security-features.md](09-security-features.md) |
| Vulnerability Report | U | Secure -> Vulnerability report | [09-security-features.md](09-security-features.md) |
| Security Policies | U | Secure -> Policies | [09-security-features.md](09-security-features.md) |
| Dependency List | U | Secure -> Dependency list | [09-security-features.md](09-security-features.md) |
| Compliance Center | U | Secure -> Compliance center | [09-security-features.md](09-security-features.md) |
| MR security widget (comparison) | U | MR -> Security tab | [09-security-features.md](09-security-features.md) |

---

## Deploy - Releasing Software

| Feature | Tier | Navigation | Course file |
|---|---|---|---|
| GitLab Pages | F | Deploy -> Pages | [07-gitlab-pages.md](07-gitlab-pages.md) |
| Pages custom domains | F | Deploy -> Pages -> New domain | [07-gitlab-pages.md](07-gitlab-pages.md) |
| Pages HTTPS (Let's Encrypt) | F | Deploy -> Pages -> domain settings | [07-gitlab-pages.md](07-gitlab-pages.md) |
| Pages access control | F | Settings -> General -> Visibility -> Pages | [07-gitlab-pages.md](07-gitlab-pages.md) |
| Releases | F | Deploy -> Releases | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Release assets and download links | F | Deploy -> Releases -> create/edit release | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Container Registry | F | Deploy -> Container registry | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |
| Container Registry cleanup policies | F | Settings -> Packages and registries -> Container registry | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |
| Package Registry (npm, PyPI, Maven, etc.) | F | Deploy -> Package registry | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |
| Terraform module registry | F | Deploy -> Terraform module registry | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |

---

## Operate - Running Software

| Feature | Tier | Navigation | Course file |
|---|---|---|---|
| Environments | F | Operate -> Environments | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Deployment history | F | Operate -> Environments -> environment -> Deployments | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Feature flags | F | Operate -> Feature flags | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Terraform state (HTTP backend) | F | Operate -> Terraform states | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |
| Kubernetes agent | F | Operate -> Kubernetes clusters | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Protected environments | P | Operate -> Environments -> Edit | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Deployment approvals | P | Operate -> Environments -> Approval rules | [06-gitlab-cicd.md](06-gitlab-cicd.md) |

---

## Monitor - Observability

| Feature | Tier | Navigation | Course file |
|---|---|---|---|
| Incident management | F | Monitor -> Incidents | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Error tracking | F | Monitor -> Error tracking | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Service Desk | F | Monitor -> Service Desk | [08-issues-and-planning.md](08-issues-and-planning.md) |
| On-call schedules | P | Monitor -> On-call schedules | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Escalation policies | P | Monitor -> Escalation policies | [08-issues-and-planning.md](08-issues-and-planning.md) |

---

## Analyse - Analytics

| Feature | Tier | Navigation | Course file |
|---|---|---|---|
| CI/CD analytics | F | Analyse -> CI/CD analytics | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Repository analytics | F | Analyse -> Repository analytics | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Issue analytics | P | Analyse -> Issue analytics | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Merge request analytics | P | Analyse -> Merge request analytics | [05-merge-requests.md](05-merge-requests.md) |
| Value stream analytics | P | Analyse -> Value stream analytics | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| GitLab Duo and SDLC trends | P | Analyse -> Analytics dashboards | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Contribution analytics (group) | U | Group -> Analyse -> Contribution analytics | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Code review analytics | U | Analyse -> Code review analytics | [05-merge-requests.md](05-merge-requests.md) |
| DevOps adoption (group) | U | Group -> Analyse -> DevOps adoption | [13-groups-and-permissions.md](13-groups-and-permissions.md) |

---

## AI Features - GitLab Duo

| Feature | Tier/Add-on | Access | Course file |
|---|---|---|---|
| Code Suggestions (inline completions) | Duo Core (free with P/U) | IDE extensions | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Duo Chat (non-agentic) | Duo Core | GitLab UI, IDE extensions | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Code Suggestions (advanced, multi-line) | Duo Pro ($19/user) | IDE extensions | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Test generation | Duo Pro | IDE extensions | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Duo Chat (agentic, multi-step) | Duo Pro | GitLab UI, IDE extensions | [10-gitlab-duo.md](10-gitlab-duo.md) |
| MR summaries | Duo Pro | MR -> Summary button | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Duo Agent Platform | Duo Pro | GitLab UI -> Duo | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Planner Agent | Duo Pro | Plan -> Duo | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Security Analyst Agent | Duo Pro | Secure -> Duo | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Developer Flow (issue to MR) | Duo Pro | Issue -> Duo | [10-gitlab-duo.md](10-gitlab-duo.md) |
| CI/CD Migration Flow | Duo Pro | Build -> Duo | [10-gitlab-duo.md](10-gitlab-duo.md) |
| MCP Client (external tools) | Duo Pro | Group -> Settings -> GitLab Duo | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Root Cause Analysis | Duo Enterprise ($39/user, U only) | Failed job -> Root cause analysis | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Vulnerability explanation | Duo Enterprise | Secure -> Vulnerability report | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Vulnerability resolution (AI fix) | Duo Enterprise | Secure -> Vulnerability -> Resolve with AI | [10-gitlab-duo.md](10-gitlab-duo.md) |
| AI Code Review | Duo Enterprise | MR -> Start AI review | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Issue description generation | Duo Enterprise | Issue -> Duo | [10-gitlab-duo.md](10-gitlab-duo.md) |
| GitLab Duo with Amazon Q | Separate add-on (self-managed only) | Settings -> GitLab Duo | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Self-hosted AI models | Duo Enterprise (self-managed) | gitlab.rb + AI Gateway | [10-gitlab-duo.md](10-gitlab-duo.md) |

---

## Settings - Project Level

All project settings are accessible from the left sidebar under **Settings** when you are inside a project.

| Setting | Navigation | Course file |
|---|---|---|
| General project settings (name, description, avatar) | Settings -> General | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Visibility and permissions | Settings -> General -> Visibility, project features, permissions | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Merge request settings (merge method, squash, approvals) | Settings -> Merge requests | [05-merge-requests.md](05-merge-requests.md) |
| Protected branches | Settings -> Repository -> Protected branches | [05-merge-requests.md](05-merge-requests.md) |
| Protected tags | Settings -> Repository -> Protected tags | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Repository mirroring | Settings -> Repository -> Mirroring repositories | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Deploy keys | Settings -> Repository -> Deploy keys | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Deploy tokens | Settings -> Repository -> Deploy tokens | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| CI/CD pipeline configuration | Settings -> CI/CD -> General pipelines | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Auto DevOps | Settings -> CI/CD -> Auto DevOps | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| CI/CD runners | Settings -> CI/CD -> Runners | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| CI/CD variables (project) | Settings -> CI/CD -> Variables | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Pipeline triggers | Settings -> CI/CD -> Pipeline triggers | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Token access (job token scope) | Settings -> CI/CD -> Token Access | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |
| Webhooks (project) | Settings -> Webhooks | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |
| Integrations (Jira, Slack, etc.) | Settings -> Integrations | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |
| Access tokens (project) | Settings -> Access tokens | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Container registry cleanup | Settings -> Packages and registries -> Container registry | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |
| Pages configuration | Settings -> General -> Visibility -> Pages | [07-gitlab-pages.md](07-gitlab-pages.md) |
| Archive project | Settings -> General -> Advanced -> Archive project | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Transfer project | Settings -> General -> Advanced -> Transfer project | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Delete project | Settings -> General -> Advanced -> Delete project | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |

---

## Settings - Group Level

All group settings are accessible from the group's left sidebar under **Settings**.

| Setting | Navigation | Course file |
|---|---|---|
| General group settings (name, URL, visibility) | Settings -> General | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Members and roles | Manage -> Members | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Group labels | Manage -> Labels | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Group milestones | Plan -> Milestones | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Group runners | CI/CD -> Runners | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Group CI/CD variables | Settings -> CI/CD -> Variables | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Group access tokens | Settings -> Access tokens | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| SAML SSO | Settings -> SAML SSO | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| SCIM provisioning | Settings -> SAML SSO -> Generate SCIM token | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Audit events (group) | Security & Compliance -> Audit events | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Group webhooks (Premium) | Settings -> Webhooks | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |
| Custom project templates | Settings -> General -> Custom project templates | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| GitLab Duo settings | Settings -> GitLab Duo | [10-gitlab-duo.md](10-gitlab-duo.md) |
| Transfer group | Settings -> General -> Advanced -> Transfer group | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Delete group | Settings -> General -> Advanced -> Remove group | [13-groups-and-permissions.md](13-groups-and-permissions.md) |

---

## Group-Level Features

Features that exist at the group level in addition to or instead of the project level.

| Feature | Tier | Description | Course file |
|---|---|---|---|
| Group labels | F | Labels shared across all projects in the group | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Group milestones | F | Milestones spanning multiple projects | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Group boards | F | Issue boards aggregating across projects | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Group runners | F | Runners available to all projects in the group | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Group CI/CD variables | F | Variables inherited by all projects and subgroups | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Group access tokens | P (GitLab.com) / F (self-managed) | Bot user credentials scoped to the group | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Subgroups (up to 20 levels) | F | Hierarchical group organisation | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| Group package registry | F | Shared package registry across projects | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |
| SAML SSO | P | Single sign-on for group members | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| SCIM provisioning | P | Automatic user lifecycle management | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Group wikis | P | Shared wiki for the entire group | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Group webhooks | P | Webhooks for events in any project in the group | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |
| Epics (group-level) | P | Large initiatives spanning multiple projects | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Group roadmaps | P | Timeline view of group epics | [08-issues-and-planning.md](08-issues-and-planning.md) |
| Security dashboard (group) | U | Aggregated vulnerabilities across all projects | [09-security-features.md](09-security-features.md) |
| Compliance frameworks | U | Apply compliance rules across projects | [09-security-features.md](09-security-features.md) |
| Audit events (group) | P | Change and access log across the group | [13-groups-and-permissions.md](13-groups-and-permissions.md) |
| Value stream analytics (group) | P | Aggregated delivery metrics across projects | [06-gitlab-cicd.md](06-gitlab-cicd.md) |

---

## Self-Managed Only Features

These features are only available on self-managed GitLab installations (not on GitLab.com).

| Feature | Description | Course file |
|---|---|---|
| Admin Area | Full instance administration interface | [11-self-hosting.md](11-self-hosting.md) |
| Instance-level SAML | Configure SSO at the server level (affects all users) | [11-self-hosting.md](11-self-hosting.md) |
| LDAP authentication | Authenticate against Active Directory or OpenLDAP | [11-self-hosting.md](11-self-hosting.md) |
| Internal visibility level | Projects visible to all authenticated instance users | [04-projects-and-namespaces.md](04-projects-and-namespaces.md) |
| System webhooks | Instance-wide webhooks for all events | [14-gitlab-api-and-integrations.md](14-gitlab-api-and-integrations.md) |
| Geo (multi-region replication) | Secondary read-only instances for disaster recovery | [11-self-hosting.md](11-self-hosting.md) |
| Elasticsearch integration | Advanced code and commit search at scale | [11-self-hosting.md](11-self-hosting.md) |
| Unlimited storage per project | No storage caps (limited only by disk) | [11-self-hosting.md](11-self-hosting.md) |
| Unlimited compute minutes | No CI/CD minute limits with own runners | [11-self-hosting.md](11-self-hosting.md) |
| Custom gitlab.rb configuration | Full control over every platform setting | [11-self-hosting.md](11-self-hosting.md) |
| Backup and restore | gitlab-backup create / restore commands | [11-self-hosting.md](11-self-hosting.md) |
| Prometheus and Grafana | Built-in monitoring with pre-configured dashboards | [11-self-hosting.md](11-self-hosting.md) |
| GitLab Pages admin configuration | Enable Pages, configure wildcard DNS | [07-gitlab-pages.md](07-gitlab-pages.md) |
| Container Registry admin config | Configure external storage (S3, GCS) | [11-self-hosting.md](11-self-hosting.md) |
| Duo self-hosted models | Run AI models on your own infrastructure | [10-gitlab-duo.md](10-gitlab-duo.md) |

---

## GitLab.com Only Features

These features are specific to the hosted GitLab.com service.

| Feature | Description | Course file |
|---|---|---|
| Instance runners (GitLab-hosted) | Linux, Windows and macOS runners managed by GitLab | [06-gitlab-cicd.md](06-gitlab-cicd.md) |
| Identity verification (risk-based) | Phone and credit card verification for shared runners | [02-creating-an-account.md](02-creating-an-account.md) |
| 5-user limit (Free tier private groups) | Applies to top-level groups on GitLab.com Free | [02-creating-an-account.md](02-creating-an-account.md) |
| 3 top-level group limit (post Jan 2026) | New accounts created after 27 Jan 2026 | [02-creating-an-account.md](02-creating-an-account.md) |
| GitLab Dedicated | Single-tenant managed instance on GitLab's infrastructure | [01-what-is-gitlab.md](01-what-is-gitlab.md) |
| SaaS subscription management | Manage billing at gitlab.com/billing | [01-what-is-gitlab.md](01-what-is-gitlab.md) |

---

## Terminology Quick Reference

If you are coming from GitHub, this table maps every GitHub term to its GitLab equivalent.

| GitHub | GitLab | Notes |
|---|---|---|
| Repository | Project | GitLab project includes repo + issues + pipelines + registry + more |
| Pull request | Merge request (MR) | Functionally identical; GitLab's name is more accurate |
| GitHub Actions workflow | .gitlab-ci.yml | Different YAML syntax; GitLab uses one file at repo root |
| Actions runner | GitLab Runner | Same concept; install and register to your project/group |
| Organisation | Group | GitLab groups support subgroup nesting; GitHub orgs do not |
| Team | Subgroup or role | GitLab uses roles for access control; subgroups for structure |
| Branch protection rules | Protected branches | Similar; GitLab requires Premium for required approvals |
| CODEOWNERS (enforcement) | CODEOWNERS (enforcement) | Same file format; enforcement is Premium on GitLab |
| GitHub Pages | GitLab Pages | Both free; GitLab uses CI/CD pipeline for builds |
| Gist | Snippet | GitLab Snippets support multiple files; Gists do not |
| GitHub Copilot | GitLab Duo | Different models, different integration depth |
| Dependabot | Dependency Scanning | Different implementation; both check vulnerable packages |
| Secret scanning | Secret Detection | Both scan for leaked credentials |
| GitHub Advanced Security | GitLab Ultimate security | GitLab includes DAST/fuzz; GitHub does not |
| GitHub Enterprise Server | GitLab self-managed (EE) | GitHub: $21/user/month; GitLab CE: free |
| GitHub Marketplace | GitLab integrations + CI/CD Catalog | GitLab builds more natively; GitHub has larger marketplace |
| GitHub Discussions | (no direct equivalent) | GitLab has no community forum feature |
| GitHub Sponsors | (no direct equivalent) | GitLab has no developer sponsorship mechanism |
| GitHub Codespaces | GitLab Workspaces | GitLab Workspaces is in beta as of 2026 |
| github.dev (. shortcut) | Web IDE (. shortcut) | Both open a browser-based editor on any repository |

---

## Feature Tier Matrix

A condensed view of the most commonly asked "which tier is this?" questions.

| Feature | Free | Premium | Ultimate |
|---|---|---|---|
| Unlimited public and private repositories | Yes | Yes | Yes |
| Issues, boards, milestones | Yes | Yes | Yes |
| CI/CD pipelines | Yes (400 mins) | Yes (10K mins) | Yes (50K mins) |
| Container and Package Registries | Yes | Yes | Yes |
| GitLab Pages | Yes | Yes | Yes |
| Basic security scanning (SAST, secret detection, dependency, container, IaC) | Yes | Yes | Yes |
| Service Desk | Yes | Yes | Yes |
| Group runners | Yes | Yes | Yes |
| Group CI/CD variables | Yes | Yes | Yes |
| Required MR approvals | No | Yes | Yes |
| CODEOWNERS enforcement | No | Yes | Yes |
| Scoped labels (key::value) | No | Yes | Yes |
| Epics and roadmaps | No | Yes | Yes |
| Iterations (sprints) | No | Yes | Yes |
| Merge trains | No | Yes | Yes |
| Merged results pipelines | No | Yes | Yes |
| Protected environments | No | Yes | Yes |
| Group wikis | No | Yes | Yes |
| SAML SSO | No | Yes | Yes |
| Advanced SAST (cross-file) | No | No | Yes |
| DAST | No | No | Yes |
| Fuzz testing | No | No | Yes |
| License compliance | No | No | Yes |
| Vulnerability management dashboard | No | No | Yes |
| Security policies | No | No | Yes |
| Duo Core (Code Suggestions, Chat) | No | Yes (included) | Yes (included) |
| Duo Pro (advanced AI + agents) | No | +$19/user | +$19/user |
| Duo Enterprise (RCA, vuln resolution) | No | No | +$39/user |
| Users per private group | 5 max | Unlimited | Unlimited |

---

## Sources and Further Reading

- [GitLab feature comparison](https://about.gitlab.com/pricing/feature-comparison/) - official tier-by-tier feature breakdown
- [GitLab documentation home](https://docs.gitlab.com) - complete reference for every feature
- [GitLab 18.10 release post](https://about.gitlab.com/releases/2026/03/19/gitlab-18-10-released/) - latest version highlights
- [GitLab pricing](https://about.gitlab.com/pricing/) - current plan details and limits
- [GitLab community programmes](https://docs.gitlab.com/subscriptions/community_programs/) - Education, Open Source and Startups programmes
- [GitLab Duo features](https://docs.gitlab.com/user/gitlab_duo/feature_summary/) - complete AI feature availability by tier

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
