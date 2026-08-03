# Frequently Asked Questions

**Difficulty:** 🟢 Beginner to 🔴 Advanced | **Time:** As needed - jump to your question

---

## Table of Contents

- [About this course](#about-this-course)
- [Git basics](#git-basics)
- [Installation and setup](#installation-and-setup)
- [Commands and workflow](#commands-and-workflow)
- [GitHub](#github)
- [GitLab](#gitlab)
- [Branching and merging](#branching-and-merging)
- [Undoing things](#undoing-things)
- [Security and credentials](#security-and-credentials)
- [IDEs and editors](#ides-and-editors)
- [Career and industry](#career-and-industry)
- [Common errors](#common-errors)
- [Git 3.0 and what is coming](#git-30-and-what-is-coming)

---

## About This Course

### How is the course structured?

The course is organised into 12 numbered sections, each covering a distinct topic. The numbering reflects the intended reading order for beginners, but experienced learners can jump to any section directly.

| Folder                   | What it covers                                                                 |
| ------------------------ | ------------------------------------------------------------------------------ |
| `00-welcome/`            | What this course is and how to get the most from it                            |
| `01-introduction/`       | What version control is, how to set up Git and how to navigate the course      |
| `02-git/`                | Everything Git - from `git init` through internals, hooks, worktrees and more  |
| `03-github/`             | Full GitHub platform - issues, Actions, Copilot, security, releases and more   |
| `04-gitlab/`             | Full GitLab platform - pipelines, merge requests, runners, self-hosting        |
| `05-other-platforms/`    | Bitbucket, Azure DevOps, Gitea, Forgejo and Codeberg                           |
| `06-ides-and-editors/`   | VS Code, JetBrains, Neovim, Cursor, Zed and more                               |
| `07-terminal/`           | Shell setup, lazygit, delta, fzf, bat, tig, gitui, starship and zoxide         |
| `08-real-world/`         | Open source contribution, team workflows, GitOps, monorepos, disaster recovery |
| `09-reference/`          | Cheatsheet, glossary, common mistakes, security reference                      |
| `10-resources/`          | 120+ curated books, videos, tools and communities                              |
| `11-first-contribution/` | A safe sandbox for making your very first open source pull request             |

Every content file includes a difficulty label (🟢 Beginner / 🟡 Intermediate / 🔴 Advanced) and covers Windows, Mac and Linux commands side by side.

### Where should I start if I already know basic Git?

Skip `01-introduction/` and go straight to whichever platform or topic you want to deepen. Good entry points for experienced users:

- **Strengthen Git fundamentals:** `02-git/12-git-workflows.md` through `02-git/25-git-internals-advanced.md`
- **Learn GitHub deeply:** start at `03-github/00-github-overview.md`
- **Terminal-first workflow:** start at `07-terminal/00-terminal-overview.md`
- **Real-world team practices:** `08-real-world/02-working-in-a-team.md`
- **Quick reference:** `09-reference/cheatsheet.md` has every Git command in one place

### Is the entire course free?

Yes. Every file, every section and all future updates are free under the [CC BY-SA 4.0 Licence](LICENSE). You can use, share, adapt and build on the material as long as you give credit and keep it under the same licence. There is no paid tier, no email gate and no premium content.

### How do I contribute to this course?

Read [CONTRIBUTING.md](CONTRIBUTING.md) for the full guide. The short version: fork the repo, create a branch, make your change following the writing style guide and open a pull request. Your name gets added to [HALL_OF_FAME.md](HALL_OF_FAME.md) once your first PR is merged.

If you have never opened a pull request before, start at [11-first-contribution/](11-first-contribution/README.md) - it is a sandbox designed for exactly that.

---

## Git Basics

### What is Git?

Git is a **distributed version control system** - software that tracks every change you make to your files over time. It lets you save snapshots of your work (called commits), return to any previous snapshot, work on multiple versions simultaneously (called branches) and collaborate with others without overwriting each other's work.

Git was created by **Linus Torvalds** in April 2005 - the same person who created Linux. He built it in approximately 10 days after the Linux kernel team lost access to their previous version control tool. It is free, open source and runs on every major operating system.

### What is the difference between Git and GitHub?

This is the most common misconception in all of version control.

**Git** is software installed on your computer. It runs locally and tracks changes to your files. It has nothing to do with the internet.

**GitHub** is a website and cloud platform where you can store your Git repositories online, share them with others and collaborate. GitHub launched in April 2008 - three years after Git was created.

You can use Git without GitHub entirely. GitHub is just one of many platforms that host Git repositories. GitLab and Bitbucket are others.

### Why should I learn Git?

- Approximately **94% of developers worldwide** use Git - one of the most universally adopted technologies in history
- **67% of software developer job listings** specifically mention Git or version control
- **90% of Fortune 100 companies** use GitHub
- **83% of technical hiring managers** view a GitHub profile as more reliable than a traditional CV
- Git is used beyond software development - in data science, DevOps, academic research, technical writing and government

Even if you work alone on personal projects, Git protects your work, lets you experiment safely and gives you a full history of everything you have ever built.

### Is Git free?

Yes. Git is completely free and open source, released under the GNU General Public Licence version 2.

GitHub is free for individuals with unlimited public and private repositories. Paid plans add team features, more Actions minutes and enterprise capabilities. GitLab has a free tier with 400 CI/CD minutes per month.

### What is version control?

Version control is a system that records changes to files over time so you can recall specific versions later. Think of it like a very detailed save history for your entire project - not just the latest version, but every version you have ever saved, who saved it and when.

There are three main types. **Local VCS** tracks changes on a single computer - simple but no collaboration and a complete single point of failure. **Centralised VCS** (such as SVN) uses a single shared server - if the server fails, everything is at risk. **Distributed VCS** (such as Git) gives every developer a complete clone of the entire repository including full history - no single point of failure, fast local operations and flexible collaboration.

---

## Installation and Setup

### What version of Git should I install?

The current stable release is **Git 2.53.0** (February 2026). Always install the latest from [git-scm.com/downloads](https://git-scm.com/downloads).

On Windows: `winget install --id Git.Git -e --source winget`

### Why does Git say "Author identity unknown" when I commit?

You have not told Git who you are. Run these two commands one at a time:

```bash
git config --global user.name "Your Name"
```

```bash
git config --global user.email "your@email.com"
```

Use the same email as your GitHub or GitLab account.

### What is the difference between `--global` and `--local` config?

`--global` applies to all repositories on your computer. `--local` applies only to the current repository. Use `--global` for your name, email and default branch. Use `--local` when you need different settings for a specific project - for example a work email on a work repo.

### My Git is installed but the terminal says command not found

**Windows** - Close and reopen your terminal. The PATH update only applies to new terminal sessions. If it still fails, reinstall using `winget install --id Git.Git -e --source winget`.

**Mac** - Run `xcode-select --install`. After macOS updates, the developer tools path often breaks.

**Linux** - Run `which git`. If empty, install with `sudo apt install git` (Ubuntu) or the equivalent for your distribution.

---

## Commands and Workflow

### What are the most important Git commands to learn first?

For day-to-day work, most developers use about 10 commands regularly:

| Command                   | What it does                                  |
| ------------------------- | --------------------------------------------- |
| `git init`                | Create a new repository in the current folder |
| `git clone URL`           | Download an existing repository               |
| `git status`              | See what has changed                          |
| `git add .`               | Stage all changes ready to commit             |
| `git commit -m "message"` | Save a snapshot with a message                |
| `git push`                | Upload your commits to GitHub or GitLab       |
| `git pull`                | Download the latest changes                   |
| `git log --oneline`       | See your commit history                       |
| `git switch branch-name`  | Switch to a different branch                  |
| `git branch`              | List all branches                             |

### What is `git switch` and should I use it instead of `git checkout`?

`git switch` was introduced in Git 2.23 (2019) to do one thing clearly - switch branches. `git checkout` does multiple things (switching branches, restoring files, detaching HEAD) which makes it confusing for beginners.

As of **Git 2.51 (August 2025), `git switch` and `git restore` are officially stable**. They are now the recommended commands for switching branches and restoring files. `git checkout` is _not_ deprecated and will not be removed - all three commands continue to work.

### What is the staging area and why does it exist?

The staging area (also called the index) is a preparation zone between your working directory and your commits. You choose exactly which changes to include in a commit using `git add` before running `git commit`.

This gives you precise control. If you changed three files but only two are ready, you can stage and commit just those two. The third stays unstaged for the next commit.

Think of it like packing a box: your working directory is the room full of things, staging is putting selected items in the box and committing is sealing and labelling the box.

### What makes a good commit message?

A good commit message answers "what does this commit do?" in under 72 characters. Be specific.

**Poor:** `update stuff`, `fix`, `changes`, `WIP`

**Good:** `fix: resolve null pointer on user login`, `add: dark mode toggle to settings page`

This course follows the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) format: `type: description`. Common types are `add`, `fix`, `update`, `remove` and `rename`.

### How often should I commit?

Commit every time you complete a small, logical unit of work. Small, frequent commits make it easier to understand history, easier to revert if something breaks and easier to review in pull requests.

A rough rule: if you would feel anxious losing the last 30 minutes of work, commit now.

---

## GitHub

### What is GitHub?

GitHub is the world's largest platform for hosting Git repositories and collaborating on code. Founded in **April 2008** by Tom Preston-Werner, Chris Wanstrath, P.J. Hyett and Scott Chacon, it was acquired by **Microsoft in June 2018** for $7.5 billion.

GitHub has **180 million developers** across **630 million repositories** as of 2025. A new developer joins every second. **90% of Fortune 100 companies** use GitHub.

### What is the difference between GitHub Free and GitHub Pro?

**GitHub Free** includes unlimited public and private repositories, GitHub Actions (2,000 minutes per month), GitHub Pages, Discussions, Issues, basic security features and a Copilot free tier (2,000 completions and 50 chat requests per month).

**GitHub Pro** ($4/month) adds more Actions minutes, larger file storage, GitHub Advanced Insights and priority support. Most individual developers do not need it.

### What is GitHub Copilot and is it free?

GitHub Copilot is an AI coding assistant. It has a **free tier** with 2,000 completions and 50 chat requests per month - available to all GitHub users at no cost. Copilot Pro is $10/month. Verified students get Copilot Pro free through the GitHub Student Developer Pack.

### What is the GitHub Student Developer Pack?

A free pack of tools for verified students - worth approximately $45,000 in total value. It includes GitHub Copilot Pro, JetBrains IDEs, domain names, cloud credits and over 100 other offers.

Apply at [education.github.com](https://education.github.com) with a school email or student ID. Approval usually takes 1 to 3 days.

### How do I push my first project to GitHub?

1. Create a repository on GitHub (click the `+` button, then **New repository**)
2. Do not initialise it with a README if you already have local files
3. In your terminal, inside your project folder, run these commands one at a time:

```bash
git init
```

```bash
git add .
```

```bash
git commit -m "initial commit"
```

```bash
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
```

```bash
git branch -M main
```

```bash
git push -u origin main
```

### What is a pull request?

A pull request (PR) is a proposal to merge changes from one branch into another. You work on a branch, push it to GitHub, then open a PR to ask someone to review and merge your changes. Pull requests are where code review happens - comments, suggestions, approvals and ultimately merging.

On GitLab the same concept is called a **merge request** (MR).

### What are GitHub badges and achievements?

GitHub badges appear on your profile when you do specific things. Current badges include **Pull Shark** (opening merged pull requests), **YOLO** (merging a PR without review), **Quickdraw** (closing an issue or PR within 5 minutes of opening), **Pair Extraordinaire** (co-authoring merged commits), **Galaxy Brain** (getting an accepted answer on a Discussion) and **Starstruck** (having a repository receive 16 or more stars). Some badges have bronze, silver and gold tiers.

---

## GitLab

### What is GitLab?

GitLab is a complete DevSecOps platform that includes Git hosting, CI/CD pipelines, container registry, security scanning, issue tracking and more - all in one application. The current version is **GitLab 18.10** (March 2026).

GitLab was founded in **2011** by Dmitriy Zaporozhets and Sid Sijbrandij. It is available as a cloud service at gitlab.com or as a self-hosted Community Edition (free and open source under the MIT licence).

### What is the difference between GitHub and GitLab?

| Feature            | GitHub                                     | GitLab                                         |
| ------------------ | ------------------------------------------ | ---------------------------------------------- |
| Best for           | Open source, broad collaboration           | Enterprise, regulated industries, self-hosting |
| CI/CD              | GitHub Actions - modular, huge marketplace | Built-in pipelines - more deeply integrated    |
| Free CI/CD minutes | 2,000/month                                | 400/month                                      |
| Self-hosting       | Enterprise Server only (paid)              | Community Edition (free and open source)       |
| Security scanning  | Dependabot, CodeQL, secret scanning        | Built-in SAST, DAST, secret detection          |
| AI                 | GitHub Copilot                             | GitLab Duo                                     |

### Does GitLab have a free tier?

Yes. GitLab Free includes unlimited private repositories, 400 CI/CD minutes per month, the container registry, GitLab Pages, basic SAST and secret detection and up to 5 users per private group.

### Can I self-host GitLab?

Yes. GitLab Community Edition is free and open source. You can run it on your own server using Linux packages, Docker or Kubernetes. Minimum requirements are 4 CPU cores and 4 GB RAM. This is why regulated industries - defence, finance, healthcare, government - often choose GitLab over GitHub.

### What is GitLab CI/CD?

GitLab CI/CD is a built-in system for automating testing, building and deploying your code. You define your pipeline in a `.gitlab-ci.yml` file at the root of your repository. Every time you push code, GitLab runs your pipeline automatically. Full coverage is in [04-gitlab/06-gitlab-ci-cd.md](04-gitlab/06-gitlab-ci-cd.md).

---

## Branching and Merging

### What is a branch?

A branch is a separate line of development. Technically it is just a lightweight pointer (40 bytes) to a specific commit - creating a branch is nearly instantaneous and uses almost no disk space.

Branches let you work on a new feature or fix without touching the main codebase. When you are done, you merge your branch back in. If something goes wrong you can delete the branch without affecting anything else.

### What is the difference between merge and rebase?

**Merge** creates a new "merge commit" that joins two branches together. It preserves the exact history of when things happened. It is safe, non-destructive and recommended for beginners and shared branches.

**Rebase** moves your commits on top of another branch, rewriting them as if they happened after the latest commit on that branch. It creates a cleaner, linear history but rewrites commit SHAs. Never rebase branches that other people are using.

### What is a merge conflict and how do I fix one?

A merge conflict happens when two branches changed the same lines of the same file differently, and Git does not know which version to keep. Git pauses the merge and marks the conflicting sections:

```
<<<<<<< HEAD
this is the version from your current branch
=======
this is the version from the branch being merged
>>>>>>> feature-branch
```

To fix it: open the file, choose which version to keep (or combine them), delete the `<<<<<<<`, `=======` and `>>>>>>>` markers, save the file, then run `git add` followed by `git commit`.

Most IDEs - VS Code, JetBrains, Visual Studio - have a visual merge conflict editor that makes this much easier than editing raw text.

---

## Undoing Things

### How do I undo my last commit?

This is the most searched Git question on Stack Overflow with over 14 million views.

**Undo the commit but keep the changes in your files (most common):**

```bash
git reset --soft HEAD~1
```

**Undo the commit and unstage the changes but keep the files:**

```bash
git reset HEAD~1
```

**Undo the commit and delete the changes completely:**

```bash
git reset --hard HEAD~1
```

> [!CAUTION]
> `--hard` permanently deletes your changes. Only use it if you are absolutely certain you do not need them.

### How do I undo a commit that I already pushed?

If you have already pushed to a shared branch, use `git revert`. This creates a new commit that undoes the changes without rewriting history:

```bash
git revert HEAD
```

This is safe to push because it adds a new commit rather than changing existing ones.

> [!WARNING]
> Do not force-push a rewritten branch to a shared remote unless you have coordinated with your team. It will cause serious problems for anyone who has already pulled that branch.

### I accidentally committed a file I should not have

If you have not pushed yet:

```bash
git reset HEAD~1
```

Then add the file to `.gitignore` before committing again.

If you have already pushed and the file contains sensitive data (a password, API key or token), see the security section below - removing it requires `git filter-repo` and you must also **revoke and rotate the compromised credential immediately**.

---

## Security and Credentials

### I accidentally pushed my API key or password to GitHub. What do I do?

**Act immediately - in this order:**

1. **Revoke the compromised credential right now.** Go to whatever service the key belongs to and invalidate it. Bots scan GitHub continuously and will use exposed keys within minutes of a push to a public repository.
2. **Generate a new credential** on that service.
3. **Remove the secret from your repository history** using `git filter-repo`.
4. **Force-push** to overwrite the remote history.
5. **Contact GitHub support** if the repository is public - they can help accelerate the cleanup.

Removing the file from the current commit is not enough. The secret remains visible in the git history to anyone who clones or has cloned the repo.

> [!NOTE]
> GitHub's secret scanning automatically detects common token patterns in public repositories and alerts the service provider. As of March 2026 it covers 39+ token types. This is helpful but you should not rely on it - prevent exposure in the first place.

### How do I remove a sensitive file from Git history?

Use **git-filter-repo** - the officially recommended tool, endorsed by Git's own documentation:

```bash
pip install git-filter-repo
```

```bash
git filter-repo --path secretfile.env --invert-paths
```

Then force-push all branches:

```bash
git push origin --force --all
```

> [!WARNING]
> This rewrites history. Anyone who has cloned the repo will have diverged history and will need to re-clone. Coordinate with your team before doing this.

### How do I prevent committing secrets in the first place?

Three layers of prevention:

1. **Add `.env` to `.gitignore` before your first commit.** Never commit `.env`, `*.pem`, `*.key` or any file containing credentials.
2. **Use a pre-commit scanning tool** like TruffleHog (800+ secret types, open source) or GitHub's built-in push protection (free for public repos).
3. **Never hardcode credentials in your code.** Use environment variables or a secrets manager.

### Should I use SSH or HTTPS for authentication?

Both work. SSH is more convenient for regular use once set up - no passwords or tokens to manage. HTTPS is slightly easier for beginners.

For SSH, use an **Ed25519 key** - the current best practice. It is faster and more secure than the older RSA type.

For HTTPS, use a **fine-grained Personal Access Token** (not a classic token and never your account password, which has not worked for Git operations since August 2021).

> [!TIP]
> The easiest option for beginners: install the GitHub CLI and run `gh auth login`. It handles everything through your browser and stores credentials securely.

---

## IDEs and Editors

### Do I need to use the terminal, or can I use Git entirely through VS Code?

You do not need the terminal once you are set up. VS Code's Source Control panel (`Ctrl + Shift + G` / `Cmd + Shift + G`) handles staging, committing, pushing, pulling, branch switching and merge conflicts visually.

However, learning terminal commands first is strongly recommended. It teaches you what is actually happening, and you will encounter situations where the terminal is the only option. This course covers both.

### What is the best VS Code extension for Git?

**GitLens** by GitKraken (46 million+ downloads) is the most popular. The free tier shows who wrote each line, lets you explore commit history and navigate the repository visually.

**GitHub Pull Requests** by GitHub (31 million+ downloads) lets you review and manage pull requests inside VS Code.

> [!NOTE]
> **Git Graph** (13 million+ downloads) still works but is no longer actively maintained. VS Code's built-in **Source Control Graph** (introduced in version 1.93) provides visual branch history natively and is the recommended replacement.

### Which JetBrains IDE should I use?

| Language                 | IDE                                       |
| ------------------------ | ----------------------------------------- |
| Java or Kotlin           | IntelliJ IDEA (Community edition is free) |
| Python                   | PyCharm (Community edition is free)       |
| JavaScript or TypeScript | WebStorm                                  |
| C or C++                 | CLion                                     |
| C# or .NET               | Rider                                     |
| Go                       | GoLand                                    |

All JetBrains IDEs share the same Git integration. If you are unsure, IntelliJ IDEA Community edition is a safe starting point.

---

## Career and Industry

### Is a GitHub profile important for getting a job?

It helps significantly at tech-focused companies. **83% of technical hiring managers** view GitHub profiles as more reliable than traditional CVs. Recruiters look for consistent contribution activity, clear READMEs, meaningful commit messages, code quality and open source involvement.

That said, an empty GitHub profile will not necessarily disqualify you - many excellent developers work primarily on private company repositories. It matters most at startups and tech-first companies.

### Does Git matter for fields outside software development?

Yes, significantly.

- **Data science** - tracking Jupyter notebooks, ML models, data pipelines and experiment results
- **DevOps and cloud** - GitOps (using Git as the source of truth for infrastructure) is used by over 64% of organisations as of 2025
- **Academic research** - Git enables reproducible research and is taught at universities worldwide including Harvard and MIT
- **Technical writing** - documentation teams use Git branching and pull requests for collaborative review
- **Government** - the US federal government requires agencies to publish at least 20% of custom code as open source; France has published its legal code on GitHub

---

## Common Errors

### `fatal: not a git repository`

You are trying to run a Git command in a folder with no repository. Either run `git init` to create one here, or `cd` into the correct project folder.

### `error: src refspec main does not match any`

You tried to push a branch called `main` but your local branch is still called `master`. Fix it:

```bash
git branch -M main
```

Then push again.

### `rejected - non-fast-forward`

Someone else pushed commits to the remote that you do not have locally. Pull first:

```bash
git pull
```

Resolve any conflicts, then push again.

### `CONFLICT - Automatic merge failed`

A merge conflict. Open the flagged files, find the `<<<<<<<` markers, choose which changes to keep, save and then:

```bash
git add .
```

```bash
git commit
```

### `Permission denied (publickey)`

Your SSH key is not set up correctly. Check that your key files exist (`ls ~/.ssh/` - look for `id_ed25519`), the agent is running (`eval "$(ssh-agent -s)"` then `ssh-add ~/.ssh/id_ed25519`) and your public key is added at https://github.com/settings/keys.

### `HEAD detached at abc1234`

You checked out a specific commit directly rather than a branch. To get back:

```bash
git switch main
```

Or create a new branch from here to save your work:

```bash
git switch -c my-new-branch
```

---

## Git 3.0 and What is Coming

### What is Git 3.0?

Git 3.0 has **not been released**. Developers are targeting **late 2026**. It will be the first intentionally backwards-incompatible version. Key planned changes:

- Default hash algorithm: SHA-1 → **SHA-256** for new repositories
- Default ref storage: files → **reftable** for new repositories
- Default branch name: `master` → **`main`** for new repositories
- **Rust becomes a mandatory build dependency**
- **Removals:** `git whatchanged`, `git pack-redundant`, graft support

> [!NOTE]
> SHA-256 repositories are not yet supported by GitHub, so the migration will not be seamless on day one. Setting `git config --global init.defaultBranch main` now already matches the new default - that part of the transition is already done.

### Should I worry about Git 3.0?

Not right now. Existing repositories will continue to work. The changes mostly affect new repositories created after upgrading. When Git 3.0 releases, this course will be updated with migration guidance.

---

## Sources and Further Reading

- [Official Git documentation](https://git-scm.com/doc) - the complete reference for all Git commands
- [Pro Git book](https://git-scm.com/book/en/v2) - the free official Git book, available online in multiple languages
- [GitHub documentation](https://docs.github.com) - everything about using GitHub
- [GitLab documentation](https://docs.gitlab.com) - everything about using GitLab
- [Git BreakingChanges documentation](https://git-scm.com/docs/BreakingChanges) - the official list of what will change in Git 3.0
- [Oh Shit, Git!?!](https://ohshitgit.com) - practical fixes for common Git disasters, also at dangitgit.com
- [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) - the standard for structured commit messages
- [GitHub Octoverse 2025](https://github.blog/news-insights/octoverse/) - the annual report on developer trends
- [Git turns 20: A Q&A with Linus Torvalds](https://github.blog/open-source/git/git-turns-20-a-qa-with-linus-torvalds/) - Linus Torvalds on Git's creation (GitHub Blog, 2025)
- [TruffleHog](https://github.com/trufflesecurity/trufflehog) - open source secret scanning tool
- [git-filter-repo](https://github.com/newren/git-filter-repo) - the recommended tool for rewriting Git history

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
