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

**Version:** 0.9.0
**Stage:** Phase 3 complete - git/ done, github/ done, gitlab/ done, other-platforms/ done. Phase 4 (ides/) next.
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
| other-platforms/bitbucket/       | Complete    |
| other-platforms/azure-devops/    | Complete    |
| other-platforms/gitea/           | Complete    |
| other-platforms/forgejo/         | Complete    |
| other-platforms/codeberg/        | Complete    |
| ides/                            | In progress |
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
- [ ] introduction/04-version-control-concepts.md - planned new file covering what version control is, why it matters and how Git fits in, before the git/ folder begins
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

Each platform has its own subfolder at full depth.

#### other-platforms/bitbucket/ - Complete (14 files)

- [x] 00-bitbucket-overview.md
- [x] 01-what-is-bitbucket.md
- [x] 02-creating-an-account.md
- [x] 03-bitbucket-vs-github-vs-gitlab.md
- [x] 04-repositories.md
- [x] 05-pull-requests.md
- [x] 06-bitbucket-pipelines.md
- [x] 07-branch-permissions.md
- [x] 08-jira-integration.md
- [x] 09-security-features.md
- [x] 10-bitbucket-cloud-vs-data-center.md
- [x] 11-workspaces-and-permissions.md
- [x] 12-api-and-integrations.md
- [x] 13-all-bitbucket-features-reference.md

#### other-platforms/azure-devops/ - Complete (15 files)

- [x] 00-azure-devops-overview.md
- [x] 01-what-is-azure-devops.md
- [x] 02-creating-an-account.md
- [x] 03-azure-repos.md
- [x] 04-azure-boards.md
- [x] 05-azure-pipelines.md
- [x] 06-azure-artifacts.md
- [x] 07-azure-test-plans.md
- [x] 08-branch-policies.md
- [x] 09-security-and-permissions.md
- [x] 10-azure-devops-server.md
- [x] 11-extensions-and-marketplace.md
- [x] 12-azure-devops-cli.md
- [x] 13-api-and-integrations.md
- [x] 14-all-azure-devops-features-reference.md

#### other-platforms/gitea/ - Complete (13 files)

- [x] 00-gitea-overview.md
- [x] 01-what-is-gitea.md
- [x] 02-installation.md
- [x] 03-creating-an-account.md
- [x] 04-repositories.md
- [x] 05-pull-requests-and-code-review.md
- [x] 06-gitea-actions.md
- [x] 07-issues-and-projects.md
- [x] 08-packages.md
- [x] 09-administration.md
- [x] 10-gitea-vs-forgejo.md
- [x] 11-api-and-integrations.md
- [x] 12-all-gitea-features-reference.md

#### other-platforms/forgejo/ - Complete (10 files)

- [x] 00-forgejo-overview.md
- [x] 01-what-is-forgejo.md
- [x] 02-installation.md
- [x] 03-creating-an-account.md
- [x] 04-repositories-and-code-review.md
- [x] 05-forgejo-actions.md
- [x] 06-issues-packages-and-federation.md
- [x] 07-administration.md
- [x] 08-api-and-integrations.md
- [x] 09-all-forgejo-features-reference.md

#### other-platforms/codeberg/ - Complete (10 files)

- [x] 00-codeberg-overview.md
- [x] 01-what-is-codeberg.md
- [x] 02-creating-an-account.md
- [x] 03-repositories-and-code-review.md
- [x] 04-issues-and-projects.md
- [x] 05-codeberg-ci.md
- [x] 06-pages.md
- [x] 07-packages-and-releases.md
- [x] 08-organisations-and-teams.md
- [x] 09-all-codeberg-features-reference.md

**Phase 3 Status: Complete (82 files across github/ + gitlab/ + 5 other-platforms/)**

---

## Phase 4 - Tools and environment

The `ides/` folder covering IDEs and editors, and the `terminal/` folder covering the command line.

Note: ides/ files can reference all platforms accurately now that all platform folders are complete.

### ides/ folder

- [ ] 00-ides-overview.md
- [ ] 01-vs-code.md - Git integration (source control panel, timeline, inline blame), GitLens, GitHub Pull Requests extension, GitLab Workflow extension, GitHub Copilot in VS Code, Duo in VS Code
- [ ] 02-jetbrains.md - built-in Git (IntelliJ/PyCharm/WebStorm/Rider/GoLand), GitHub plugin, GitLab plugin, commit tool window, interactive rebase, shelving
- [ ] 03-neovim.md - fugitive.vim, gitsigns.nvim, neogit, GitHub/GitLab CLI integration from terminal
- [ ] 04-github-desktop.md - visual Git client for beginners, cloning, committing, branching, pull requests without CLI
- [ ] 05-other-editors.md - Sublime Text (GitSavvy), Zed (built-in Git), Cursor (AI-first with Git), Helix (no built-in, terminal workflow)

### terminal/ folder

- [ ] Planned: shell setup, Git configuration, aliases, productivity tools (fzf, bat, delta, lazygit), terminal-based Git workflows

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
- [ ] `introduction/04-version-control-concepts.md` - new file explaining version control concepts in plain language before the Git folder begins
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
