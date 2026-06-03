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

### Added

- `.github/FUNDING.yml` - Patreon and Buy Me a Coffee support links
- `.github/CODEOWNERS` - repository ownership declaration
- `.github/DISCUSSION_TEMPLATE/` - four discussion templates: general questions, bug discussion, ideas and show-and-tell
- `.github/dependabot.yml` - weekly Dependabot updates for GitHub Actions
- `.github/workflows/stale.yml` - automatic stale issue and PR management
- `.github/workflows/automerge-dependabot.yml` - auto-merge safe Dependabot patch and minor bumps

### Fixed

- Removed dead link to `graphite.com/blog/bitkeeper-linux-story-of-git-creation` (404) from `02-git/01-what-is-version-control.md`
- Added `packagecontrol.io`, `blog.jetbrains.com` and `www.gnu.org/software/bash` to `.lycheeignore` to suppress intermittent CI timeouts and 500s from bot-blocking domains
- Disabled `MD060/table-column-style` in `.markdownlint.json` following markdownlint-cli2 upgrade to v23

---

## Long-term plans

- GitHub Pages site at zaccesss.github.io/git-unlocked
- Interactive HTML quiz pages with instant answer checking
- Animated SVG diagrams for key Git concepts
- Cross-reference audit of all internal links
- Accessibility review
- Banner image and assets folder

---

## [1.2.0] - 2026-04-21

### Added

#### real-world/ - 8 files

Practical scenarios showing Git in real professional contexts.

- `real-world/00-real-world-overview.md` - section map, reading order and the three key themes
- `real-world/01-contributing-to-open-source.md` - fork workflow, upstream sync, fixup commit review pattern, GitHub vs GitLab differences, Linux/Kubernetes/Rust project flows
- `real-world/02-working-in-a-team.md` - all four branching strategies, code review, CODEOWNERS, Rulesets, merge queues, real examples from Google/Shopify/Microsoft/Netflix
- `real-world/03-gitops-workflows.md` - OpenGitOps principles, ArgoCD v3.3.7, Flux v2.8.2, Kustomize overlays, Kargo, real adoption examples
- `real-world/04-monorepo-patterns.md` - partial clone, sparse-checkout cone mode, fsmonitor, Scalar, Nx 22.x, Turborepo 2.8, Bazel 9/8, real company examples
- `real-world/05-migrating-between-platforms.md` - mirror clone, Git LFS migration, GitLab importer, GEI toolkit, full checklist, 2017 GitLab DB outage war story
- `real-world/06-recovering-from-disasters.md` - reflog recovery, bad merge, accidental force push, committed secrets, gitleaks, TruffleHog, revert vs reset rule
- `real-world/07-all-real-world-reference.md` - consolidated quick-reference for all patterns from the section

#### reference/ - 4 files

- `reference/cheatsheet.md` - every essential Git command organised by task including monorepo and performance commands
- `reference/glossary.md` - A to W, every term in Git and version control defined plainly
- `reference/common-mistakes.md` - most frequent Git errors grouped by category with exact fix commands
- `reference/security.md` - comprehensive security reference: gitleaks, TruffleHog, push protection, signing, Rulesets, SLSA, cosign v3, Trusted Publishing, Dependabot cooldown, SOPS+age, GCM, IDE security, terminal hygiene, five real-world incidents and the five controls that prevent most supply chain compromises

#### resources/ - 1 file

- `resources/index.md` - curated guide to 120+ learning resources: official docs, books with ISBNs, free and paid video courses, interactive platforms, websites, cheatsheets, podcasts, GitHub repositories, GUI clients, communities, newsletters and certifications; recommended paths by level and resources-to-avoid section

#### first-contribution/ - 2 files

- `11-first-contribution/README.md` - step-by-step guide for making a first open source PR, with troubleshooting
- `11-first-contribution/contributors.md` - the file contributors add their name to

#### introduction/ - 2 new files

- `introduction/02-how-to-use-this-course.md` - full rewrite; replaces initial placeholder
- `introduction/04-version-control-concepts.md` - new bridging file covering what version control is, the history of VCS tools and the core Git mental model

### Updated

- `README.md` - trimmed and restructured as a pure navigation page; added sections for real-world/, reference/, resources/ and first-contribution/
- `CHANGELOG.md` - added v1.2.0 entry
- `ROADMAP.md` - all sections marked complete; long-term items noted

---

## [1.1.0] - 2026-04-20

### Added

#### terminal/ - 14 files

- `terminal/00-terminal-overview.md` through `terminal/13-all-terminal-tools-reference.md`
- Full terminal toolchain: shell setup, safety, config, aliases, delta, lazygit, fzf, bat, tig, gitui, starship, zoxide, forgit, complete workflow

### Fixed

- `terminal/04-terminal-safety.md` - MD038 space inside backtick code span

---

## [1.0.0] - 2026-04-20

### Added

#### ides-and-editors/ - 14 files

- `ides-and-editors/00-ides-overview.md` through `ides-and-editors/13-all-ides-features-reference.md`
- VS Code, JetBrains, Neovim, GitHub Desktop, Cursor, Windsurf, Antigravity, Kiro, Zed, Helix, Sublime Text, other editors (Emacs/Magit, Visual Studio, Xcode, Android Studio, Eclipse, Nova, Vim, nano)

#### introduction/ - 3 files

- `00-welcome/README.md`, `introduction/02-how-to-use-this-course.md` (initial), `introduction/03-setting-up.md`

#### git/ - 29 files

- `git/00-git-overview.md` through `git/28-all-git-commands.md`
- Complete Git coverage from installation to internals, packfiles and reflog

#### github/ - 28 files

- `github/00-github-overview.md` through `github/27-all-github-features-reference.md`
- Full GitHub platform coverage including Copilot, Actions, Pages, security, CLI and organisations

#### gitlab/ - 16 files

- `gitlab/00-gitlab-overview.md` through `gitlab/15-all-gitlab-features-reference.md`
- Full GitLab coverage including CI/CD, Duo, self-hosting and API

#### other-platforms/ - 62 files

- `other-platforms/bitbucket/` - 14 files
- `other-platforms/azure-devops/` - 15 files
- `other-platforms/gitea/` - 13 files
- `other-platforms/forgejo/` - 10 files
- `other-platforms/codeberg/` - 10 files

#### Infrastructure

- markdownlint CI, link checker CI, issue templates, PR template, `.editorconfig`, `.markdownlint.json`
- Root meta files: CONTRIBUTING, CODE_OF_CONDUCT, SECURITY, SUPPORT, FAQ, HALL_OF_FAME

### Fixed

- `other-platforms/gitea/11-api-and-integrations.md` - MD038 errors and restored truncated sections

---

[Unreleased]: https://github.com/zaccesss/git-unlocked/compare/v1.2.0...HEAD
[1.2.0]: https://github.com/zaccesss/git-unlocked/releases/tag/v1.2.0
[1.0.0]: https://github.com/zaccesss/git-unlocked/releases/tag/v1.0.0
