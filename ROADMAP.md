# Roadmap

This document outlines the current state of git-unlocked, what is being built and what is planned. It is updated as the project grows.

---

## Table of Contents

- [Current status](#current-status)
- [Phase 1 - Foundation](#phase-1---foundation)
- [Phase 2 - Core Git content](#phase-2---core-git-content)
- [Phase 3 - Platform content](#phase-3---platform-content)
- [Phase 4 - Tools and environment](#phase-4---tools-and-environment)
- [Phase 5 - Real world and reference](#phase-5---real-world-and-reference)
- [Phase 6 - Community and polish](#phase-6---community-and-polish)
- [Future ideas](#future-ideas)
- [How to suggest something](#how-to-suggest-something)

---

## Current Status

**Version:** 0.4.0
**Stage:** Phase 3 in progress - git/ complete, github/ complete, gitlab/ complete, other-platforms/ next
**Started:** March 2026

| Section                          | Status      |
| -------------------------------- | ----------- |
| Repository setup                 | Complete    |
| README                           | Complete    |
| CONTRIBUTING                     | Complete    |
| CODE_OF_CONDUCT                  | Complete    |
| SECURITY                         | Complete    |
| SUPPORT                          | Complete    |
| CHANGELOG                        | Complete    |
| ROADMAP                          | Complete    |
| FAQ                              | Complete    |
| HALL_OF_FAME                     | Complete    |
| .github/ templates and workflows | Complete    |
| .editorconfig                    | Complete    |
| .markdownlint.json               | Complete    |
| introduction/                    | Complete    |
| git/                             | Complete    |
| github/                          | Complete    |
| gitlab/                          | Complete    |
| other-platforms/                 | In progress |
| ides/                            | Planned     |
| terminal/                        | Planned     |
| real-world/                      | Planned     |
| reference/                       | Planned     |
| resources/                       | Planned     |
| first-contribution/              | Planned     |
| assets/                          | Planned     |

---

## Phase 1 - Foundation

Getting the structure, branding and core files in place.

- [x] Create repository
- [x] README
- [x] MIT licence
- [x] CONTRIBUTING.md
- [x] CODE_OF_CONDUCT.md (Contributor Covenant 3.0)
- [x] SECURITY.md
- [x] SUPPORT.md
- [x] CHANGELOG.md
- [x] ROADMAP.md
- [x] FAQ.md
- [x] HALL_OF_FAME.md
- [x] Folder structure
- [x] .github/ issue templates and PR template
- [x] .github/workflows/ - markdownlint and link checker CI
- [x] .editorconfig
- [x] .markdownlint.json
- [x] introduction/01-welcome.md
- [x] introduction/02-how-to-use-this-course.md
- [x] introduction/03-setting-up.md
- [ ] Banner and assets
- [ ] first-contribution/ sandbox

---

## Phase 2 - Core Git content

The `git/` folder covering Git from zero to advanced.

- [x] 00-git-overview.md
- [x] 01-what-is-version-control.md
- [x] 02-installing-git.md
- [x] 03-how-git-works.md
- [x] 04-core-concepts.md
- [x] 05-basic-commands.md
- [x] 06-branching.md
- [x] 07-merging.md
- [x] 08-merge-conflicts.md
- [x] 09-remote-repos.md
- [x] 10-gitignore.md
- [x] 11-stash.md
- [x] 12-git-workflows.md
- [x] 13-rebase.md
- [x] 14-cherry-pick.md
- [x] 15-reset-and-revert.md
- [x] 16-tags-and-releases.md
- [x] 17-submodules.md
- [x] 18-hooks.md
- [x] 19-bisect.md
- [x] 20-signing-commits.md
- [x] 21-advanced-config.md
- [x] 22-large-repos.md
- [x] 23-monorepos.md
- [x] 24-gitops.md
- [x] 25-git-internals-advanced.md
- [x] 26-gitattributes.md
- [x] 27-git-worktree.md
- [x] 28-all-git-commands.md

**Status: Complete (29 files)**

---

## Phase 3 - Platform content

The `github/`, `gitlab/` and `other-platforms/` folders covering every major Git hosting platform.

### github/ folder

- [x] 00-github-overview.md
- [x] 01-what-is-github.md
- [x] 02-creating-an-account.md
- [x] 03-github-desktop.md
- [x] 04-repositories.md
- [x] 05-forking.md
- [x] 06-editing-on-github.md
- [x] 07-issues.md
- [x] 08-pull-requests.md
- [x] 09-branch-protection-and-rulesets.md
- [x] 10-github-actions.md
- [x] 11-github-pages.md
- [x] 12-discussions.md
- [x] 13-projects-and-boards.md
- [x] 14-github-releases.md
- [x] 15-github-packages.md
- [x] 16-security-features.md
- [x] 17-profile-and-readme.md
- [x] 18-badges-and-achievements.md
- [x] 19-copilot.md
- [x] 20-education.md
- [x] 21-cli.md
- [x] 22-mobile.md
- [x] 23-collaborators-teams-orgs.md
- [x] 24-advanced.md
- [x] 25-what-should-not-be-on-github.md
- [x] 26-platform-comparison.md
- [x] 27-all-github-features-reference.md

**Status: Complete (28 files)**

### gitlab/ folder

- [x] 00-gitlab-overview.md
- [x] 01-what-is-gitlab.md
- [x] 02-creating-an-account.md
- [x] 03-gitlab-vs-github.md
- [x] 04-projects-and-namespaces.md
- [x] 05-merge-requests.md
- [x] 06-gitlab-cicd.md
- [x] 07-gitlab-pages.md
- [x] 08-issues-and-planning.md
- [x] 09-security-features.md
- [x] 10-gitlab-duo.md
- [x] 11-self-hosting.md
- [x] 12-gitlab-cli.md
- [x] 13-groups-and-permissions.md
- [x] 14-gitlab-api-and-integrations.md
- [x] 15-all-gitlab-features-reference.md

**Status: Complete (16 files)**

### other-platforms/ folder

Each platform has its own subfolder at full depth - same standard as github/ and gitlab/.

#### other-platforms/bitbucket/

Planned files:
- 00-bitbucket-overview.md
- 01-what-is-bitbucket.md - founding (2008), Atlassian acquisition (2010), Cloud vs Data Center, Mercurial dropped (2020)
- 02-creating-an-account.md - Atlassian account, 2FA, SSH keys, app passwords, free tier (5 users, 50 pipeline minutes)
- 03-bitbucket-vs-github-vs-gitlab.md - three-way comparison, pricing, CI/CD, self-hosting, Atlassian ecosystem
- 04-repositories.md - creating repos, Projects (container above repos), visibility, forking, LFS
- 05-pull-requests.md - creating PRs, reviewers, inline comments, merge strategies, default reviewers
- 06-bitbucket-pipelines.md - full bitbucket-pipelines.yml guide, steps, stages, parallel, services, caches, artifacts, pipes, runners
- 07-branch-permissions.md - branch restrictions, merge checks, required builds, default reviewers
- 08-jira-integration.md - smart commits, development panel, automating transitions, linking issues
- 09-security-features.md - IP allowlisting, 2FA enforcement, secret scanning, merge checks
- 10-bitbucket-cloud-vs-data-center.md - when to self-host, Data Center licensing, installation, migration
- 11-workspaces-and-permissions.md - Workspaces, projects, user roles, repository permissions
- 12-api-and-integrations.md - Bitbucket REST API v2, webhooks, app passwords, Atlassian marketplace
- 13-all-bitbucket-features-reference.md

#### other-platforms/azure-devops/

Planned files:
- 00-azure-devops-overview.md
- 01-what-is-azure-devops.md - history (VSS to TFS to VSTS to Azure DevOps 2018), five services, Services vs Server
- 02-creating-an-account.md - Azure/Microsoft account, organisations and projects, PATs, SSH keys, free tier
- 03-azure-repos.md - Git repos, branch policies, pull requests, code review, TFVC vs Git
- 04-azure-boards.md - work items (Epic/Feature/User Story/Task/Bug), Kanban, sprints, backlogs, queries
- 05-azure-pipelines.md - full azure-pipelines.yml guide, triggers, stages, jobs, steps, tasks, variables, environments, agents
- 06-azure-artifacts.md - package feeds (npm/NuGet/PyPI/Maven/Cargo), upstream sources, feed permissions
- 07-azure-test-plans.md - manual test plans, test suites, exploratory testing
- 08-branch-policies.md - requiring PRs, minimum reviewers, build validation, comment resolution
- 09-security-and-permissions.md - organisation/project/team/repo permissions, security groups
- 10-azure-devops-server.md - on-premises installation, requirements, licensing, upgrading from TFS
- 11-extensions-and-marketplace.md - Azure DevOps Marketplace, popular extensions
- 12-azure-devops-cli.md - az devops CLI, installation, authentication, commands
- 13-api-and-integrations.md - REST API, service hooks, GitHub integration, service connections
- 14-all-azure-devops-features-reference.md

#### other-platforms/gitea/

Planned files:
- 00-gitea-overview.md
- 01-what-is-gitea.md - fork of Gogs (2016), Go-based, lightweight, Gitea Ltd controversy (2022), current version
- 02-installation.md - binary/Docker/packages on all three OS, very low system requirements, app.ini, initial setup
- 03-creating-an-account.md - registration, SSH keys, access tokens, 2FA, OAuth2 providers
- 04-repositories.md - creating repos, mirroring from GitHub/GitLab, LFS, releases, topics
- 05-pull-requests-and-code-review.md - PRs, reviewers, inline comments, protected branches
- 06-gitea-actions.md - GitHub Actions compatible, act_runner installation, workflow syntax, differences
- 07-issues-and-projects.md - issues, labels, milestones, Kanban projects, issue templates
- 08-packages.md - package registry (Docker/npm/PyPI/Maven/NuGet/Cargo/Helm)
- 09-administration.md - admin panel, user management, instance settings, LDAP/SAML/OAuth2
- 10-gitea-vs-forgejo.md - the 2022 split, what diverged, which to choose
- 11-api-and-integrations.md - REST API (Swagger at /api/swagger), webhooks
- 12-all-gitea-features-reference.md

#### other-platforms/forgejo/

Planned files:
- 00-forgejo-overview.md
- 01-what-is-forgejo.md - the 2022 governance controversy, community fork, Forgejo e.V., relationship with Codeberg
- 02-installation.md - installing Forgejo, differences from Gitea installation
- 03-creating-an-account.md - registration, SSH keys, access tokens, 2FA
- 04-repositories-and-features.md - what Forgejo has that Gitea does not, federation, unique features
- 05-forgejo-actions.md - Actions runner, workflow syntax, GitHub Actions compatibility
- 06-federation.md - ActivityPub support, F3 format, what federation means for Git hosting
- 07-forgejo-vs-gitea.md - feature comparison, governance comparison, migration between the two
- 08-api-and-administration.md - API (compatible with Gitea), admin panel, webhooks
- 09-all-forgejo-features-reference.md

#### other-platforms/codeberg/

Planned files:
- 00-codeberg-overview.md
- 01-what-is-codeberg.md - Codeberg e.V. (nonprofit, Berlin), founded 2019, runs Forgejo, mission, GDPR compliance, statistics
- 02-creating-an-account.md - signup, no phone/credit card required, SSH keys, access tokens, 2FA, free for open source
- 03-repositories-and-features.md - creating repos, organisations, topics, releases, package registry
- 04-codeberg-pages.md - free static site hosting, branch-based, custom domains, HTTPS, comparison with GitHub Pages
- 05-ci-cd-on-codeberg.md - no built-in CI/CD, Woodpecker CI (recommended), Forgejo Actions, other options
- 06-community-and-philosophy.md - FOSS principles, no ads/tracking/VC money, who should use Codeberg
- 07-codeberg-vs-github.md - feature comparison, what Codeberg lacks, what it offers instead
- 08-api-and-integrations.md - Forgejo-compatible API, webhooks, integrations
- 09-all-codeberg-features-reference.md

**Status: In progress**

---

## Phase 4 - Tools and environment

The `ides/` folder covering IDEs and editors, and the `terminal/` folder covering the command line.

Note: ides/ files can reference all platforms accurately now that all platform folders will be complete.

### ides/ folder

Planned files:
- 00-ides-overview.md
- 01-vs-code.md - Git integration, GitLens, GitHub extension, GitLab extension, Copilot, Duo
- 02-jetbrains.md - IntelliJ/PyCharm/WebStorm, built-in Git, GitHub and GitLab plugins
- 03-neovim.md - fugitive.vim, gitsigns.nvim, GitHub/GitLab CLI integration
- 04-github-desktop.md - visual Git client for beginners
- 05-other-editors.md - Sublime Text, Zed, Cursor, Helix

### terminal/ folder

Planned files covering shell setup, Git configuration, aliases, productivity tools (fzf, bat, delta, lazygit) and terminal-based Git workflows.

---

## Phase 5 - Real world and reference

### real-world/ folder

Practical scenarios combining everything:
- Contributing to open source (fork, branch, PR, iterate)
- Working in a team (branching strategies, code review norms, CI/CD)
- GitOps workflows
- Monorepo patterns
- Migrating between platforms (GitHub to GitLab, GitLab to Gitea, etc.)
- Recovering from common disasters

### reference/ folder

Quick lookup resources:
- Cheatsheet (all essential commands on one page)
- Glossary (every term defined)
- Common mistakes (the most frequent errors and how to fix them)

### resources/ folder

Curated external links:
- Official documentation
- Books and courses
- Useful tools and plugins
- Community forums

---

## Phase 6 - Community and polish

The finishing touches that make the course complete.

- [ ] `introduction/02-how-to-use-this-course.md` - full rewrite once all folders are complete (deliberately deferred to avoid repeated rewrites as content grows)
- [ ] Root-level Codespaces dev container setup
- [ ] Root-level setup documentation for all tools
- [ ] Banner image and visual assets
- [ ] `first-contribution/` sandbox project for practising open source contributions
- [ ] Cross-reference audit (check all internal links are correct)
- [ ] Accessibility review of all content

---

## Future ideas

Ideas that may be added after the core course is complete. Not committed to.

- Video walkthroughs for key topics
- Interactive exercises using GitLab CI/CD within the repository itself
- Translations (starting with Spanish and French)
- `github-actions-advanced/` folder (deep dive beyond the github/ intro)
- `gitlab-cicd-advanced/` folder (deep dive beyond the gitlab/ intro)
- `bitbucket-pipelines-advanced/` folder
- `azure-pipelines-advanced/` folder

---

## How to suggest something

Open a Discussion on GitHub, open an issue using the content suggestion template, or open a pull request directly.

All suggestions welcome. Priority is given to: correcting factual errors, updating outdated information, improving clarity on confusing topics and adding missing OS coverage (Windows/Mac/Linux).

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
