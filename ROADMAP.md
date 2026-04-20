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

**Version:** 1.0.0
**Stage:** Phase 4 complete - ides-and-editors/ done. Phase 5 (terminal/) next.
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
| ides-and-editors/                | Complete    |
| terminal/                        | Next        |
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

### github/ folder - Complete (28 files)

- [x] 00-github-overview.md through 27-all-github-features-reference.md

### gitlab/ folder - Complete (16 files)

- [x] 00-gitlab-overview.md through 15-all-gitlab-features-reference.md

### other-platforms/ folder - Complete (62 files across 5 subfolders)

- [x] bitbucket/ - 14 files (00-13)
- [x] azure-devops/ - 15 files (00-14)
- [x] gitea/ - 13 files (00-12)
- [x] forgejo/ - 10 files (00-09)
- [x] codeberg/ - 10 files (00-09)

**Phase 3 Status: Complete (106 files across github/ + gitlab/ + other-platforms/)**

---

## Phase 4 - Tools and environment

The `ides-and-editors/` folder covering IDEs and editors, and the `terminal/` folder covering the command line.

### ides-and-editors/ folder - Complete (14 files)

Files cover VS Code, JetBrains, Neovim, GitHub Desktop, Cursor, Windsurf, Google Antigravity, AWS Kiro, Zed, Helix, Sublime Text, and a broad other-editors file covering Emacs/Magit, Visual Studio, Xcode, Android Studio, Eclipse, Nova, Vim classic and nano.

- [x] 00-ides-overview.md - landscape overview, three generations of Git tooling, VS Code fork family, how-to-choose guide
- [x] 01-vs-code.md - Source Control panel, GitLens, GitHub Pull Requests extension, GitLab Workflow extension, Copilot, Duo
- [x] 02-jetbrains.md - built-in Git, changelists, shelving, Log tab, interactive rebase editor, three-pane merge tool
- [x] 03-neovim.md - vim-fugitive, gitsigns.nvim, neogit, diffview.nvim, lazygit.nvim, lazy.nvim
- [x] 04-github-desktop.md - visual Git client, line-level staging, Copilot commit messages, Git hooks support
- [x] 05-cursor.md - VS Code fork, Cursor Blame, Composer/Agent mode, Background Agents, Cloud Agents, .cursor/rules
- [x] 06-windsurf.md - Cascade agent, Flow awareness, Wave 13 Git worktrees, Windsurf 2.0 Agent Command Center
- [x] 07-antigravity.md - Google/ex-Windsurf team, dual-view architecture, Artifacts system, Manager view
- [x] 08-kiro.md - Code-OSS fork, spec-driven development, Hooks system with Git events, GitHub Autonomous Agent
- [x] 09-zed.md - GPU-accelerated Rust editor, native Git since March 2025, Diff View, multiplayer Co-Authored-By
- [x] 10-helix.md - minimal by design, gutter indicators only, lazygit via tmux popup, :sh integration
- [x] 11-sublime-text.md - GitSavvy, status dashboard, Repo History with interactive rebase, Line History
- [x] 12-other-editors.md - Emacs/Magit, Visual Studio, Xcode, Android Studio, Eclipse/EGit, Nova, Vim, nano
- [x] 13-all-ides-features-reference.md - complete comparison tables, platform support, pricing, quick decision guide

**Phase 4 ides-and-editors/ Status: Complete (14 files)**

### terminal/ folder - Next

Planned files covering shell setup, Git configuration, aliases and productivity tools (fzf, bat, delta, lazygit).

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
