# Changelog

All notable changes to git-unlocked are documented here.

This project follows [Semantic Versioning](https://semver.org). The format is based on [Keep a Changelog](https://keepachangelog.com).

---

## Version format

```
MAJOR.MINOR.PATCH

MAJOR - significant restructure or complete section overhaul
MINOR - new files, new sections or meaningful content additions
PATCH - typo fixes, small corrections, link updates
```

| Tag       | Meaning                                 |
| --------- | --------------------------------------- |
| `Added`   | New files or sections                   |
| `Updated` | Existing content improved or expanded   |
| `Fixed`   | Typos, errors or broken links corrected |
| `Removed` | Content removed                         |
| `Renamed` | Files or folders renamed                |

---

## [Unreleased]

In progress or planned - not yet in a release.

- All `ides/` folder content
- All `terminal/` folder content
- All `real-world/` folder content
- All `reference/` folder content
- All `resources/` folder content
- `first-contribution/` sandbox
- `assets/` banner and diagrams
- `introduction/02-how-to-use-this-course.md` - full rewrite once all folders complete
- `introduction/04-version-control-concepts.md` - new file explaining what version control is before Git specifics
- Codespaces dev container setup
- Root-level setup documentation

---

## [0.9.0] - 2026-04-09

### Added

- `other-platforms/codeberg/00-codeberg-overview.md` - folder navigation, Forgejo relationship, OS coverage note, prerequisites
- `other-platforms/codeberg/01-what-is-codeberg.md` - Codeberg e.V. (German non-profit e.V. structure, democratic membership, no investor pressure), history (Gitea hosting → Forgejo stewardship after 2022 governance dispute), how it differs from GitHub/GitLab.com (governance/data/sustainability model/AI features/scale), what Codeberg provides (free repos, Pages, Woodpecker CI, package registry, federation), who uses it (free software advocates, privacy-conscious devs, European public sector, NLnet-funded projects), limitations (smaller ecosystem, CI friction, no SLA, no private org repos on free tier)
- `other-platforms/codeberg/02-creating-an-account.md` - registration at codeberg.org (hCaptcha, email verification), username considerations (federated identity implications), profile settings, 2FA setup (Aegis recommended, recovery codes), SSH key generation on all three OS, access token creation with scopes, federated identity (`@username@codeberg.org`), ActivityPub discoverability, Codeberg-specific limits, supporting Codeberg financially
- `other-platforms/codeberg/03-repositories-and-code-review.md` - creating repositories, HTTPS and SSH clone, importing from GitHub (full migration with issues/PRs/labels/milestones), git push --mirror for code-only migrations, pull mirrors, push mirrors to GitHub, pull request workflow, code review (inline comments, suggestions, review submission), branch protection, PR templates, topics and discoverability, wikis, Codeberg-specific limits (storage fair use, private org repos)
- `other-platforms/codeberg/04-issues-and-projects.md` - issue management for public open source projects (triage patterns, duplicate tracking, upstream labels, stale management), labels setup (type/status/difficulty sets, `good first issue` for onboarding), milestones for release planning, issue templates (bug report and feature request with front matter), security vulnerability template, Kanban project boards, contribution workflows (fork-and-PR model, CONTRIBUTING.md importance, good first issues), cross-repository references, federation and issues
- `other-platforms/codeberg/05-codeberg-ci.md` - Woodpecker CI overview (what it is, community Drone fork, comparison with GitHub Actions and Forgejo Actions), connecting to ci.codeberg.org (OAuth2), enabling repositories, `.woodpecker.yml` syntax (steps, image, commands, settings), plugins (Docker build/push, SSH deploy, Telegram), volumes and caching, triggers (push/pull_request/tag/cron/manual, per-step when conditions, branch filters), environment variables and secrets (from_secret, built-in CI variables), matrix builds, services (Docker sidecars for databases), practical pipeline examples (Node.js, Python with PostgreSQL, Go multi-platform Docker, release with binaries), self-hosted Woodpecker agents (Docker Compose setup, OAuth2 app registration)
- `other-platforms/codeberg/06-pages.md` - how Codeberg Pages works (pages branch, automatic serving), basic setup (orphan branch, static files), URL structure (username.codeberg.page for pages repo, /repo-name sub-path), deploy from Woodpecker CI, custom domains (.domains file, CNAME DNS setup, Let's Encrypt auto-HTTPS), static site generators (Hugo, MkDocs, Eleventy with full CI pipelines), SPA support (404.html trick for client-side routing), limitations (public repos only, exact `pages` branch name required, no server-side code, fair use storage)
- `other-platforms/codeberg/07-packages-and-releases.md` - package registry (23+ Forgejo formats, URL pattern), container images (login/push/pull, Kubernetes imagePullSecret), npm packages (.npmrc configuration, scoped packages), PyPI packages (twine upload, pip.conf), generic packages (curl PUT/GET), publishing from Woodpecker CI (Docker, Python, npm pipelines), releases (web UI creation, release description best practices, API creation), publishing release assets from CI (Go multi-platform pipeline, upload script), Codeberg-specific storage considerations and fair use etiquette
- `other-platforms/codeberg/08-organisations-and-teams.md` - creating organisations (permanent URL slug, federated identity), organisation settings, members and roles (Owner vs Member), teams (creating, permission levels: None/Read/Write/Admin, all repos vs specific repos), common team structures for open source, service account teams for CI, organisation repositories, transferring repositories to orgs, forks and contributor workflows, organisation-level features (shared labels, webhooks, packages, Woodpecker CI org secrets), Codeberg considerations (private repos require financial support, bot accounts allowed, organisation name and federated identity)
- `other-platforms/codeberg/09-all-codeberg-features-reference.md` - complete feature reference tables (account, repos, code review, issues, Woodpecker CI, Codeberg Pages, packages/releases, organisations, authentication, federation, API), Codeberg vs GitHub vs GitLab.com quick comparison, quick navigation

### Updated

- `CHANGELOG.md` - added v0.9.0 entry
- `ROADMAP.md` - marked other-platforms/codeberg/ as complete, updated current status to v0.9.0

---

## [0.8.0] - 2026-04-09

### Added

- `other-platforms/forgejo/00-forgejo-overview.md` - folder navigation, relationship with Gitea and Codeberg, OS coverage note (Linux/Mac only)
- `other-platforms/forgejo/01-what-is-forgejo.md` - the 2022 Gitea governance dispute (48-contributor open letter, domain transfer without notice), Codeberg e.V. as steward, soft fork (December 2022) to hard fork (February 2024) timeline, GPLv3+ licence change and what it means for self-hosters vs distributors, Forgejo in 2026 (v9.0.x), ActivityPub federation (ForgeFed protocol, ForgeFed working group), who uses Forgejo (SAML requirement, free software advocates, federation interest), key concepts (Forgejo runner, app.ini, .forgejo/workflows)
- `other-platforms/forgejo/02-installation.md` - binary install on Linux and macOS (download from codeberg.org/forgejo/forgejo/releases), Docker and Docker Compose (codeberg.org/forgejo/forgejo image, FORGEJO__ env prefix), system requirements, installation wizard, app.ini configuration, PostgreSQL database setup, Nginx and Caddy reverse proxy, systemd service, Forgejo runner setup (download from codeberg.org/forgejo/runner, register with token, daemon start), upgrading
- `other-platforms/forgejo/03-creating-an-account.md` - registration, 2FA, SSH keys, access tokens, SAML sign-in (Forgejo exclusive free feature: Okta/Azure AD/Keycloak/Google Workspace configuration, SP metadata URLs, ACS URL, IdP-specific setup, safe testing procedure), OAuth2 providers, account security checklist
- `other-platforms/forgejo/04-repositories-and-code-review.md` - creating repos, clone via HTTPS/SSH, mirroring and migration, pull requests, code review (inline comments, suggestions, review submission), merge strategies, protected branches, releases, Git LFS, visibility levels (public/private/internal), forking
- `other-platforms/forgejo/05-forgejo-actions.md` - enabling Actions (app.ini + repository level), .forgejo/workflows/ file location (also accepts .github/ and .gitea/), triggers, jobs and steps, runners and labels, the Forgejo runner vs act_runner distinction, secrets and variables (FORGEJO_TOKEN automatic secret), artefacts, caching, matrix builds, actions from external sources (DEFAULT_ACTIONS_URL options for air-gapped), practical examples (Node.js, Python, Docker, release binary pipeline)
- `other-platforms/forgejo/06-issues-packages-and-federation.md` - issues (identical to Gitea), labels and milestones, issue templates (.forgejo/ISSUE_TEMPLATE/), Kanban project boards, package registry (all 23+ formats, identical to Gitea with forgejo.example.com URLs), ActivityPub federation (what it enables: federated identity, following repos across instances, cross-instance notifications; current status 2026; enabling with [federation] ENABLED=true; blocking instances; ForgeFed long-term vision)
- `other-platforms/forgejo/07-administration.md` - admin panel, user management (create/suspend/delete/impersonate), global 2FA enforcement, LDAP configuration (Active Directory specifics, user filter), SAML 2.0 (generating certificates, app.ini configuration, SP URLs for Okta/Azure AD/Keycloak/Google Workspace, safe testing), OAuth2 providers, email configuration, federation administration, instance settings, maintenance CLI commands, backup and restore (database + work directory + SAML certs), Prometheus metrics
- `other-platforms/forgejo/08-api-and-integrations.md` - REST API (compatible with Gitea for core operations), authentication, core endpoints (repos/issues/PRs/users/orgs/actions secrets/releases), ActivityPub API endpoints (NodeInfo, WebFinger, user actor, repository actor, follow API), webhooks (X-Gitea-Signature header for backward compatibility), Renovate bot (platform: gitea works), Woodpecker CI (native Forgejo support, Docker Compose), Terraform provider, ArgoCD, rate limits, pagination
- `other-platforms/forgejo/09-all-forgejo-features-reference.md` - complete feature reference (repos, code review, issues, Actions, packages, users/orgs, authentication including SAML free, federation tables with status, administration, API), Forgejo vs Gitea quick reference, quick navigation

### Fixed

- `other-platforms/gitea/11-api-and-integrations.md` - MD038 lint errors: two instances of backtick code spans with trailing spaces (`` `token ` ``) on lines 852 and 854 corrected to `` `token` `` (no trailing space)

### Updated

- `CHANGELOG.md` - added v0.8.0 entry
- `ROADMAP.md` - marked other-platforms/forgejo/ as complete, updated current status

---

## [0.7.0] - 2026-04-09

### Added

- `other-platforms/gitea/00-gitea-overview.md` - folder navigation, terminology mapping, level-based paths, version notes (v1.x scheme)
- `other-platforms/gitea/01-what-is-gitea.md` - origins (Gogs fork November 2016, why the fork), growth years 2016-2022, the 2022 governance controversy (Gitea Ltd incorporation, domain transfer, 48-contributor open letter), CommitGo Inc. and open-core model (Gitea Enterprise: SAML/audit logs/IP allowlist/dependency scanning, Technical Oversight Committee structure), Gitea in 2026 (v1.25.5, HTMX+Tailwind frontend rewrite, new v1.25 features), what Gitea provides, who uses it, comparison with Forgejo/Gogs/GitLab CE/GitHub
- `other-platforms/gitea/02-installation.md` - binary install on Linux/Windows/macOS, Docker quick start, Docker Compose with PostgreSQL, system packages (Arch/Alpine/FreeBSD), installation wizard walkthrough, app.ini configuration (server/database/security/service/mailer/oauth2 sections), generating secrets, PostgreSQL and MySQL database setup, Nginx and Caddy reverse proxy, systemd service on Linux, Windows service, act_runner setup (download, register with labels, start as service), upgrading
- `other-platforms/gitea/03-creating-an-account.md` - registration and restrictions, profile settings, username change implications, 2FA (TOTP, recovery codes, instance-wide enforcement since v1.24), SSH key generation on all three OS (ed25519), adding public key, testing connection, custom SSH port, access tokens (creating, scopes table, HTTPS Git use, rotating), OAuth2 sign-in providers, email privacy settings
- `other-platforms/gitea/04-repositories.md` - creating repos (web and API), repository settings, clone via HTTPS and SSH, remote URL updates, branches, pull mirrors (mirroring from GitHub/GitLab/any Git URL, interval configuration), push mirrors, import vs mirror distinction, repository topics, releases (creating, API, asset attachments), Git LFS (setup, .gitattributes, Gitea LFS config), wikis (as Git repo), visibility levels (public/private/internal), forking, transfer and deletion
- `other-platforms/gitea/05-pull-requests-and-code-review.md` - creating PRs (banner/branches page/PR list/API), PR form fields, PR interface tabs (Overview/Commits/Files Changed), conflict handling, inline and multi-line comments, code suggestions (suggestion fenced code block), submitting reviews, merge strategies (merge commit/squash/rebase), protected branches configuration, PR templates (.gitea/PULL_REQUEST_TEMPLATE.md, multiple templates), auto-merge, cross-repository PRs from forks
- `other-platforms/gitea/06-gitea-actions.md` - enabling Actions (app.ini + repository), .gitea/workflows/ and .github/workflows/ paths, triggers (push/pull_request/schedule/workflow_dispatch/release), jobs and steps, conditions, step outputs, runners and labels (runs-on matching), act_runner setup, label format (docker://image, host executor), secrets and variables (repository and organisation level), artefacts and caching, using GitHub Marketplace actions (DEFAULT_ACTIONS_URL config, air-gapped self), matrix builds (include/exclude), managing runners (status, blocking, deleting), practical examples (Node.js CI, Python matrix, Docker build to Gitea registry, multi-platform Go release)
- `other-platforms/gitea/07-issues-and-projects.md` - issues (creating, form fields, commenting, reactions, locking, states), labels (creating, suggested sets, organisation-level), milestones, assignees and mentions, issue templates (.gitea/ISSUE_TEMPLATE/ with front matter, multiple templates, config.yml for blank issues), closing keywords (Closes/Fixes/Resolves with cross-repo syntax), pinned issues, emoji reactions, Kanban project boards (creating, columns, add issues, move cards, filtering), issue dependencies, cross-repository references
- `other-platforms/gitea/08-packages.md` - package registry (23 formats, enabling in app.ini, storage config, MinIO/S3), container/Docker (login/push/pull/Kubernetes), npm (.npmrc config, scoped packages, publishing), PyPI (twine, pip.conf, pyproject.toml), NuGet (.NET, nuget add source, push), Maven (pom.xml, settings.xml, Gradle), Cargo (config.toml, credentials.toml), Go modules (GOPROXY config), Debian packages (APT repository), Alpine packages, RPM packages, Helm charts (OCI and native), RubyGems, generic packages (multi-file upload/download), all 23+ supported formats table, using packages in Gitea Actions, package management and retention
- `other-platforms/gitea/09-administration.md` - admin panel sections, user management (create/edit/suspend/delete/impersonate), global 2FA enforcement (REQUIRE_SIGNIN_2FA), organisation management, repository management (fsck, quotas), LDAP/Active Directory configuration (BindDN mode, AD-specific filters), OAuth2 providers, email configuration (SMTP protocols, Gmail/SendGrid/SES examples, test email), instance settings via app.ini, queue and background tasks, maintenance CLI commands (admin user/git/doctor), backup and restore (gitea dump, database-only, automated cron), monitoring (log files, Prometheus metrics, health check)
- `other-platforms/gitea/10-gitea-vs-forgejo.md` - the split timeline (October 2022 incorporation → December 2022 Forgejo → November 2023 soft fork → February 2024 hard fork), governance comparison (CommitGo/TOC vs Codeberg e.V.), licence differences (MIT vs GPLv3+), feature divergence 2026 (SAML in Forgejo free / Gitea Enterprise; Windows in Gitea / dropped in Forgejo; federation in Forgejo / stalled in Gitea; enterprise tier in Gitea / none in Forgejo), version numbering (not comparable), API compatibility, migration between them (Forgejo migration tool, git push --mirror, caveats), which to choose (decision matrix)
- `other-platforms/gitea/11-api-and-integrations.md` - REST API base URL and Swagger, authentication (access token, basic auth, OAuth2, in-pipeline GITEA_TOKEN), API conventions and versioning, repositories (list/get/create/delete/branches/file contents/create-update-file/commits/tags), issues and PRs (list/create/close/comment/create-PR/list-PRs/merge), users and organisations, Gitea Actions via API (workflow runs, secrets, releases), webhooks (web UI and API creation, event types table, push payload structure, signature verification with Python/Flask), third-party integrations (Drone CI, Jenkins, ArgoCD, Renovate, VS Code extension, Terraform provider), rate limits and pagination (page/limit, X-Total-Count, bash and PowerShell iteration scripts), Swagger UI usage
- `other-platforms/gitea/12-all-gitea-features-reference.md` - complete feature reference tables (repos, code review, issues, Actions, packages, users/orgs, authentication, administration, API), Forgejo vs Gitea quick reference, quick navigation

### Updated

- `CHANGELOG.md` - added v0.7.0 entry
- `ROADMAP.md` - marked other-platforms/gitea/ as complete

---

## [0.6.0] - 2026-04-09

### Added

- `other-platforms/azure-devops/00-azure-devops-overview.md` - folder navigation, five-service structure, terminology mapping from GitHub/GitLab, level-based paths, plan quick reference, note on Services vs Server
- `other-platforms/azure-devops/01-what-is-azure-devops.md` - history (Visual SourceSafe 1994 → TFS 2005-2018 → VSTS 2012-2018 → Azure DevOps 2018), the five services (Boards/Repos/Pipelines/Artifacts/Test Plans), Services vs Server, market position (enterprise Microsoft environments, regulated industries, .NET shops, TFS migration path), Microsoft strategic positioning (GitHub = AI-first future, ADO = enterprise orchestration, hybrid model, February 2025 migration playbook), Azure DevOps in 2026 (New Boards Hub GA Sprint 259, Managed DevOps Pools, GitHub org connections to 2000, MCP Server preview March 2026), hosted agent images (ubuntu-latest=24.04, windows-latest=Server 2025, macos-latest=15), key concepts (organisation/project/repository/agent/PAT)
- `other-platforms/azure-devops/02-creating-an-account.md` - Microsoft account vs Azure AD (personal vs enterprise identity, when to choose each), creating organisation (name permanence, region selection), understanding organisations and projects hierarchy, creating first project (process templates: Agile/Scrum/CMMI/Basic, visibility), PATs (creating with scope table, HTTPS Git usage, credential storage on all three OS, rotating), SSH keys (generating ed25519, adding, testing, cloning, custom port), free tier limits (5 Basic users, 1,800 minutes/month, 1 self-hosted job, 2 GiB Artifacts), adding members (access levels table: Stakeholder/Basic/Basic+TP/VS subscriber), connecting to Azure Active Directory, account security (MFA, reviewing tokens and SSH keys)
- `other-platforms/azure-devops/03-azure-repos.md` - Git vs TFVC choice, navigating Azure Repos (sidebar, repository selector, default branch), creating repositories (web and API), clone via HTTPS and SSH, setting up remote on existing repo, branches (viewing/creating/naming conventions/deleting), pull requests (creating via all four methods, form fields, statuses), code review (Files tab, inline comments, multi-line, code suggestions, replying, resolving), reviews and approvals (submitting review, vote states), branch policies (minimum reviewers with all sub-options, linked work items, comment resolution, build validation, merge strategy, status checks), repository-level settings (policies/security/fork policies), Git LFS, importing and migrating (from any Git host, GitHub mirror, TFVC to Git migration), TFVC overview (concepts/basic commands)
- `other-platforms/azure-devops/04-azure-boards.md` - process templates (Agile/Scrum/CMMI/Basic hierarchy diagrams, changing template), work items in depth (creating, full form field coverage, states, types and hierarchy, bulk editing), Boards view (Kanban, columns, WIP limits, swimlanes, card information), Backlogs view (levels, priority ordering, filters, settings), Sprints (setting up iterations, sprint board, sprint planning, capacity planning, burndown charts, velocity), Queries (WIQL operators table, WIQL syntax, saving and sharing queries), Delivery plans, linking work to code (AB# syntax, PR work item links, branch creation from work item, automatic transitions), reporting and dashboards (widgets list, Analytics views, Power BI connector), teams within a project (area paths, creating teams, area path structure)
- `other-platforms/azure-devops/05-azure-pipelines.md` - complete azure-pipelines.yml guide: how pipelines work, triggers (push/PR/schedule/manual with full filter syntax), agents and pools (Microsoft-hosted images table with current OS including retired images warning, self-hosted installation on all three OS), stages/jobs/steps hierarchy, tasks (built-in tasks with YAML: checkout/.NET/Node.js/Docker/Azure deployment/publish test results/publish code coverage), variables (inline/predefined variables table/variable groups with Key Vault integration/secrets/dynamic setting/output variables), conditions (built-in functions and expressions), artifacts (publish/download/caching with key hashing), environments and deployment jobs (creating, approval gates, deployment strategies: runOnce/rolling/canary), templates (step/job/stage templates with parameters, extends for security), service connections (types table, creating, workload identity federation), pipeline security (protected resources, YAML pipeline validation), Managed DevOps Pools (scaling to zero, Gen 2 VMs, Spot VMs mid-2026), classic pipelines (legacy, migration guidance), practical examples (Node.js with caching, .NET with Docker, multi-environment with approvals)
- `other-platforms/azure-devops/06-azure-artifacts.md` - how Artifacts works (caching model, why use a private feed), feeds (creating, scope: project vs organisation, multiple feeds), upstream sources (supported registries table, caching behaviour, saved packages), NuGet (Visual Studio, nuget.config, credential provider installation on all three OS, publishing with dotnet nuget push), npm (.npmrc config, authenticating on all three OS, scoped packages), Python/pip (twine upload, pip.conf on all three OS, pyproject.toml with Poetry), Maven (pom.xml, settings.xml, Gradle), Cargo (preview, config.toml, credentials.toml), Universal Packages (publishing and downloading via az CLI, versioning), using Artifacts in pipelines (NuGetAuthenticate/npmAuthenticate tasks, publishing NuGet/npm/Universal from pipeline), feed permissions (roles table, pipeline build service identity), storage and billing (2 GiB free, retention policies)
- `other-platforms/azure-devops/07-azure-test-plans.md` - what Test Plans provides (three scenarios: planned manual/exploratory/automated), who needs it (vs automated-only teams), test plans and suites (plan structure, suite types: static/requirement-based/query-based), test cases (creating, steps grid with expected results, shared steps, parameterised tests), executing manual tests (starting a run, test runner interface, pass/fail/blocked marking, specifying build), exploratory testing (Test and Feedback Chrome/Edge extension, starting a session, capturing screenshots/video/notes/bugs, ending a session), running automated tests from Test Plans, defect tracking (filing bugs with pre-populated context, linking existing bugs), test reporting, traceability (linking test cases to requirements, viewing coverage), configuration testing (cross-browser/OS)
- `other-platforms/azure-devops/08-branch-policies.md` - how branch policies work (server-side enforcement, additive model), accessing settings (per-branch and wildcard patterns), require minimum reviewers (all sub-options: allow self-approval/prohibit recent pusher/reset on push/require all members), check for linked work items, check for comment resolution, limit merge types (strategies explained, when to use each), build validation (trigger/requirement/expiration options, what it does, multiple validations), status checks (external service integration, configuring as policy), automatically include reviewers (required vs optional, file path filters), branch locks (use cases), cross-repository policies, bypassing policies (permissions, what is logged), policy combinations for common scenarios (simple team/mid-size/enterprise/release branch)
- `other-platforms/azure-devops/09-security-and-permissions.md` - permission model (hierarchy, allow/deny evaluation, deny is absolute), organisation-level security (Project Collection Administrators, groups table), project-level security (four default groups with details), team-level access (teams are not a security boundary), repository-level permissions (accessing, full permissions table, restricting to subset, using explicit allows vs denies), security groups (creating custom groups, common patterns, group nesting), permission inheritance and override, Azure AD integration (benefits, connecting, using AD groups), conditional access and MFA (MFA for ADO, Conditional Access policies, impact on PATs), service accounts and service principals (pipeline service identity, service principals, managed identities), PATs at scale (org-wide policies, auditing), audit log (accessing, what is logged, filtering, exporting, streaming to SIEM)
- `other-platforms/azure-devops/10-azure-devops-server.md` - name history table (VSS 1994 → TFS → VSTS → ADO Server 2019-2022 → ADO Server current December 2025), when to choose Server (data sovereignty/air-gapped/on-prem AD/TFS investment/upgrade control/cost at scale), system requirements (application tier, SQL Server with Full-Text Search requirement, Elasticsearch for code search, scale configurations table), installation (Linux and Windows, prerequisites checklist, downloading and running installer), configuration wizard (database/account/URL/SSL/search), first login and project collections, authentication (Windows/AD default, Azure AD, SAML), upgrading from TFS (supported paths, pre-upgrade checklist, procedure, post-upgrade tasks), differences from Services table, licensing (user licences table, server licence), maintaining an instance (backup strategy, updates, monitoring, common tasks)
- `other-platforms/azure-devops/11-extensions-and-marketplace.md` - how extensions work (what they contain, permissions), finding extensions, installing (from Marketplace and inside ADO), managing (viewing/updating/disabling/uninstalling), notable extensions (SonarQube/SonarCloud with YAML, Checkmarx, WhiteSource/Mend, Pester, Terraform tasks with YAML, AWS Toolkit, Slack/Teams notifications, dashboard widgets), extension security considerations (risks, approved lists), building custom extensions (types, TypeScript SDK, task.json, index.ts, packaging and publishing)
- `other-platforms/azure-devops/12-azure-devops-cli.md` - installation (Azure CLI then az extension add, all three OS), authentication (az login, service principal, PAT via AZURE_DEVOPS_EXT_PAT, pipeline usage with System.AccessToken), setting defaults (az devops configure), repos commands (list/show/create/delete/branch operations/PR create/show/complete/approve/reviewer/branch policies), Boards commands (work-item create/show/update/query with WIQL/link/delete), Pipelines commands (list/show/run with variables/runs list/cancel/logs/variables create/update/variable-groups), Artifacts commands (feed list/create/Universal publish/download/list versions), service connection commands, project and organisation commands (create/delete/user/team management), scripting (JSON output with jq and PowerShell ConvertFrom-Json, pipeline script example, batch operations on all three OS, error handling)
- `other-platforms/azure-devops/13-api-and-integrations.md` - REST API base URLs and current version (7.1), authentication (PAT basic auth, OAuth2 bearer, Entra ID bearer, OAuth deprecation April 2025), API conventions (HTTP methods/response format/status codes), repositories and commits (list/get/create/delete/branches/file contents/create-update/commits/tags), pull requests (list/create/vote/complete), work items (get/bulk get/create with JSON Patch/update/run WIQL), pipelines (list/trigger with variables/get run/list runs), Artifacts (list feeds/packages/versions), commit status API (posting/getting/using with branch policies), service hooks (creating via UI and API, event types table, push payload, handling webhooks with Python/Flask), GitHub integration (connecting Pipelines to GitHub, GitHub service connection, AB# work item linking, managing GitHub repos from ADO), service connections (types table, creating ARM via API, workload identity federation), rate limits and pagination (X-Retry-After, cursor-based pagination)
- `other-platforms/azure-devops/14-all-azure-devops-features-reference.md` - plan quick reference table, complete feature tables (Boards/Repos/Pipelines/Artifacts/Test Plans/organisation and administration/security/integrations and API/Server exclusive features), deprecated and retired features table (Windows 2019/Ubuntu 20.04/macOS 13 retired, Classic pipelines maintenance mode, ADO OAuth deprecated), feature comparison with GitHub and GitLab, quick navigation

### Updated

- `CHANGELOG.md` - added v0.6.0 entry
- `ROADMAP.md` - marked other-platforms/azure-devops/ as complete

---

## [0.5.0] - 2026-04-09

### Added

- `other-platforms/bitbucket/00-bitbucket-overview.md` - folder navigation, terminology mapping, level-based paths, plan quick reference, Cloud vs Data Center note
- `other-platforms/bitbucket/01-what-is-bitbucket.md` - founding 2008 (Jesper Nøhr, Mercurial hosting), Atlassian acquisition 2010, Cloud vs Data Center split, Mercurial deprecation and removal 2020, Bitbucket Server EOL 2024, Atlassian ecosystem integration (Jira/Confluence/Trello), market position (enterprise Atlassian shops), the Atlassian account model, Bitbucket in 2026 (current plan pricing, hosted agent images)
- `other-platforms/bitbucket/02-creating-an-account.md` - Atlassian account (one account for Jira/Confluence/Trello/Bitbucket), creating account at id.atlassian.com, Bitbucket profile setup, workspace creation (slug permanence, visibility), 2FA (TOTP, recovery codes), SSH keys (generation on all three OS, adding to Bitbucket, testing), app passwords (purpose, creating with scopes table, using for HTTPS Git and API, rotating), free tier limits (5 users, 50 Pipelines minutes/month, 1 GiB LFS)
- `other-platforms/bitbucket/03-bitbucket-vs-github-vs-gitlab.md` - three-way comparison (governance/pricing/CI/security/ecosystem), Bitbucket strengths (Atlassian integration, Jira smart commits, enterprise Data Center), Bitbucket weaknesses (small community, weaker CI, no Pages), when to choose each, migration paths
- `other-platforms/bitbucket/04-repositories.md` - creating repos (workspace and Projects container), cloning HTTPS and SSH, pushing existing repos, branching, branch naming, Projects (organising repos, not GitHub Projects equivalent), repository settings (name/description/language/privacy/forking), forking, LFS setup and limits, default reviewers, webhooks per repository
- `other-platforms/bitbucket/05-pull-requests.md` - creating PRs (from branch/compare page/API), PR form (reviewers, diff algorithm, source/dest), reviewing (inline comments, tasks vs comments distinction, suggestions), approval (approve/request changes/needs work), merge strategies (merge commit/squash/fast forward), default merge strategy per repo, merge checks enforced by branch permissions
- `other-platforms/bitbucket/06-bitbucket-pipelines.md` - full bitbucket-pipelines.yml guide: how Pipelines works (YAML in repo root, steps run in Docker), pipeline types (default/branches/tags/pull-requests/custom/scheduled), steps (script/name/image/size/runs-on), stages (manual/automatic), parallel steps, after-scripts, services (Postgres/Redis/Docker-in-Docker), caches (built-in: pip/npm/maven/gradle, custom caches), artifacts (between steps and pipelines), pipes (pre-built integration blocks: AWS/GCP/Azure deployment, Slack, SonarQube), variables and secrets (workspace/repository/deployment levels, secured variables), deployment environments, Pipelines runners (self-hosted: Linux/Windows/macOS, installing the runner agent), pipeline limits (50 minutes/month free, step timeouts, build minutes counter)
- `other-platforms/bitbucket/07-branch-permissions.md` - branch restrictions (who can push/merge), merge checks (minimum approvals, passing builds, required tasks, no unresolved comments, minimum default reviewers), default reviewers (auto-added, required vs suggested), branch models (Gitflow/feature branch, Bitbucket's built-in branch model), branch deletion policy after merge
- `other-platforms/bitbucket/08-jira-integration.md` - smart commits (transitions, comments, time logging syntax), development panel in Jira (linked commits/branches/PRs/builds/deployments), automating issue transitions via Pipelines (Deployments API), creating Bitbucket workspace connected to Jira site, issue key detection in branch names and commit messages
- `other-platforms/bitbucket/09-security-features.md` - IP allowlisting (workspace-level, CIDR blocks, exclusions), 2FA enforcement policy (require for all workspace members), secret scanning (Pipelines secrets detection, push protection), merge checks as quality gates, branch permissions as security layer, app passwords vs OAuth (why app passwords are safer), security advisories and CVE tracking
- `other-platforms/bitbucket/10-bitbucket-cloud-vs-data-center.md` - when to self-host (compliance/air-gapped/cost at scale/customisation), Data Center licensing model (user tiers, annual renewal), system requirements (minimum/recommended), installation overview (Linux, PostgreSQL, Elasticsearch), differences table (Cloud vs DC: CI minutes/storage/SAML/LDAP/clustering/upgrade timing), migration from Cloud to DC
- `other-platforms/bitbucket/11-workspaces-and-permissions.md` - workspaces (the top-level container, one per team/company), workspace members vs repository access, workspace roles (Admin/Member), repository permissions (Admin/Write/Read), repository access groups (groups within workspace), permission inheritance, workspace settings (visibility/2FA enforcement/IP allowlist/SSO)
- `other-platforms/bitbucket/12-api-and-integrations.md` - Bitbucket REST API v2 (base URL, authentication with app password, response format), key endpoints (repos/commits/PRs/issues/branch-restrictions), webhooks (workspace and repo level, event types, payload structure, HMAC verification), Atlassian Marketplace (apps vs Pipelines pipes distinction), popular integrations (Jira/Slack/SonarQube/Snyk), rate limits and pagination
- `other-platforms/bitbucket/13-all-bitbucket-features-reference.md` - complete feature reference tables (account/repos/pull requests/Pipelines CI-CD/branch control/Jira integration/security/workspaces and permissions/API), Bitbucket vs GitHub vs GitLab quick comparison, quick navigation

### Updated

- `CHANGELOG.md` - added v0.5.0 entry
- `ROADMAP.md` - marked other-platforms/bitbucket/ as complete, updated current status

---

## [0.4.0] - 2026-04-09

### Added

- `gitlab/00-gitlab-overview.md` - folder navigation with terminology mapping, level-based learning paths and tier notes
- `gitlab/01-what-is-gitlab.md` - founding story (Zaporozhets 2011, Sijbrandij 2012), Y Combinator W2015, the Microsoft/GitHub moment (June 2018, 100K repos migrated), IPO October 2021 NASDAQ GTLB, Bill Staples as CEO (December 2024), Siva Padisetty as CTO (January 2026), 40M+ users, 50%+ Fortune 100, GitLab 18.10, single-application philosophy, CE vs EE, plans, community programmes
- `gitlab/02-creating-an-account.md` - signup and username rules, identity verification (risk-based, credit card for shared runners), profile setup, TOTP setup, WebAuthn hardware keys, passkeys (GA 18.9, passwordless sign-in), SSH key generation on all three OS, PAT creation with full scope table, free tier limits (5 users/private group, 400 compute minutes, 10 GiB/project, 3 top-level groups post January 2026), credit card requirement explanation
- `gitlab/03-gitlab-vs-github.md` - philosophical comparison (single application vs marketplace), full feature comparison tables, CI/CD comparison (400 vs 2,000 free minutes), security features comparison, AI comparison (Duo vs Copilot), pricing tables, pricing controversy (merge approvals/CODEOWNERS paywalled, $19 to $29 Premium increase), self-hosting comparison (CE free vs GHE $21/user), community and ecosystem, when to choose each platform, using both together
- `gitlab/04-projects-and-namespaces.md` - project vs repository distinction, namespace types (personal/group), creating groups, subgroups (up to 20 levels deep), role inheritance, creating projects (blank/template/import), project settings by section, visibility levels (Public/Internal/Private), forking and fork visibility, syncing forks, project templates (built-in and group-level custom), Web IDE, importing from GitHub, archiving/transferring/deleting, comparison with GitHub orgs and teams
- `gitlab/05-merge-requests.md` - creating MRs (terminal link, UI, from issue, glab CLI), MR interface tabs (Overview/Commits/Pipelines/Changes), draft MRs (Draft: prefix), reviewing (inline comments, suggested changes, batch suggestions), resolve threads, approvals (Free vs Premium), approval rules (prevent author approval, reset on new commits), CODEOWNERS (Premium, all syntax including sections/optional sections), merge methods (merge commit/semi-linear/fast-forward/squash), auto-merge, merge trains (Premium), merged results pipelines, MR templates, closing issues automatically, MR dependencies (Premium), reverting, glab CLI commands
- `gitlab/06-gitlab-cicd.md` - how CI/CD works, runners (shared/group/project, all three OS install and register), executors, first pipeline, stages (.pre/.post), jobs (all keywords in detail), script, image/services, before_script/after_script, rules (if/changes/exists/when), workflow rules (duplicate pipeline prevention with auto_cancel), predefined variables (full tables: commit/pipeline/project/MR/registry/user), custom variables (4 levels and precedence), artifacts (paths/expire/when/reports), caching (key/file-based/policy/multiple), needs/DAG, parallel matrix (max 200), extends/hidden jobs, include (local/project/remote/template/component, max 150), environments, dynamic environments, review apps, MR pipelines, security scanning templates (SAST/secret detection/dependency/container), scheduled pipelines, child and parent pipelines, pipeline editor with linting, common mistakes
- `gitlab/07-gitlab-pages.md` - how Pages works (public/ directory, pages: true since 17.5), old vs new job naming, built-in templates, framework pipelines (Jekyll/Hugo/MkDocs/Gatsby/Sphinx/Eleventy), testing build on MRs, URL structure, unique domains, custom domains (CNAME/A record and TXT verification), HTTPS (Let's Encrypt auto), Force HTTPS, access control (restrict to members), limits (100 MiB, 200,000 files), Pages on self-managed, Pages vs GitHub Pages comparison
- `gitlab/08-issues-and-planning.md` - issues (work items framework), creating issues (all methods), all issue fields (assignees, labels, milestones, due date, weight, health status, iteration, epic), confidential issues, sub-issues, task list checkboxes, linked issues (relates to/blocks/blocked by), time tracking, quick actions (complete reference table covering state/assignment/labels/planning/relationships/time/issue management/MR-specific), labels (project vs group), scoped labels (Premium, key::value), milestones (burndown/burnup charts), issue boards (Kanban, list types, board scope, multiple boards), epics (Premium, hierarchy, child epics), roadmaps (Premium, timeline presets, filter options), iterations (Premium, cadences, burndown), issue and MR templates, Service Desk (email-to-issue, template, converting issues), GitLab Wiki (project/group, sidebar/footer, cloning locally), GitLab Snippets (versioned, multi-file up to 10, embed, clone as repository)
- `gitlab/09-security-features.md` - SAST (Semgrep-based, supported languages, Advanced SAST cross-file/function in Ultimate, false positive detection via Duo 18.10), Secret Detection (90+ patterns, push protection, auto-revocation, what to do when found), Dependency Scanning (supported ecosystems, Trivy, CycloneDX SBOM), Container Scanning (Trivy, what it finds, reducing vulnerabilities), IaC Scanning (Terraform/CloudFormation/K8s/Ansible/Dockerfile), DAST v5 browser-based (Ultimate, replaces proxy-based in 17.3), API Security Testing, Fuzz Testing (coverage-guided and web API, Ultimate, fails pipeline on crash), License Compliance (approved/denied policies), Vulnerability Management (Detected/Confirmed/Dismissed/Resolved statuses, creating issues from vulnerabilities, group security dashboard), Security Policies (scan execution, MR approval, pipeline execution), GitLab Advisory Database (GLAD, CVE Numbering Authority), comparison with GitHub security
- `gitlab/10-gitlab-duo.md` - GitLab Duo overview and philosophy, Duo tiers (Core/Pro/Enterprise) with pricing, credits system (12/user Premium, 24/user Ultimate, $1 on-demand), Code Suggestions (inline completions, 25+ languages, accepting/rejecting), Duo Chat (non-agentic vs agentic modes, slash commands, context), Duo Agent Platform (GA January 15 2026, agents vs chat difference, governance), Planner Agent, Security Analyst Agent, Root Cause Analysis, Vulnerability Resolution, AI Code Review, Flows (Developer Flow/Convert to CI/Fix CI/Code Review), AI Catalog, MCP Client (Jira/Slack/Confluence/Grafana), AI models (Anthropic Claude/Google Vertex AI/Fireworks Codestral), IDE extensions (VS Code/JetBrains/Visual Studio/Neovim/Web IDE), privacy (zero-day retention, no training on private code, context exclusion, AI Transparency Center), self-hosted models (vLLM/AWS Bedrock/Azure OpenAI), Duo vs Copilot comparison
- `gitlab/11-self-hosting.md` - CE vs EE (why always install EE), why self-host (data sovereignty, air-gapped, cost at scale, control), system requirements (minimum 4vCPU/8GB, 1K reference: 8vCPU/16GB), reference architectures (1K-50K, HA, Cloud Native Hybrid), Linux package installation (Ubuntu/Debian, RHEL), Docker installation (Docker Compose, pinning versions), Kubernetes Helm chart, initial configuration (gitlab.rb), email/SMTP, HTTPS with Let's Encrypt, LDAP, SAML, backup (what is included/excluded, automated cron, object storage), restore, upgrades (upgrade paths, required stops, background migrations, zero-downtime), GitLab Runner, Pages, Container Registry, monitoring (Prometheus, Grafana, health checks), self-managed vs GitLab.com differences table
- `gitlab/12-gitlab-cli.md` - glab overview, installation on all three OS, authentication, repo/MR/issue/CI/variable/release/label/snippet/runner/schedule commands, glab api (GET/POST/PUT/DELETE), glab duo ask, self-managed usage, multiple instances, shell completions, glab vs gh comparison
- `gitlab/13-groups-and-permissions.md` - groups overview, creating and managing, all roles (Guest/Reporter/Developer/Maintainer/Owner/Planner/Minimal Access/Security Manager), role inheritance, group access tokens, project access tokens, deploy tokens, deploy keys, SAML SSO, SCIM provisioning, group CI/CD variables, group runners, visibility levels, audit events, structuring a real organisation
- `gitlab/14-gitlab-api-and-integrations.md` - REST API, authentication, pagination, key endpoints, GraphQL, webhooks, integrations (Jira/Slack/Teams/Jenkins/Datadog), Container Registry, Package Registry, API in CI/CD, rate limits, python-gitlab library
- `gitlab/15-all-gitlab-features-reference.md` - complete feature reference with tier requirements and navigation paths

### Updated

- `CHANGELOG.md` - added v0.4.0 entry for gitlab/ folder
- `ROADMAP.md` - marked gitlab/ as complete, updated current status to v0.4.0

---

## [0.3.0] - 2026-04-09

### Added

- `github/18-badges-and-achievements.md` - every GitHub badge, how to earn each, tier thresholds (bronze/silver/gold), the Arctic Code Vault Contributor badge, the Pro badge
- `github/19-copilot.md` - free tier (2,000 completions/50 chat), Pro, Business and Enterprise, agent mode, Copilot in the CLI, IDE support, privacy settings
- `github/20-education.md` - Student Developer Pack (application, renewal, what is included), GitHub Classroom, Campus Expert programme
- `github/21-cli.md` - gh installation on all three OS, authentication, all major command groups (repo/pr/issue/run/gist/secret), aliases, gh copilot suggest/explain
- `github/22-mobile.md` - iOS and Android apps, review and comment on PRs, manage notifications, push protection on mobile, limitations vs desktop
- `github/23-collaborators-teams-orgs.md` - adding collaborators, organisations, teams (nested teams, team permissions, CODEOWNERS), outside collaborators, repository roles
- `github/24-advanced.md` - REST API, GraphQL API, webhooks, GitHub Apps vs OAuth Apps, GitHub Sponsors, Gists, GitHub Search (advanced syntax), GitHub Wikis
- `github/25-what-should-not-be-on-github.md` - secrets, PII, large binary files, generated files, credentials, what to do if you accidentally pushed sensitive data, git-filter-repo
- `github/26-platform-comparison.md` - GitHub Web vs Desktop vs Mobile vs CLI vs Codespaces comparison table
- `github/27-all-github-features-reference.md` - complete reference for every GitHub feature with settings paths and course links

### Updated

- `github/00-github-overview.md` - updated table to include files 18-27, added new learning path sections

---

## [0.2.0] - 2026-03-22

### Added

- `CODE_OF_CONDUCT.md` - Contributor Covenant 3.0
- `SECURITY.md` - security reporting policy
- `SUPPORT.md` - how to get help
- `.editorconfig` - consistent editor settings across all IDEs
- `.markdownlint.json` - markdown linting rules
- `.github/workflows/markdownlint.yml` - automated markdown linting on every PR
- `.github/workflows/linkcheck.yml` - weekly automated link checking
- `.github/ISSUE_TEMPLATE/bug_report.md` - structured bug reports
- `.github/ISSUE_TEMPLATE/content_suggestion.md` - structured content suggestions
- `.github/ISSUE_TEMPLATE/config.yml` - issue template config with Discussions link
- `.github/PULL_REQUEST_TEMPLATE.md` - PR checklist
- Contributors section in README using contrib.rocks
- Open in GitHub Codespaces badge in README

### Updated

- All files updated to new formatting standard - GitHub alert syntax replaces emoji callouts, minimal emoji throughout, proper bold and italic usage
- `introduction/03-setting-up.md` - GitHub and GitLab account creation added as Steps 2 and 3, full step reorder, new common mistakes
- `CONTRIBUTING.md` - new emoji policy, callout syntax guide, branding rules
- `README.md` - Contributor Covenant badge, Codespaces button, contrib.rocks grid, cleaner structure

---

## [0.1.0] - 2026-03-22

### Added

- Initial repository at [github.com/zaccessss/git-unlocked](https://github.com/zaccessss/git-unlocked)
- `README.md`, `LICENSE`, `CONTRIBUTING.md`, `CHANGELOG.md`, `ROADMAP.md`, `FAQ.md`, `HALL_OF_FAME.md`
- All folder structure with `.gitkeep` files
- `introduction/01-welcome.md`
- `introduction/02-how-to-use-this-course.md`
- `introduction/03-setting-up.md`

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
