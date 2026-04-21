# git-unlocked

> The most complete open source Git and version control course available. Free forever. MIT licensed.

[![markdownlint](https://github.com/zaccessss/git-unlocked/actions/workflows/markdownlint.yml/badge.svg)](https://github.com/zaccessss/git-unlocked/actions/workflows/markdownlint.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-1.2.0-blue.svg)](CHANGELOG.md)

git-unlocked takes you from absolute zero to professional-level Git across every major platform - GitHub, GitLab, Bitbucket, Azure DevOps, Gitea, Forgejo and Codeberg. Every file covers Windows, Mac and Linux side by side. Nothing assumed. Nothing skipped.

**202 files. 15 sections. All free.**

---

## Start here

New to Git? Never heard of version control? Start at the beginning:

1. [What is version control?](introduction/04-version-control-concepts.md)
2. [How to use this course](introduction/02-how-to-use-this-course.md)
3. [Setting up your environment](introduction/03-setting-up.md)

Want to make your first contribution right now? Go to [first-contribution/](first-contribution/README.md).

---

## Course contents

### Introduction

| File | Topics |
|---|---|
| [01-welcome.md](introduction/01-welcome.md) | What this course is and who it is for |
| [02-how-to-use-this-course.md](introduction/02-how-to-use-this-course.md) | How to navigate, difficulty levels, OS coverage |
| [03-setting-up.md](introduction/03-setting-up.md) | Install Git, configure your identity, choose a terminal |
| [04-version-control-concepts.md](introduction/04-version-control-concepts.md) | What version control is, why it exists, how Git fits in |

### Git

29 files covering everything from `git init` to internals and packfiles.

| # | File | Topics | Level |
|---|---|---|---|
| 00 | [git-overview.md](git/00-git-overview.md) | Section map and reading order | 🟢 |
| 01 | [what-is-version-control.md](git/01-what-is-version-control.md) | Version control history and Git's place in it | 🟢 |
| 02 | [installing-git.md](git/02-installing-git.md) | Install Git on Windows, Mac and Linux | 🟢 |
| 03 | [how-git-works.md](git/03-how-git-works.md) | The object model: blobs, trees, commits, refs | 🟢 |
| 04 | [core-concepts.md](git/04-core-concepts.md) | Repository, working tree, staging area, HEAD | 🟢 |
| 05 | [basic-commands.md](git/05-basic-commands.md) | init, clone, add, commit, status, log, diff | 🟢 |
| 06 | [branching.md](git/06-branching.md) | Creating, switching and deleting branches | 🟢 |
| 07 | [merging.md](git/07-merging.md) | Fast-forward, merge commits, strategies | 🟡 |
| 08 | [merge-conflicts.md](git/08-merge-conflicts.md) | Resolving conflicts step by step | 🟡 |
| 09 | [remote-repos.md](git/09-remote-repos.md) | clone, fetch, pull, push, remotes | 🟢 |
| 10 | [gitignore.md](git/10-gitignore.md) | Ignoring files and global gitignore | 🟢 |
| 11 | [stash.md](git/11-stash.md) | Saving work without committing | 🟡 |
| 12 | [git-workflows.md](git/12-git-workflows.md) | GitFlow, GitHub Flow, trunk-based development | 🟡 |
| 13 | [rebase.md](git/13-rebase.md) | Rebase vs merge, interactive rebase | 🔴 |
| 14 | [cherry-pick.md](git/14-cherry-pick.md) | Applying specific commits across branches | 🔴 |
| 15 | [reset-and-revert.md](git/15-reset-and-revert.md) | Undoing changes safely | 🔴 |
| 16 | [tags-and-releases.md](git/16-tags-and-releases.md) | Tagging versions and creating releases | 🟡 |
| 17 | [submodules.md](git/17-submodules.md) | Embedding repositories inside repositories | 🔴 |
| 18 | [hooks.md](git/18-hooks.md) | Automating actions with Git hooks | 🔴 |
| 19 | [bisect.md](git/19-bisect.md) | Binary search to find a bug-introducing commit | 🔴 |
| 20 | [signing-commits.md](git/20-signing-commits.md) | GPG and SSH commit signing | 🔴 |
| 21 | [advanced-config.md](git/21-advanced-config.md) | Aliases, conditional includes, advanced config | 🔴 |
| 22 | [large-repos.md](git/22-large-repos.md) | Git LFS, sparse checkout, partial clone | 🔴 |
| 23 | [monorepos.md](git/23-monorepos.md) | Managing large multi-package repositories | 🔴 |
| 24 | [gitops.md](git/24-gitops.md) | Git as the source of truth for infrastructure | 🔴 |
| 25 | [git-internals-advanced.md](git/25-git-internals-advanced.md) | Packfiles, reflog, garbage collection | 🔴 |
| 26 | [gitattributes.md](git/26-gitattributes.md) | Line endings, diff drivers, export-ignore | 🟡 |
| 27 | [git-worktree.md](git/27-git-worktree.md) | Multiple working trees from one repository | 🔴 |
| 28 | [all-git-commands.md](git/28-all-git-commands.md) | Complete command reference | 🟢🟡🔴 |

### GitHub

28 files covering the full GitHub platform.

| # | File | Topics | Level |
|---|---|---|---|
| 00 | [github-overview.md](github/00-github-overview.md) | Section map | 🟢 |
| 01 | [what-is-github.md](github/01-what-is-github.md) | GitHub vs Git, plans and pricing | 🟢 |
| 02 | [creating-an-account.md](github/02-creating-an-account.md) | Account setup, 2FA, SSH keys | 🟢 |
| 03 | [github-desktop.md](github/03-github-desktop.md) | Visual Git client for beginners | 🟢 |
| 04 | [repositories.md](github/04-repositories.md) | Creating, cloning, visibility, settings | 🟢 |
| 05 | [forking.md](github/05-forking.md) | Fork workflow for open source contribution | 🟢 |
| 06 | [editing-on-github.md](github/06-editing-on-github.md) | Web editor, github.dev, Codespaces | 🟢 |
| 07 | [issues.md](github/07-issues.md) | Issues, labels, milestones, templates | 🟢 |
| 08 | [pull-requests.md](github/08-pull-requests.md) | Creating, reviewing and merging PRs | 🟢 |
| 09 | [branch-protection-and-rulesets.md](github/09-branch-protection-and-rulesets.md) | Rulesets, CODEOWNERS, merge queues | 🟡 |
| 10 | [github-actions.md](github/10-github-actions.md) | CI/CD workflows, runners, marketplace | 🟡 |
| 11 | [github-pages.md](github/11-github-pages.md) | Static site hosting from a repository | 🟢 |
| 12 | [discussions.md](github/12-discussions.md) | Community Q&A and announcements | 🟢 |
| 13 | [projects-and-boards.md](github/13-projects-and-boards.md) | Project boards, roadmaps, automation | 🟡 |
| 14 | [github-releases.md](github/14-github-releases.md) | Releases, assets, changelogs | 🟡 |
| 15 | [github-packages.md](github/15-github-packages.md) | Container and package registry | 🟡 |
| 16 | [security-features.md](github/16-security-features.md) | Dependabot, secret scanning, CodeQL | 🟡 |
| 17 | [profile-and-readme.md](github/17-profile-and-readme.md) | Profile README, pinned repos, stats | 🟢 |
| 18-27 | Advanced topics | Copilot, Education, CLI, Mobile, Orgs, Advanced, Security, Platform comparison, All features | 🟡🔴 |

### GitLab

16 files covering the full GitLab platform.

| # | File | Topics | Level |
|---|---|---|---|
| 00 | [gitlab-overview.md](gitlab/00-gitlab-overview.md) | Section map | 🟢 |
| 01-15 | Core through reference | Account setup, GitLab vs GitHub, projects, MRs, CI/CD, Pages, issues, security, Duo AI, self-hosting, CLI, groups, API, all features | 🟢🟡🔴 |

### Other platforms

62 files across 5 subfolders.

| Platform | Files | Topics |
|---|---|---|
| [bitbucket/](other-platforms/bitbucket/) | 14 | Bitbucket Cloud and Data Center |
| [azure-devops/](other-platforms/azure-devops/) | 15 | Boards, Repos, Pipelines, Test Plans, Artifacts |
| [gitea/](other-platforms/gitea/) | 13 | Self-hosted Git server |
| [forgejo/](other-platforms/forgejo/) | 10 | Forgejo fork with federation |
| [codeberg/](other-platforms/codeberg/) | 10 | Non-profit Git hosting |

### IDEs and editors

14 files covering every major editor.

| # | File | Editor | Level |
|---|---|---|---|
| 00 | [ides-overview.md](ides-and-editors/00-ides-overview.md) | Section map and how-to-choose guide | 🟢 |
| 01 | [vs-code.md](ides-and-editors/01-vs-code.md) | VS Code, GitLens, Copilot, GitHub/GitLab extensions | 🟢🟡 |
| 02 | [jetbrains.md](ides-and-editors/02-jetbrains.md) | IntelliJ, PyCharm, WebStorm, Rider, GoLand, CLion | 🟡 |
| 03 | [neovim.md](ides-and-editors/03-neovim.md) | fugitive.vim, gitsigns.nvim, telescope | 🔴 |
| 04 | [github-desktop.md](ides-and-editors/04-github-desktop.md) | GitHub Desktop visual client | 🟢 |
| 05 | [cursor.md](ides-and-editors/05-cursor.md) | Cursor AI editor | 🟡 |
| 06 | [windsurf.md](ides-and-editors/06-windsurf.md) | Windsurf, Cascade, Flows | 🟡 |
| 07 | [antigravity.md](ides-and-editors/07-antigravity.md) | Google Antigravity editor | 🟡 |
| 08 | [kiro.md](ides-and-editors/08-kiro.md) | AWS Kiro IDE | 🟡 |
| 09 | [zed.md](ides-and-editors/09-zed.md) | Zed editor, multibuffer | 🟡 |
| 10 | [helix.md](ides-and-editors/10-helix.md) | Helix modal editor | 🔴 |
| 11 | [sublime-text.md](ides-and-editors/11-sublime-text.md) | Sublime Text and Sublime Merge | 🟡 |
| 12 | [other-editors.md](ides-and-editors/12-other-editors.md) | Emacs/Magit, Visual Studio, Xcode, Android Studio, Eclipse, Nova, Vim, nano | 🟡🔴 |
| 13 | [all-ides-features-reference.md](ides-and-editors/13-all-ides-features-reference.md) | Consolidated reference | 🟢🟡🔴 |

### Terminal

14 files covering the full Git terminal toolchain.

| # | File | Topics | Level |
|---|---|---|---|
| 00 | [terminal-overview.md](terminal/00-terminal-overview.md) | Section map and tool list | 🟢 |
| 01 | [what-is-a-terminal.md](terminal/01-what-is-a-terminal.md) | Terminals, shells and the command line | 🟢 |
| 02 | [shells-and-platforms.md](terminal/02-shells-and-platforms.md) | Bash, Zsh, Fish, PowerShell | 🟢 |
| 03 | [terminal-and-git.md](terminal/03-terminal-and-git.md) | Day-to-day Git in the terminal | 🟢 |
| 04 | [terminal-safety.md](terminal/04-terminal-safety.md) | Credential hygiene, force-push guards | 🟡 |
| 05 | [git-config-in-depth.md](terminal/05-git-config-in-depth.md) | Global config, per-project config | 🟢 |
| 06 | [git-aliases.md](terminal/06-git-aliases.md) | Building a productive alias library | 🟡 |
| 07 | [delta.md](terminal/07-delta.md) | Syntax-highlighted diffs | 🟡 |
| 08 | [lazygit.md](terminal/08-lazygit.md) | Full-screen terminal UI | 🟡 |
| 09 | [fzf-and-git.md](terminal/09-fzf-and-git.md) | Fuzzy finder for branches and logs | 🟡 |
| 10 | [bat-and-tig.md](terminal/10-bat-and-tig.md) | Syntax viewer and history browser | 🟢 |
| 11 | [other-tools.md](terminal/11-other-tools.md) | gitui, starship, zoxide, forgit | 🟡 |
| 12 | [terminal-git-workflow.md](terminal/12-terminal-git-workflow.md) | All tools in a real workflow | 🔴 |
| 13 | [all-terminal-tools-reference.md](terminal/13-all-terminal-tools-reference.md) | Consolidated reference | 🟢🟡🔴 |

### Real-world

8 files covering practical professional scenarios.

| # | File | Topics | Level |
|---|---|---|---|
| 00 | [real-world-overview.md](real-world/00-real-world-overview.md) | Section map and reading order | 🟢 |
| 01 | [contributing-to-open-source.md](real-world/01-contributing-to-open-source.md) | Fork workflow, PRs, upstream sync, review | 🟢🟡 |
| 02 | [working-in-a-team.md](real-world/02-working-in-a-team.md) | Branching strategies, code review, merge queues | 🟡🔴 |
| 03 | [gitops-workflows.md](real-world/03-gitops-workflows.md) | ArgoCD, Flux, environment promotion | 🟡🔴 |
| 04 | [monorepo-patterns.md](real-world/04-monorepo-patterns.md) | Sparse checkout, Nx, Turborepo, Bazel | 🟡🔴 |
| 05 | [migrating-between-platforms.md](real-world/05-migrating-between-platforms.md) | Mirror clone, LFS, platform importers | 🟡🔴 |
| 06 | [recovering-from-disasters.md](real-world/06-recovering-from-disasters.md) | Reflog, lost commits, secrets, recovery | 🟢🟡🔴 |
| 07 | [all-real-world-reference.md](real-world/07-all-real-world-reference.md) | Consolidated quick-reference | 🟢🟡🔴 |

### Reference

| File | Topics | Level |
|---|---|---|
| [cheatsheet.md](reference/cheatsheet.md) | Every Git command organised by task | 🟢🟡🔴 |
| [glossary.md](reference/glossary.md) | Every term defined: blob, tree, HEAD, reflog, SLSA and more | 🟢🟡🔴 |
| [common-mistakes.md](reference/common-mistakes.md) | Most frequent errors with exact fix commands | 🟢🟡🔴 |
| [security.md](reference/security.md) | gitleaks, TruffleHog, signing, push protection, supply chain security | 🟡🔴 |

### Resources

| File | Topics |
|---|---|
| [index.md](resources/index.md) | 120+ curated resources: books, videos, interactive tools, podcasts, repos, communities, certifications |

### First contribution

| File | Topics |
|---|---|
| [README.md](first-contribution/README.md) | Step-by-step guide for your first open source PR |
| [contributors.md](first-contribution/contributors.md) | Add your name here |

---

## Difficulty levels

- 🟢 Beginner - no prior experience needed
- 🟡 Intermediate - comfortable with basic Git
- 🔴 Advanced - production depth, complex scenarios

OS-specific commands are labelled 🪟 Windows, 🍎 Mac and 🐧 Linux. When a command is identical on all three, it appears once without labels.

---

## Callout key

> [!NOTE]
> Something worth knowing.

> [!TIP]
> A helpful technique or shortcut.

> [!IMPORTANT]
> A pro tip that will level up your workflow.

> [!WARNING]
> Read this carefully before continuing.

> [!CAUTION]
> A security note - something that protects your credentials, data or accounts.

---

## Make your first contribution

The best way to learn Git is to use it. The [first-contribution/](first-contribution/README.md) folder is a safe sandbox for absolute beginners. Add your name to the contributors list and make your very first pull request. It takes less than ten minutes.

---

## Contributing

This course is open source and built for the community. If you want to fix a typo, improve an explanation, add a translation or suggest a new topic - you are welcome here.

Read [CONTRIBUTING.md](CONTRIBUTING.md) first and then open a pull request. Everyone who contributes gets listed in [HALL_OF_FAME.md](HALL_OF_FAME.md). Please read our [Code of Conduct](CODE_OF_CONDUCT.md) before participating.

---

## Licence

This course is licensed under the [MIT Licence](LICENSE). You are free to use, share, adapt and build on it - just give credit where it is due.

---

## Contributors

<a href="https://github.com/zaccessss/git-unlocked/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=zaccessss/git-unlocked" alt="Contributors" />
</a>

---

<div align="center">

<br />

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

<br />

If this course helped you, please star the repository. It helps others find it.

<br />

[![zacess.com](https://img.shields.io/badge/zacess.com-000000?style=for-the-badge)](https://zacess.com)
[![GitHub](https://img.shields.io/badge/github-zaccessss-181717?style=for-the-badge&logo=github)](https://github.com/zaccessss)

</div>
