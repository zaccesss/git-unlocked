# Git - Folder Overview

**Difficulty:** 🟢 Beginner | **Time:** 5 minutes

This folder contains everything about Git - the version control system itself. It covers every concept from absolute beginner to advanced professional level, on Windows, Mac and Linux throughout.

If you are brand new, start at file 01 and work forward. If you are looking for something specific, use the table below to jump straight to it.

---

## What is in this folder?

| File | Topic | Level |
|---|---|---|
| [01-what-is-version-control.md](01-what-is-version-control.md) | What version control is, its full history from 1972 and why Git won | 🟢 |
| [02-installing-git.md](02-installing-git.md) | Installing Git on Windows, Mac and Linux - all options covered | 🟢 |
| [03-how-git-works.md](03-how-git-works.md) | Snapshots vs diffs, the three areas, the object model, HEAD and branches | 🟢 |
| [04-core-concepts.md](04-core-concepts.md) | Repos, commits, branches, staging area, remotes, forks and merging | 🟢 |
| [05-basic-commands.md](05-basic-commands.md) | init, clone, status, add, commit, log, diff, push, pull, switch | 🟢 |
| [06-branching.md](06-branching.md) | Creating, switching, renaming, deleting and pushing branches | 🟡 |
| [07-merging.md](07-merging.md) | Fast-forward, three-way and squash merges | 🟡 |
| [08-merge-conflicts.md](08-merge-conflicts.md) | What conflicts are, how to resolve them in the terminal and every major IDE | 🟡 |
| [09-remote-repos.md](09-remote-repos.md) | origin, upstream, fetch, pull, push, tracking branches and the fork workflow | 🟡 |
| [10-gitignore.md](10-gitignore.md) | Ignoring files, security essentials and the global .gitignore | 🟡 |
| [11-stash.md](11-stash.md) | Saving work without committing and managing the stash stack | 🟡 |
| [12-git-workflows.md](12-git-workflows.md) | Centralised, feature branch, GitHub Flow, Gitflow and trunk-based development | 🟡 |
| [13-rebase.md](13-rebase.md) | Rebase vs merge, interactive rebase and the golden rule | 🔴 |
| [14-cherry-pick.md](14-cherry-pick.md) | Copying specific commits across branches | 🔴 |
| [15-reset-and-revert.md](15-reset-and-revert.md) | Undoing changes at every level safely | 🔴 |
| [16-tags-and-releases.md](16-tags-and-releases.md) | Annotated and signed tags, semantic versioning and GitHub/GitLab releases | 🔴 |
| [17-submodules.md](17-submodules.md) | Embedding one repository inside another and managing it | 🔴 |
| [18-hooks.md](18-hooks.md) | Automating actions with pre-commit, commit-msg, pre-push and other hooks | 🔴 |
| [19-bisect.md](19-bisect.md) | Binary search to find exactly which commit introduced a bug | 🔴 |
| [20-signing-commits.md](20-signing-commits.md) | GPG and SSH commit signing for verified, tamper-proof commits | 🔴 |
| [21-advanced-config.md](21-advanced-config.md) | Aliases, conditional includes, diff tools, performance and safety settings | 🔴 |
| [22-large-repos.md](22-large-repos.md) | Git LFS, sparse checkout, shallow and partial clones | 🔴 |
| [23-monorepos.md](23-monorepos.md) | Managing multiple packages in one repository with Turborepo, Nx and more | 🔴 |
| [24-gitops.md](24-gitops.md) | Git as the source of truth for infrastructure and deployments | 🔴 |
| [25-git-internals-advanced.md](25-git-internals-advanced.md) | Packfiles, reflog, garbage collection and the object model in depth | 🔴 |
| [26-gitattributes.md](26-gitattributes.md) | Controlling line endings, diffs, merges, exports and language stats per file type | 🟡 |
| [27-git-worktree.md](27-git-worktree.md) | Checking out multiple branches simultaneously in separate directories | 🔴 |
| [28-all-git-commands.md](28-all-git-commands.md) | Complete reference for every Git command with common options and examples | 🟢🟡🔴 |

---

## Where to start by level

### Never used Git before

Work through these in order. Do not skip any of them.

1. [01 - What is version control](01-what-is-version-control.md)
2. [02 - Installing Git](02-installing-git.md)
3. [03 - How Git works](03-how-git-works.md)
4. [04 - Core concepts](04-core-concepts.md)
5. [05 - Basic commands](05-basic-commands.md)

Then continue to the `github/` folder to push your first project online.

### Know the basics, want to go further

Start here:

- [06 - Branching](06-branching.md)
- [07 - Merging](07-merging.md)
- [08 - Merge conflicts](08-merge-conflicts.md)
- [09 - Remote repositories](09-remote-repos.md)
- [10 - .gitignore](10-gitignore.md)
- [11 - Stash](11-stash.md)
- [12 - Git workflows](12-git-workflows.md)

### Ready for advanced topics

- [13 - Rebase](13-rebase.md) - rewrite history cleanly
- [14 - Cherry-pick](14-cherry-pick.md) - grab specific commits
- [15 - Reset and revert](15-reset-and-revert.md) - undo anything safely
- [18 - Hooks](18-hooks.md) - automate your workflow
- [19 - Bisect](19-bisect.md) - find bugs with binary search
- [21 - Advanced config](21-advanced-config.md) - aliases and power settings
- [26 - .gitattributes](26-gitattributes.md) - control how Git handles your files
- [27 - Git worktree](27-git-worktree.md) - work on multiple branches at once

### Working in a professional or enterprise context

- [12 - Git workflows](12-git-workflows.md) - how teams use Git
- [20 - Signing commits](20-signing-commits.md) - verified commits
- [22 - Large repos](22-large-repos.md) - Git LFS, sparse checkout
- [23 - Monorepos](23-monorepos.md) - multi-package repositories
- [24 - GitOps](24-gitops.md) - Git-driven infrastructure
- [26 - .gitattributes](26-gitattributes.md) - line endings and cross-platform teams

### Just need a quick answer

- [28 - All Git commands](28-all-git-commands.md) - complete command reference
- [../reference/cheatsheet.md](../reference/cheatsheet.md) - one-page quick reference
- [../reference/common-mistakes.md](../reference/common-mistakes.md) - fixes for common problems
- [../FAQ.md](../FAQ.md) - frequently asked questions

---

## Note on operating system coverage

Every file in this folder shows commands for **Windows**, **Mac** and **Linux** wherever they differ. Where a command is identical across all three systems it is shown once without a label.

Windows instructions cover both **Command Prompt** and **PowerShell**. Mac instructions note **Apple Silicon (M1/M2/M3/M4/M5)** differences where relevant.

---

## Note on command style

This folder uses `git switch` for switching branches and `git restore` for discarding changes. These commands were introduced in Git 2.23 (2019) and became officially stable in Git 2.51. They are the current recommendations.

You will see `git checkout` used for both purposes in older tutorials. It still works and is covered where relevant, but `git switch` and `git restore` are clearer and are used throughout this course.

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
