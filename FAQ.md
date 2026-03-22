# ❓ Frequently Asked Questions

**Difficulty:** 🟢 Beginner to 🔴 Advanced
**Time to read:** As needed - jump to your question

Got a question? The answer is probably here. If it is not, open a [Discussion](https://github.com/zaccesss/git-unlocked/discussions) or an [Issue](https://github.com/zaccesss/git-unlocked/issues).

---

## 📋 Table of Contents

- [Git basics](#-git-basics)
- [Installation and setup](#-installation-and-setup)
- [Commands and workflow](#-commands-and-workflow)
- [GitHub](#-github)
- [GitLab](#-gitlab)
- [Branching and merging](#-branching-and-merging)
- [Undoing things](#-undoing-things)
- [Security and credentials](#-security-and-credentials)
- [IDEs and editors](#-ides-and-editors)
- [Career and industry](#-career-and-industry)
- [Common errors](#-common-errors)
- [Git 3.0 and what is coming](#-git-30-and-what-is-coming)

---

## 🔰 Git Basics

### What is Git?

Git is a **distributed version control system** - software that tracks every change you make to your files over time. It lets you save snapshots of your work (called commits), go back to any previous snapshot, work on multiple versions simultaneously (called branches) and collaborate with others without overwriting each other's work.

Git was created by **Linus Torvalds** in April 2005 - the same person who created Linux. He built it in roughly 10 days after the Linux kernel team lost access to their previous version control tool.

💡 "Git" is British slang for an unpleasant or contemptible person. Torvalds named it as a self-deprecating joke. Depending on your mood it also stands for "Global Information Tracker" or "Goddamn Idiotic Truckload of sh*t."

### What is the difference between Git and GitHub?

This is the most common misconception in all of version control.

**Git** is software installed on your computer. It runs locally and tracks changes to your files. It has nothing to do with the internet.

**GitHub** is a website and cloud platform where you can store your Git repositories online, share them with others and collaborate. GitHub did not exist until 2008 - three years after Git was created.

You can use Git without GitHub entirely. You can also use GitHub with other version control systems, though nobody really does.

**GitLab** is a different company and platform that does similar things to GitHub, with a stronger focus on built-in CI/CD and self-hosting for enterprise use.

### Why should I learn Git?

- Approximately **94% of developers worldwide** use Git - it is the most universally adopted developer technology in history
- **67% of software developer job listings** specifically mention Git or version control
- **90% of Fortune 100 companies** use GitHub
- Git is used beyond software development in data science, DevOps, academic research, technical writing and government
- GitHub profiles are reviewed by **83% of technical hiring managers** - a strong profile genuinely helps your career

Even if you work alone on personal projects, Git protects your work, lets you experiment safely and gives you a full history of everything you have ever built.

### Is Git free?

Yes. Git is completely free and open source, released under the GNU General Public Licence version 2.

GitHub is free for individuals with unlimited public and private repositories. Paid plans add team features, more Actions minutes and enterprise features. GitLab also has a generous free tier with 400 CI/CD minutes per month.

### What is version control?

Version control is a system that records changes to files over time so you can recall specific versions later. Think of it like a very detailed save history for your entire project - not just the latest version, but every version you have ever saved, who saved it and when.

The three main types are local (only on your computer - like RCS), centralised (one shared server - like SVN) and distributed (every developer has a full copy - like Git). Git is distributed, which means every clone of a repository is a complete backup of the entire history.

---

## ⚙️ Installation and Setup

### What version of Git should I install?

The current stable release is **Git 2.53.0** (February 2026). Always install the latest stable version from [git-scm.com/downloads](https://git-scm.com/downloads).

If you are on Windows and use `winget`, run: `winget install --id Git.Git -e --source winget`

💡 Git 3.0 is expected later in 2026. It will change the default branch name to `main` and switch to SHA-256 hashing. Setting `git config --global init.defaultBranch main` now future-proofs your setup.

### Why does Git say "Author identity unknown" when I commit?

You have not told Git who you are. Run these two commands in your terminal, one at a time:

```
git config --global user.name "Your Name"
```
```
git config --global user.email "your@email.com"
```

Use the same email as your GitHub or GitLab account.

### What is the difference between `--global` and `--local` config?

`--global` applies to all repositories on your computer. `--local` (or no flag) applies only to the current repository. Use `--global` for your name, email and default branch. Use `--local` only when you need different settings for a specific project - for example a work email on a work repo.

### What editor should I use for Git commit messages?

Set it in your terminal with:

```
git config --global core.editor "code --wait"
```

Replace `code --wait` with `nano` if you want a simpler terminal editor, or `notepad` on Windows if you prefer a GUI. If Git ever opens Vim unexpectedly and you want to escape, type `:q!` and press Enter.

### My Git is installed but the terminal says command not found or not recognized

🪟 **Windows:** Close and reopen your terminal. The PATH update only applies to new terminal sessions. If still failing, reinstall using `winget install --id Git.Git -e --source winget`.

🍎 **Mac:** Run `xcode-select --install`. After macOS updates, the developer tools path often breaks.

🐧 **Linux:** Run `which git`. If empty, install with `sudo apt install git` (Ubuntu) or the equivalent for your distribution.

---

## ⌨️ Commands and Workflow

### What are the most important Git commands to know first?

For day-to-day work you need roughly 10 commands. Start with these:

| Command | What it does |
|---|---|
| `git init` | Create a new repository in the current folder |
| `git clone URL` | Download an existing repository |
| `git status` | See what has changed |
| `git add .` | Stage all changes ready to commit |
| `git commit -m "message"` | Save a snapshot with a message |
| `git push` | Upload your commits to GitHub or GitLab |
| `git pull` | Download the latest changes |
| `git log --oneline` | See your commit history |
| `git switch branch-name` | Switch to a different branch |
| `git branch` | List all branches |

### What is `git switch` and should I use it instead of `git checkout`?

`git switch` was introduced in Git 2.23 (2019) to do one thing clearly - switch branches. `git checkout` does multiple things (switching branches, restoring files, detaching HEAD) which makes it confusing for beginners.

As of **Git 2.51 (August 2025), `git switch` and `git restore` are officially stable** - no longer experimental. They are now the recommended way to switch branches and restore files. `git checkout` is NOT deprecated and will not be removed - all three commands will continue to work.

Use `git switch` for new work. The full course uses both, always explaining what each does.

### What is the staging area and why does it exist?

The staging area (also called the index) is a preparation zone between your working directory and your commits. You choose exactly which changes to include in a commit using `git add` before running `git commit`.

This gives you precise control. If you changed three files but only two are ready, you can stage and commit just those two. The third stays unstaged for the next commit.

Think of it like packing a box: your working directory is the room full of stuff, staging is putting things in the box, and committing is sealing and labelling the box.

### What makes a good commit message?

A good commit message answers the question "what does this commit do?" in under 72 characters. Be specific.

❌ Bad: `update stuff`, `fix`, `changes`, `WIP`
✅ Good: `fix: resolve null pointer on user login`, `add: dark mode toggle to settings page`

This course follows the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) format: `type: description`. Common types are `add`, `fix`, `update`, `remove` and `rename`.

### How often should I commit?

Commit every time you complete a small, logical unit of work. Do not wait until a feature is entirely finished to commit. Small, frequent commits make it easier to understand history, easier to revert if something breaks and easier to review in pull requests.

A rough rule: if you would feel anxious losing the last 30 minutes of work, commit now.

---

## 🐙 GitHub

### What is GitHub?

GitHub is the world's largest platform for hosting Git repositories and collaborating on code. Founded in **April 2008** by Tom Preston-Werner, Chris Wanstrath, P.J. Hyett and Scott Chacon, it was acquired by **Microsoft in June 2018** for $7.5 billion.

GitHub has **180 million developers** across **630 million repositories** as of 2025. A new developer joins every second. **90% of Fortune 100 companies** use GitHub.

### What is the difference between GitHub Free and GitHub Pro?

GitHub Free includes unlimited public and private repositories, GitHub Actions (2,000 minutes per month), GitHub Pages, Discussions, Issues, basic security features and a Copilot free tier (2,000 completions and 50 chat requests per month).

GitHub Pro (currently $4 per month) adds more Actions minutes, larger file storage, GitHub Advanced Insights and priority support. Most individual developers do not need Pro.

### What is GitHub Copilot and is it free?

GitHub Copilot is an AI coding assistant that suggests code as you type. It has a **free tier** (as of December 2024) with 2,000 completions and 50 chat requests per month, available to all GitHub users at no cost.

Copilot Pro is $10/month for 300 premium requests. Copilot Pro+ is $39/month. Verified students get Copilot Pro free through the GitHub Student Developer Pack.

### What is the GitHub Student Developer Pack?

A free pack of tools and services for verified students - worth approximately $45,000 in total value. It includes GitHub Copilot Pro, free access to JetBrains IDEs, domain names, cloud credits and over 100 other offers.

Apply at [education.github.com](https://education.github.com) with a school email or student ID. Approval usually takes 1-3 days.

### How do I push my first project to GitHub?

1. Create a repository on GitHub (click the `+` button, choose New repository)
2. Do not initialise it with a README if you already have local files
3. In your terminal, inside your project folder, run these commands one at a time:

```
git init
```
```
git add .
```
```
git commit -m "initial commit"
```
```
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
```
```
git branch -M main
```
```
git push -u origin main
```

### What is a pull request?

A pull request (PR) is a way of proposing changes. You work on a branch, push it to GitHub, then open a PR to ask someone to review and merge your changes into the main branch. Pull requests are where code review happens - comments, suggestions, approvals and ultimately merging.

On GitLab the same thing is called a **merge request** (MR). Same concept, different name.

### What are GitHub badges and achievements?

GitHub badges are little icons that appear on your profile for doing specific things. Current badges include Pull Shark (opening merged pull requests), YOLO (merging a PR without review), Quickdraw (closing an issue or PR within 5 minutes of opening it), Pair Extraordinaire (co-authoring merged commits), Galaxy Brain (getting an accepted answer on a Discussion) and Starstruck (having a repository receive 16 or more stars). Some badges have tiers - bronze, silver and gold based on how many times you earn them.

---

## 🦊 GitLab

### What is GitLab?

GitLab is a complete DevSecOps platform that includes Git hosting, CI/CD pipelines, container registry, security scanning, issue tracking and more - all in one application. The current version is **GitLab 18.10** (March 2026).

GitLab was founded in **2011** by Dmitriy Zaporozhets and Sid Sijbrandij in Ukraine and the Netherlands. It is available as a cloud service at gitlab.com or as a self-hosted Community Edition (free and open source under MIT licence).

### What is the difference between GitHub and GitLab?

Both host Git repositories and support collaboration. The main differences:

| Feature | GitHub | GitLab |
|---|---|---|
| Best for | Open source, broad collaboration | Enterprise, regulated industries, self-hosting |
| CI/CD | GitHub Actions (modular, marketplace) | Built-in pipelines (more integrated) |
| Free CI/CD minutes | 2,000/month | 400/month |
| Self-hosting | Enterprise Server only (paid) | Community Edition (free and open source) |
| Security scanning | Dependabot, CodeQL, secret scanning | Built-in SAST, DAST, secret detection |
| AI assistant | GitHub Copilot (mature, widely adopted) | GitLab Duo (newer, integrated into DevSecOps) |
| Free private repos | Unlimited | Unlimited |

### Does GitLab have a free tier?

Yes. GitLab Free includes unlimited private repositories, 400 CI/CD minutes per month on shared runners, the container registry, GitLab Pages, basic SAST and secret detection (as pipeline artifacts), and up to 5 users per private group.

### Can I self-host GitLab?

Yes. GitLab Community Edition is free and open source under the MIT licence. You can run it on your own server using Linux packages (Omnibus), Docker or Kubernetes. Minimum requirements are 4 CPU cores and 4 GB RAM (8 GB recommended). This is the main reason regulated industries - defence, finance, healthcare, government - choose GitLab over GitHub.

### What is GitLab CI/CD?

GitLab CI/CD is a built-in system for automating testing, building and deploying your code. You define your pipeline in a file called `.gitlab-ci.yml` at the root of your repository. Every time you push code, GitLab runs your pipeline automatically. Full coverage is in [gitlab/06-gitlab-ci-cd.md](gitlab/06-gitlab-ci-cd.md).

---

## 🌿 Branching and Merging

### What is a branch?

A branch is a separate line of development. Technically it is just a lightweight pointer (40 bytes) to a specific commit - creating a branch is nearly instantaneous and uses almost no disk space.

Branches let you work on a new feature or fix without touching the main codebase. When you are done, you merge your branch back in. If something goes wrong you can delete the branch without affecting anything else.

### What is the difference between merge and rebase?

Both merge and rebase are ways of combining work from different branches.

**Merge** creates a new "merge commit" that joins two branches together. It preserves the exact history of when things happened. It is safe and non-destructive. Recommended for beginners and shared branches.

**Rebase** moves your commits on top of another branch, rewriting them as if they happened after the latest commit on that branch. It creates a cleaner, linear history but rewrites commit SHAs. Never rebase branches that other people are using.

### What is a merge conflict and how do I fix one?

A merge conflict happens when two branches changed the same lines of the same file differently, and Git does not know which version to keep. Git pauses the merge and marks the conflicting sections in the file like this:

```
<<<<<<< HEAD
this is the version from your current branch
=======
this is the version from the branch being merged
>>>>>>> feature-branch
```

To fix it: open the file, choose which version to keep (or combine them), delete the `<<<<<<<`, `=======` and `>>>>>>>` markers, save the file and run `git add` followed by `git commit`.

Most IDEs - VS Code, JetBrains, Visual Studio - have a visual merge conflict editor that makes this much easier than editing raw text.

---

## ↩️ Undoing Things

### How do I undo my last commit?

This is the most searched Git question on Stack Overflow with over 14 million views.

**If you want to undo the commit but keep the changes in your files (most common):**
```
git reset --soft HEAD~1
```

**If you want to undo the commit and unstage the changes but keep the files:**
```
git reset HEAD~1
```

**If you want to undo the commit and delete the changes completely (destructive - cannot be undone):**
```
git reset --hard HEAD~1
```

⚠️ Only use `--hard` if you are absolutely sure you do not need those changes.

### How do I undo a commit that I already pushed?

If you have already pushed to a shared branch, the safest approach is `git revert`. This creates a new commit that undoes the changes without rewriting history:

```
git revert HEAD
```

This is safe to push because it does not change existing commits - it adds a new one.

⚠️ Do not force-push a rewritten branch to a shared remote unless you have coordinated with your team. It will cause serious problems for anyone else who has pulled that branch.

### I accidentally committed a file I should not have. How do I remove it?

If you have not pushed yet:
```
git reset HEAD~1
```

Then add the file to `.gitignore` before committing again.

If you have already pushed and the file contains sensitive data (like a password or API key), see the security section below. Removing it from the history requires `git filter-repo` and force-pushing - and you must also **revoke and rotate the compromised credential immediately**.

---

## 🔒 Security and Credentials

### I accidentally pushed my API key or password to GitHub. What do I do?

**Do this immediately - in this order:**

1. **Revoke the compromised credential right now.** Go to whatever service the key belongs to and invalidate it. This is urgent - bots scan GitHub for secrets continuously and will use your key within minutes of it being public.
2. **Generate a new credential** on that service.
3. **Remove the secret from your repository history** using `git filter-repo` (see below).
4. **Force-push** to overwrite the remote history.
5. **Contact GitHub support** - they can help if the repo is public.

The file being removed from the current commit is not enough. The secret will still be visible in the git history to anyone who clones or has cloned the repo.

💡 GitHub's secret scanning automatically detects common token patterns (covering 39+ token types as of March 2026) in public repositories and alerts the service provider. GitHub Copilot can also detect generic passwords with AI. This is helpful but you should not rely on it - prevent commits first.

### How do I remove a sensitive file from Git history?

Use `git-filter-repo` - the officially recommended tool (endorsed by Git's own documentation):

Install it:
```
pip install git-filter-repo
```

Remove the file from all history:
```
git filter-repo --path secretfile.env --invert-paths
```

Then force-push all branches:
```
git push origin --force --all
```

⚠️ This rewrites history. Anyone who has cloned the repo will have diverged history and will need to re-clone. Coordinate with your team before doing this.

### How do I prevent committing secrets in the first place?

Three layers of prevention:

1. **Always add `.env` to `.gitignore` before your first commit.** Never commit `.env`, `*.pem`, `*.key`, `config.secret` or any file containing credentials.

2. **Use a pre-commit hook or scanning tool** to catch secrets before they are committed. TruffleHog (open source, 800+ secret types) and GitHub's built-in push protection (free for public repos) are both effective.

3. **Never hardcode credentials in your code.** Use environment variables or a secrets manager (AWS Secrets Manager, HashiCorp Vault, Doppler).

### Should I use SSH or HTTPS for authentication?

Both work. SSH is more convenient for regular use once set up - no passwords or tokens to manage. HTTPS is slightly easier to set up for beginners.

For SSH, use an **ed25519 key** - it is the current best practice. The older RSA key type still works but ed25519 is faster and more secure.

For HTTPS, use a **fine-grained Personal Access Token** (not a classic token, and never your account password which has not worked for Git since August 2021).

🔥 **Easiest option for beginners:** Install the GitHub CLI (`gh`) and run `gh auth login`. It handles everything through your browser and securely stores credentials automatically.

---

## 💻 IDEs and Editors

### Do I need to use the terminal? Can I use Git entirely through VS Code?

You do not need to use the terminal once you are set up. VS Code's Source Control panel (`Ctrl + Shift + G` / `Cmd + Shift + G`) handles staging, committing, pushing, pulling, branch switching and merge conflicts visually.

However, learning the terminal commands first is strongly recommended. It teaches you what is actually happening, and you will inevitably encounter situations where the terminal is the only option. This course teaches both.

### What is the best VS Code extension for Git?

**GitLens** by GitKraken (46 million+ downloads) is the most popular Git extension for VS Code. The free tier shows who wrote each line, lets you explore commit history and navigate the repository visually. GitLens Pro adds a full visual Commit Graph, Worktrees, Visual File History and AI features.

**GitHub Pull Requests** by GitHub (31 million+ downloads) lets you review and manage pull requests and issues directly inside VS Code.

Note: **Git Graph** (13 million+ downloads) still works but is no longer actively maintained. VS Code's built-in **Source Control Graph** (introduced in version 1.93) now provides visual branch history natively.

### Which JetBrains IDE should I use?

It depends on your language:

| Language | IDE |
|---|---|
| Java or Kotlin | IntelliJ IDEA (Community edition is free) |
| Python | PyCharm (Community edition is free) |
| JavaScript or TypeScript | WebStorm |
| C or C++ | CLion |
| C# or .NET | Rider |
| Go | GoLand |

All JetBrains IDEs share the same Git integration. If you do not know what you want yet, IntelliJ IDEA Community edition is a safe choice.

---

## 💼 Career and Industry

### Is a GitHub profile important for getting a job?

It helps significantly at tech-focused companies. **83% of technical hiring managers** view GitHub profiles as more reliable than traditional CVs. Recruiters look for: consistent contribution activity, clear READMEs, meaningful commit messages, code quality and open source involvement.

That said, an empty GitHub profile will not necessarily disqualify you - many excellent developers work on private company repositories and have little public activity. It matters most at startups and tech-first companies.

### Does Git/GitHub matter for fields other than software development?

Yes, significantly:

- **Data science** - tracking Jupyter notebooks, ML models, data pipelines and experiment results
- **DevOps and cloud** - GitOps (using Git as the source of truth for infrastructure) is used by over 64% of surveyed organisations as of 2025
- **Academic research** - Git enables reproducible research. Published papers from Johns Hopkins, MIT and other universities document integrating GitHub into statistics and data science courses
- **Technical writing** - documentation teams use Git branching and pull requests for collaborative writing and review
- **Government** - the US federal government requires agencies to publish at least 20% of custom code as open source. France has published its entire legal code on GitHub

### What about GitOps? What is it?

GitOps is a practice where your entire infrastructure (servers, cloud resources, Kubernetes deployments) is defined in Git repositories. Instead of manually running commands to deploy, every change goes through a Git pull request and an automated system applies it. It extends Git workflows from software development into operations. Popular tools include ArgoCD and Flux.

---

## 🚨 Common Errors

### `fatal: not a git repository`

You are trying to run a Git command in a folder that does not have a Git repository. Either:
- Run `git init` to create a new one here, or
- `cd` into the correct project folder first

### `error: src refspec main does not match any`

You tried to push a branch called `main` but your local branch is still called `master`. Fix it:
```
git branch -M main
```
Then push again.

### `rejected - non-fast-forward`

Someone else pushed commits to the remote that you do not have locally. Pull first:
```
git pull
```

Resolve any conflicts, then push again.

### `CONFLICT - Automatic merge failed`

A merge conflict. Open the flagged files, find the `<<<<<<<` markers, choose which changes to keep, save and then:
```
git add .
```
```
git commit
```

### `Permission denied (publickey)`

Your SSH key is not set up correctly. Check:
1. Your key exists: run `ls ~/.ssh/` and look for `id_ed25519` and `id_ed25519.pub`
2. The agent is running: `eval "$(ssh-agent -s)"` then `ssh-add ~/.ssh/id_ed25519`
3. Your public key is added to GitHub at https://github.com/settings/keys

### `HEAD detached at abc1234`

You checked out a specific commit directly rather than a branch. You are in "detached HEAD" state - you can look around but commits here will not be saved to any branch. To get back safely:
```
git switch main
```

Or create a new branch from here to save your work:
```
git switch -c my-new-branch
```

---

## 🔮 Git 3.0 and What is Coming

### What is Git 3.0?

Git 3.0 has **not been released yet**. Developers are targeting **late 2026**. It will be the first intentionally backwards-incompatible version of Git. Key planned changes:

- **Default hash algorithm changes from SHA-1 to SHA-256** for new repositories
- **Default ref storage changes from files to reftable** for new repositories
- **Default branch name becomes `main`** (currently `master` in older Git installs without configuration)
- **Rust becomes a mandatory build dependency**
- **Removal of deprecated commands**: `git whatchanged`, `git pack-redundant`, graft support

Note: SHA-256 repositories are not yet supported by GitHub, so the migration will not be seamless on day one.

### Should I worry about Git 3.0?

Not right now. Existing repositories will continue to work. The changes mostly affect new repositories created after upgrading. Setting `git config --global init.defaultBranch main` already matches the new default, so that part will be invisible.

When Git 3.0 releases, this course will be updated with migration guidance.

---

## 🔗 Sources and Further Reading

- [Official Git documentation](https://git-scm.com/doc) - the complete reference for all Git commands and concepts
- [Pro Git book](https://git-scm.com/book/en/v2) - the free official Git book, available online in multiple languages
- [GitHub official documentation](https://docs.github.com) - everything about using GitHub
- [GitLab official documentation](https://docs.gitlab.com) - everything about using GitLab
- [Git BreakingChanges documentation](https://git-scm.com/docs/BreakingChanges) - the official list of what will change in Git 3.0
- [Oh Shit, Git!?!](https://ohshitgit.com) - practical fixes for common Git disasters, also available profanity-free at dangitgit.com
- [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) - the widely-adopted standard for structured commit messages
- [GitHub Octoverse 2025](https://github.blog/news-insights/octoverse/) - the annual report on the state of open source and developer trends
- [Git turns 20: A Q&A with Linus Torvalds](https://github.blog/open-source/git/git-turns-20-a-qa-with-linus-torvalds/) - Linus Torvalds on Git's creation, in his own words
- [TruffleHog](https://github.com/trufflesecurity/trufflehog) - the leading open source secret scanning tool
- [git-filter-repo](https://github.com/newren/git-filter-repo) - the officially recommended tool for rewriting Git history

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
