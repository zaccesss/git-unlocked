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

## [1.3.0] - 2026-10-15

### Added

- Git 2.49 to 2.56 commands in the command reference: `git backfill`, `git repo`, `git last-modified`, `git history`, `git replay` and `git refs`, with the experimental ones marked
- A section on reference storage and the reftable format in Git internals
- `includeIf "worktree:"` and `fetch.followRemoteHEAD` in advanced configuration, `git backfill` in large repositories and `git history` in rebase
- Stacked pull requests on GitHub with the `gh stack` extension
- Archiving a pull request
- Stacked merge requests on GitLab with `glab stack`, automatic Code Owner reviewers, automatic rebase before merge and enforced merge trains
- Rule insights, path exceptions and converting branch protection to a ruleset
- Dependabot cooldown with a configuration example
- Workflow lockfiles and the self-repository syntax in GitHub Actions
- Recent features in GitLab 19.x, Gitea 1.26 to 28, Forgejo v15 and v16, Azure Repos and Azure DevOps Server
- New `gh` and `glab` commands, including `gh pr checkout --worktree` and `glab repo prune`
- Git worktree support in GitHub Desktop and JetBrains IDEs plus recent diff improvements in VS Code

### Updated

- Current versions across the course: Git 2.56.0, GitHub CLI 2.102.0, GitLab 19.5, glab 1.123.0, Gitea 28.1, Forgejo v16.0, GitHub Desktop 3.6.7 and forgit 26.10.0
- Git 3.0 plans: SHA-256, reftable and `main` as defaults for new repositories, with no release date yet
- SSH advice: GitHub no longer accepts SHA-1 RSA signatures and new RSA keys must be at least 3072 bits
- GitHub Actions examples use the current major version of every official action, Node.js 24 and the current runner images
- GitHub Classroom marked as retired, with a template repository and Actions workflow that replace it
- GitLab self-managed requirements: PostgreSQL 17, Valkey and the supported operating systems
- GitLab Duo models, Azure Pipelines images and the Antigravity pricing
- Bitbucket app passwords described as switched off, with API tokens as the replacement
- Windsurf noted as renamed to Devin Desktop

### Fixed

- The gitleaks example pins a real commit of the action
- The GitHub release example uses `gh release create` instead of an archived action
- Forgejo timeline rows in the Gitea history
- Excluded linusakesson.net from the link check while it times out

### Removed

- The unused banner image and its roadmap entry

## [1.2.6] - 2026-10-15

### Fixed

- The roadmap gives the current version as 1.2.6 and the course as 12 sections. It also marks the banner as done
- The README and the welcome page describe operating system coverage, exercises and common mistakes as features of the lesson files rather than of every file
- The pull request template gives isaacadjei.me as the website

### Updated

- The citation file and the DOI badge use the all-versions DOIs for the release archive and the technical note, so they always resolve to the latest version

### Changed

- Tidied the contributor guide and the pull request template.
- Issue templates converted from markdown frontmatter to YAML issue forms

## [1.2.5] - 2026-09-11

### Fixed

- Excluded two unreachable hosts from the link check (`bazel.build/docs` restructured its site, `demo.gitea.com` was intermittently unreachable from GitHub-hosted runners)
- Excluded a looping Zenodo DOI redirect from the link check
- Refreshed stale platform facts and fixed the broken link-check CI
- Copy-edited a few lesson pages

### Changed

- Bumped `DavidAnson/markdownlint-cli2-action` from 24.1.0 to 24.2.0

## [1.2.4] - 2026-08-05

### Removed

- Dropped the animated `capsule-render.vercel.app` footer banner and the surrounding centered "Made with" block from `README.md`, a leftover from an old README-generator style not used anywhere else in the project's repositories

### Fixed

- Replaced the footer with a plain closing line and bumped the README's version badge from 1.2.2 to 1.2.4 to match this release
- Removed a duplicate `---` divider left in `README.md` right before the footer
- Bumped `CITATION.cff`'s `version` and `date-released` fields from 1.2.2 to 1.2.4, matching this release

### Added

- A `[!IMPORTANT]` alert in `README.md`'s Contact and Support section pointing to `SECURITY.md` for private security reporting

## [1.2.3] - 2026-08-05

### Fixed

- Scoped the markdownlint and linkcheck workflow tokens to read-only contents access, closing two CodeQL actions/missing-workflow-permissions alerts. Both jobs only check out the repo and run a read-only check, neither writes back

## [1.2.2] - 2026-08-03

### Changed

- Relicensed the project from MIT to CC BY-SA 4.0 across `LICENSE` and the README licence badge
- Retired the per-repo `automerge-dependabot.yml`, `update-pr-branches.yml` and `stale.yml` workflows now that Dependabot auto-merge, branch updates and stale handling are managed centrally by repo-ops automation, with no behaviour change

### Updated

- Bumped `DavidAnson/markdownlint-cli2-action` from 24 to 24.1.0

### Fixed

- Corrected `CITATION.cff`'s licence field and abstract wording from MIT to CC BY-SA 4.0 and two more stale MIT mentions in `FAQ.md` and `00-welcome/README.md` that the original relicense missed
- Corrected the file count from 217 to 182 in `README.md` and `CITATION.cff`, matching the README's own course-contents table and the actual count on disk
- Repaired 13 broken internal links across `10-resources/index.md`, `07-terminal/13-all-terminal-tools-reference.md`, `00-welcome/README.md`, `01-introduction/03-setting-up.md`, `02-git/03-how-git-works.md`, `02-git/04-core-concepts.md` and `02-git/09-remote-repos.md`
- Replaced three dead/stale links in the Gitea section (`try.gitea.io`, `docs.gitea.com/administration`, `docs.gitea.com/api/1.20/`) with working current URLs
- Corrected Forgejo's version number from a mismatched v14.x to the internally-consistent v9.0.x across the Gitea comparison files
- Removed a self-contradictory "SQL Server 2025 (when released)" note in the Azure DevOps section, since the same section already documents it as shipped
- Corrected GitLab Duo Core's introduction date from November 2025 to May 2025 to match the course's own stated release cadence
- Replaced a stale pinned GitLab education support price with a pointer to GitLab's live pricing page
- Updated GitHub Copilot's model references throughout `19-copilot.md` and `27-all-github-features-reference.md` from the GPT-4o/Claude Sonnet 3.5/o3 generation to the current GPT-5.x/Claude 4.x-5/Gemini 3.x lineup
- Aligned two stale `gitleaks` version pins (v8.24.2) in `08-real-world/` with the current version already used in `09-reference/security.md` (v8.30.1)
- Updated stale tool version references: VS Code's Git recommendation, iTerm2, GitHub Desktop and zoxide

## [1.2.1] - 2026-07-06

### Updated

- Auto-merge now covers all Dependabot PRs including major bumps (previously only same-major patch/minor bumps merged), gated by the required CI checks and any PR labelled `automerge`
- Added a `Repo maintenance` workflow to delete merged PR branches the merge could not clean up
- Added `.gitignore` for macOS `.DS_Store`

### Fixed

- Corrected the section count in `CITATION.cff` from 11 to 12 and aligned the file count with the README
- Served the DOI badge from `img.shields.io` instead of `zenodo.org` so it renders reliably through GitHub's image proxy in the README
- Excluded `oliviac.dev` and the `gitea.com` homepage from linkcheck, which kept timing out from CI runners despite the 30s timeout and retries (the `docs.gitea.com`, `dl.gitea.com` and `blog.gitea.com` subdomains stay checked)
- Replaced dead GitLab Git cheat sheet PDF (404) with the maintained GitLab Git docs reference in `10-resources/index.md`
- Raised lychee `--timeout` to 30s and excluded `netflixtechblog.com` from linkcheck to stop intermittent CI timeout failures
- Excluded `docs.gitlab.com` and `status.atlassian.com` from linkcheck: auth-gated pages return 403 or 405 to automated requests but are valid links
- Added 405 to accepted status codes in `linkcheck.yml`
- Replaced dead `weave.works/technologies/gitops/` link (domain parked) with the OpenGitOps specification repository in `02-git/24-gitops.md`

### Removed

- Deleted `oxford_scan.txt`, a leftover writing-style scan artefact committed to the repo root by accident

---

## Long-term plans

- GitHub Pages site at zaccesss.github.io/git-unlocked
- Interactive HTML quiz pages with instant answer checking
- Animated SVG diagrams for key Git concepts
- Cross-reference audit of all internal links
- Accessibility review
- Banner image and assets folder

---

## [1.2.0] - 2026-06-15

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

#### Repository meta

- `CITATION.cff` - Zenodo DOI citation metadata for academic citation
- `.github/FUNDING.yml` - Patreon and Buy Me a Coffee support links
- `.github/CODEOWNERS` - repository ownership declaration
- `.github/DISCUSSION_TEMPLATE/` - four discussion templates: general questions, bug discussion, ideas and show-and-tell
- `.github/dependabot.yml` - weekly Dependabot updates for GitHub Actions
- `.github/workflows/stale.yml` - automatic stale issue and PR management
- `.github/workflows/automerge-dependabot.yml` - auto-merge safe Dependabot patch and minor bumps

### Updated

- `README.md` - trimmed and restructured as a pure navigation page; added sections for real-world/, reference/, resources/ and first-contribution/; added Zenodo DOI badge
- `.github/FUNDING.yml` - GitHub Sponsors added as first entry; order updated to github → buy_me_a_coffee → patreon
- `.github/workflows/linkcheck.yml` - added `workflow_dispatch` trigger
- `CHANGELOG.md` - updated for v1.2.0
- `ROADMAP.md` - all sections marked complete; long-term items noted

### Fixed

- Updated broken VS Code Copilot docs link in `06-ides-and-editors/01-vs-code.md` (`/docs/copilot/overview` → `/docs/agents/concepts/overview`)
- Removed dead link to `graphite.com/blog/bitkeeper-linux-story-of-git-creation` (404) from `02-git/01-what-is-version-control.md`
- Added `packagecontrol.io`, `blog.jetbrains.com` and `www.gnu.org/software/bash` to `.lycheeignore` to suppress intermittent CI timeouts and 500s from bot-blocking domains
- Disabled `MD060/table-column-style` in `.markdownlint.json` following markdownlint-cli2 upgrade to v23

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

[Unreleased]: https://github.com/zaccesss/git-unlocked/compare/v1.3.0...HEAD
[1.3.0]: https://github.com/zaccesss/git-unlocked/releases/tag/v1.3.0
[1.2.6]: https://github.com/zaccesss/git-unlocked/releases/tag/v1.2.6
[1.2.5]: https://github.com/zaccesss/git-unlocked/releases/tag/v1.2.5
[1.2.2]: https://github.com/zaccesss/git-unlocked/releases/tag/v1.2.2
[1.2.1]: https://github.com/zaccesss/git-unlocked/releases/tag/v1.2.1
[1.2.0]: https://github.com/zaccesss/git-unlocked/releases/tag/v1.2.0
[1.1.0]: https://github.com/zaccesss/git-unlocked/releases/tag/v1.1.0
[1.0.0]: https://github.com/zaccesss/git-unlocked/releases/tag/v1.0.0
