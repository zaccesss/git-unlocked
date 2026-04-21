# Changelog

All notable changes to git-unlocked are documented here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and this project uses [Semantic Versioning](https://semver.org/).

---

## [Unreleased]

### Planned

- `real-world/` - practical scenario files (open source contributions, team workflows, GitOps, monorepos, platform migration, disaster recovery)
- `reference/` - cheatsheet, glossary and common mistakes
- `resources/` - curated external links index
- `first-contribution/` - beginner PR sandbox
- GitHub Pages setup
- Interactive HTML quiz pages
- Animated SVG diagrams
- Cross-reference audit
- Accessibility review
- README trim and `introduction/02` full rewrite (deferred until all folders complete)
- New file `introduction/04-version-control-concepts.md`

---

## [1.1.0] - 2025-07-10

### Added

#### terminal/ - 14 files

Complete terminal toolchain section covering shell setup, safety, configuration and every major Git terminal tool across Windows, Mac and Linux.

- `terminal/00-terminal-overview.md` - section introduction and tool map
- `terminal/01-shell-setup.md` - Bash, Zsh, Fish and PowerShell setup for Git work
- `terminal/02-git-config-and-aliases.md` - global config, per-project config and alias patterns
- `terminal/03-terminal-workflow.md` - day-to-day Git in the terminal from clone to push
- `terminal/04-terminal-safety.md` - preventing disasters: `.gitignore`, credential hygiene, force-push guards
- `terminal/05-delta.md` - beautiful diffs with the delta pager
- `terminal/06-lazygit.md` - full-screen terminal UI for Git
- `terminal/07-fzf.md` - fuzzy finder integrated with Git for branches, logs and stash
- `terminal/08-bat.md` - syntax-highlighted file viewer as a `cat` replacement
- `terminal/09-tig.md` - text-mode Git repository browser
- `terminal/10-gitui.md` - Rust-based terminal UI for Git
- `terminal/11-starship.md` - cross-shell prompt with Git status integration
- `terminal/12-zoxide.md` - smarter `cd` for navigating Git projects
- `terminal/13-complete-terminal-workflow.md` - putting all tools together in a real workflow

### Fixed

- `terminal/04-terminal-safety.md` line 538 - MD038 space inside backtick code span (leading space moved outside backtick)

---

## [1.0.0] - 2025-06-15

### Added

#### introduction/ - 3 files

- `introduction/00-introduction-overview.md`
- `introduction/01-welcome.md`
- `introduction/02-how-to-use-this-course.md`

#### git/ - 29 files

Complete pure Git coverage from `git init` to internals, packfiles and reflog.

- `git/00-git-overview.md`
- `git/01-what-is-version-control.md`
- `git/02-installing-git.md`
- `git/03-first-time-setup.md`
- `git/04-your-first-repo.md`
- `git/05-staging-and-committing.md`
- `git/06-viewing-history.md`
- `git/07-undoing-changes.md`
- `git/08-branching.md`
- `git/09-merging.md`
- `git/10-rebasing.md`
- `git/11-stashing.md`
- `git/12-tagging.md`
- `git/13-remote-repos.md`
- `git/14-fetch-pull-push.md`
- `git/15-resolving-conflicts.md`
- `git/16-gitignore.md`
- `git/17-git-log-advanced.md`
- `git/18-git-diff.md`
- `git/19-cherry-pick.md`
- `git/20-bisect.md`
- `git/21-submodules.md`
- `git/22-worktrees.md`
- `git/23-hooks.md`
- `git/24-reflog.md`
- `git/25-internals.md`
- `git/26-packfiles.md`
- `git/27-signing-commits.md`
- `git/28-all-git-reference.md`

#### github/ - 28 files

Full GitHub platform coverage including Copilot, Actions, Pages, security and CLI.

- `github/00-github-overview.md` through `github/27-all-github-reference.md`

#### gitlab/ - 16 files

Full GitLab platform coverage including CI/CD, Duo, self-hosting and API.

- `gitlab/00-gitlab-overview.md` through `gitlab/15-all-gitlab-reference.md`

#### other-platforms/bitbucket/ - 14 files

Bitbucket Cloud and Data Center coverage.

- `other-platforms/bitbucket/00-bitbucket-overview.md` through `other-platforms/bitbucket/13-all-bitbucket-reference.md`

#### other-platforms/azure-devops/ - 15 files

Azure DevOps all five services: Boards, Repos, Pipelines, Test Plans and Artifacts.

- `other-platforms/azure-devops/00-azure-devops-overview.md` through `other-platforms/azure-devops/14-all-azure-devops-reference.md`

#### other-platforms/gitea/ - 13 files

Gitea self-hosted Git server coverage.

- `other-platforms/gitea/00-gitea-overview.md` through `other-platforms/gitea/12-all-gitea-reference.md`

#### other-platforms/forgejo/ - 10 files

Forgejo fork with ActivityPub federation coverage.

- `other-platforms/forgejo/00-forgejo-overview.md` through `other-platforms/forgejo/09-all-forgejo-reference.md`

#### other-platforms/codeberg/ - 10 files

Codeberg non-profit Git hosting coverage.

- `other-platforms/codeberg/00-codeberg-overview.md` through `other-platforms/codeberg/09-all-codeberg-reference.md`

#### ides-and-editors/ - 14 files

All major editors and AI IDEs: VS Code, JetBrains, Neovim, GitHub Desktop, Cursor, Windsurf, Antigravity, Kiro, Zed, Helix, Sublime Text, Emacs/Magit, Xcode and Android Studio.

- `ides-and-editors/00-ides-and-editors-overview.md` through `ides-and-editors/13-all-ides-reference.md`

#### Infrastructure

- markdownlint CI on every push (MD022, MD038 enforcement)
- Link checker CI (weekly schedule)
- GitHub issue templates: bug report and content suggestion
- Pull request template
- `.editorconfig` and `.markdownlint.json`
- Root meta files: `CHANGELOG.md`, `ROADMAP.md`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`, `SUPPORT.md`, `FAQ.md`, `HALL_OF_FAME.md`

---

[Unreleased]: https://github.com/zaccessss/git-unlocked/compare/v1.1.0...HEAD
[1.1.0]: https://github.com/zaccessss/git-unlocked/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/zaccessss/git-unlocked/releases/tag/v1.0.0
