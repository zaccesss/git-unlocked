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
**Stage:** Phase 3 in progress - git/ complete, github/ complete, gitlab/ complete, ides/ next
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
| ides/                            | Next        |
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

The `github/` and `gitlab/` folders covering the two main Git hosting platforms.

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

---

## Phase 4 - Tools and environment

The `ides/` folder covering IDEs and editors, and the `terminal/` folder covering the command line.

### ides/ folder (next)

Planned files:
- 00-ides-overview.md
- 01-vs-code.md - Git integration, GitLens, GitHub extension, GitLab extension, Copilot, Duo
- 02-jetbrains.md - IntelliJ/PyCharm/WebStorm, built-in Git, GitHub and GitLab plugins
- 03-neovim.md - fugitive.vim, gitsigns.nvim, GitHub/GitLab CLI integration
- 04-github-desktop.md - visual Git client for beginners
- 05-other-editors.md - Sublime Text, Zed, Cursor, Helix

Note: IDE files can reference both GitHub and GitLab features accurately now that both platform folders are complete.

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
- Migrating from GitHub to GitLab and back
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
- `bitbucket/` folder (lower priority - Bitbucket has smaller market share)
- `github-actions-advanced/` folder (deep dive beyond the github/ intro)
- `gitlab-cicd-advanced/` folder (deep dive beyond the gitlab/ intro)

---

## How to suggest something

Open a Discussion on GitHub, open an issue using the content suggestion template, or open a pull request directly.

All suggestions welcome. Priority is given to: correcting factual errors, updating outdated information, improving clarity on confusing topics and adding missing OS coverage (Windows/Mac/Linux).

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
