<div align="center">

<img src="assets/banner.png" alt="git-unlocked - Access Granted. Success Unlocked." width="100%">

<br />

# git-unlocked

### The complete open source course for Git, GitHub and GitLab.

### From absolute zero to industry-level advanced - on Windows, Mac and Linux.

<br />

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](LICENSE)
[![Stars](https://img.shields.io/github/stars/zaccessss/git-unlocked?style=for-the-badge&logo=github)](https://github.com/zaccessss/git-unlocked/stargazers)
[![Forks](https://img.shields.io/github/forks/zaccessss/git-unlocked?style=for-the-badge&logo=github)](https://github.com/zaccessss/git-unlocked/network/members)
[![Contributors](https://img.shields.io/github/contributors/zaccessss/git-unlocked?style=for-the-badge)](https://github.com/zaccessss/git-unlocked/graphs/contributors)
[![Last Commit](https://img.shields.io/github/last-commit/zaccessss/git-unlocked?style=for-the-badge)](https://github.com/zaccessss/git-unlocked/commits/main)
[![Issues](https://img.shields.io/github/issues/zaccessss/git-unlocked?style=for-the-badge)](https://github.com/zaccessss/git-unlocked/issues)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen?style=for-the-badge)](CONTRIBUTING.md)
[![Contributor Covenant](https://img.shields.io/badge/Contributor%20Covenant-3.0-4baaaa.svg?style=for-the-badge)](CODE_OF_CONDUCT.md)

<br />

[Start Learning](#start-here) · [Learning Path](#learning-path) · [Course Structure](#course-structure) · [Contribute](CONTRIBUTING.md) · [Discussions](https://github.com/zaccessss/git-unlocked/discussions) · [Hall of Fame](HALL_OF_FAME.md) · [FAQ](FAQ.md)

<br />

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/zaccessss/git-unlocked)

</div>

---

## Who This Is For

This is not just another Git cheatsheet. This is a complete, structured course - built to take anyone from never having heard of version control all the way to using Git, GitHub and GitLab confidently in real teams and real companies.

Every file covers **Windows, Mac and Linux** side by side. Every topic includes real examples, step-by-step exercises and a dedicated section on common mistakes. Nothing is assumed. Nothing is skipped.

| Level           | You are...                             | Where to start                        |
| --------------- | -------------------------------------- | ------------------------------------- |
| Beginner 🟢     | Brand new to Git or version control    | [Welcome](introduction/01-welcome.md) |
| Intermediate 🟡 | Know the basics, want to go further    | [Branching](git/06-branching.md)      |
| Advanced 🔴     | Comfortable with Git, ready to go deep | [Rebase](git/12-rebase.md)            |

---

## Start Here

Never used Git before? Start with the introduction. It will get you set up and ready before you touch a single command.

1. [Welcome to git-unlocked](introduction/01-welcome.md)
2. [How to use this course](introduction/02-how-to-use-this-course.md)
3. [Setting up your machine](introduction/03-setting-up.md)

Then move to [What is version control?](git/01-what-is-version-control.md)

---

## Learning Path

```
BEGINNER
|
+-- 01. What is version control?
+-- 02. Installing Git (Windows / Mac / Linux)
+-- 03. How Git works under the hood
+-- 04. Core concepts - repos, commits, branches, staging
+-- 05. Basic commands - init, add, commit, status, log, diff
+-- 06. What is GitHub?
+-- 07. Creating your GitHub account
+-- 08. Pushing your first repository to GitHub

INTERMEDIATE
|
+-- 09. Branching and switching
+-- 10. Merging branches
+-- 11. Merge conflicts and how to fix them
+-- 12. Remote repositories - origin and upstream
+-- 13. Pull requests
+-- 14. .gitignore
+-- 15. Stashing work in progress
+-- 16. Git workflows - Gitflow, trunk-based, GitHub Flow

ADVANCED
|
+-- 17. Rebase - interactive rebase vs merge
+-- 18. Cherry-pick - grabbing specific commits
+-- 19. Reset and revert - undoing things safely
+-- 20. Tags and releases
+-- 21. Submodules
+-- 22. Git hooks - automating your workflow
+-- 23. Bisect - finding which commit broke something
+-- 24. Signing commits - GPG and SSH verification
+-- 25. Advanced Git config - aliases, conditional includes
+-- 26. Large repositories - Git LFS, sparse checkout
+-- 27. Monorepos - managing large multi-package repositories
+-- 28. GitOps - Git as the source of truth for infrastructure
+-- 29. Git internals advanced - packfiles, reflog, gc

GITHUB
|
+-- Repositories, issues and pull requests
+-- GitHub Actions - CI/CD pipelines
+-- GitHub Pages - free website hosting
+-- Projects, discussions and security features
+-- Profile README, badges and achievements

GITLAB
|
+-- What GitLab is and how it differs from GitHub
+-- Merge requests
+-- GitLab CI/CD pipelines
+-- Runners, environments and advanced features

IDEs AND TERMINAL
|
+-- Git in VS Code, JetBrains, Visual Studio, Xcode and more
+-- Extensions and plugins for every editor
+-- Windows CMD, PowerShell, Mac Terminal, Linux Bash
```

---

## Course Structure

### Introduction

Start here if you are completely new.

| File                                                                      | Topic                                                | Level |
| ------------------------------------------------------------------------- | ---------------------------------------------------- | ----- |
| [01-welcome.md](introduction/01-welcome.md)                               | What this course is and how to use it                | 🟢    |
| [02-how-to-use-this-course.md](introduction/02-how-to-use-this-course.md) | Learning path, callout key and tips                  | 🟢    |
| [03-setting-up.md](introduction/03-setting-up.md)                         | Getting your machine ready on Windows, Mac and Linux | 🟢    |

---

### Git

Pure Git - works on any platform, any host.

| File                                                               | Topic                                                               | Level  |
| ------------------------------------------------------------------ | ------------------------------------------------------------------- | ------ |
| [01-what-is-version-control.md](git/01-what-is-version-control.md) | What version control is, its history and why it exists              | 🟢     |
| [02-installing-git.md](git/02-installing-git.md)                   | Installing Git on Windows, Mac and Linux                            | 🟢     |
| [03-how-git-works.md](git/03-how-git-works.md)                     | How Git tracks changes under the hood                               | 🟢     |
| [04-core-concepts.md](git/04-core-concepts.md)                     | Repos, commits, branches and the staging area                       | 🟢     |
| [05-basic-commands.md](git/05-basic-commands.md)                   | init, clone, add, commit, status, log, diff                         | 🟢     |
| [06-branching.md](git/06-branching.md)                             | Creating, switching and deleting branches                           | 🟡     |
| [07-merging.md](git/07-merging.md)                                 | Merging branches together                                           | 🟡     |
| [08-merge-conflicts.md](git/08-merge-conflicts.md)                 | What conflicts are and how to fix them                              | 🟡     |
| [09-remote-repos.md](git/09-remote-repos.md)                       | origin, upstream and working with multiple remotes                  | 🟡     |
| [10-gitignore.md](git/10-gitignore.md)                             | Ignoring files and folders - including security uses                | 🟡     |
| [11-stash.md](git/11-stash.md)                                     | Saving work without committing                                      | 🟡     |
| [12-git-workflows.md](git/12-git-workflows.md)                     | Gitflow, trunk-based development, GitHub Flow and feature branching | 🟡     |
| [13-rebase.md](git/13-rebase.md)                                   | Rebase vs merge and interactive rebase                              | 🔴     |
| [14-cherry-pick.md](git/14-cherry-pick.md)                         | Picking specific commits across branches                            | 🔴     |
| [15-reset-and-revert.md](git/15-reset-and-revert.md)               | Undoing changes safely - soft, mixed and hard reset                 | 🔴     |
| [16-tags-and-releases.md](git/16-tags-and-releases.md)             | Tagging versions and creating releases                              | 🔴     |
| [17-submodules.md](git/17-submodules.md)                           | Embedding repos inside repos                                        | 🔴     |
| [18-hooks.md](git/18-hooks.md)                                     | Automating actions with Git hooks                                   | 🔴     |
| [19-bisect.md](git/19-bisect.md)                                   | Finding which commit introduced a bug                               | 🔴     |
| [20-signing-commits.md](git/20-signing-commits.md)                 | GPG and SSH commit signing and verification                         | 🔴     |
| [21-advanced-config.md](git/21-advanced-config.md)                 | Aliases, conditional includes and advanced Git configuration        | 🔴     |
| [22-large-repos.md](git/22-large-repos.md)                         | Git LFS, sparse checkout, shallow clones and partial clones         | 🔴     |
| [23-monorepos.md](git/23-monorepos.md)                             | Managing large multi-package repositories with Git                  | 🔴     |
| [24-gitops.md](git/24-gitops.md)                                   | Git as the source of truth for infrastructure and deployments       | 🔴     |
| [25-git-internals-advanced.md](git/25-git-internals-advanced.md)   | Packfiles, reflog, garbage collection and the object model          | 🔴     |
| [26-all-git-commands.md](git/26-all-git-commands.md)               | Every Git command in one place                                      | 🟢🟡🔴 |

---

### GitHub

| File                                                                  | Topic                                                 | Level |
| --------------------------------------------------------------------- | ----------------------------------------------------- | ----- |
| [01-what-is-github.md](github/01-what-is-github.md)                   | What GitHub is, its history and why it matters        | 🟢    |
| [02-creating-an-account.md](github/02-creating-an-account.md)         | Setting up your GitHub account step by step           | 🟢    |
| [03-repositories.md](github/03-repositories.md)                       | Creating and managing repositories on GitHub          | 🟢    |
| [04-issues.md](github/04-issues.md)                                   | Opening, labelling, assigning and closing issues      | 🟢    |
| [05-pull-requests.md](github/05-pull-requests.md)                     | Creating, reviewing, approving and merging PRs        | 🟡    |
| [06-github-actions.md](github/06-github-actions.md)                   | Automating workflows with CI/CD pipelines             | 🔴    |
| [07-github-pages.md](github/07-github-pages.md)                       | Hosting a free website directly from a repo           | 🟢    |
| [08-discussions.md](github/08-discussions.md)                         | Community discussions and Q&A on GitHub               | 🟢    |
| [09-projects-and-boards.md](github/09-projects-and-boards.md)         | Managing work with GitHub Projects                    | 🟡    |
| [10-security-features.md](github/10-security-features.md)             | Dependabot, secret scanning, code scanning and more   | 🟡    |
| [11-profile-and-readme.md](github/11-profile-and-readme.md)           | Building a standout GitHub profile and profile README | 🟢    |
| [12-badges-and-achievements.md](github/12-badges-and-achievements.md) | Earning GitHub badges and achievements                | 🟢    |
| [13-github-advanced.md](github/13-github-advanced.md)                 | Codespaces, Copilot, wikis, releases and sponsors     | 🔴    |

---

### GitLab

| File                                                          | Topic                                                      | Level |
| ------------------------------------------------------------- | ---------------------------------------------------------- | ----- |
| [01-what-is-gitlab.md](gitlab/01-what-is-gitlab.md)           | What GitLab is, its history and how it differs from GitHub | 🟢    |
| [02-gitlab-vs-github.md](gitlab/02-gitlab-vs-github.md)       | Side by side comparison - when to use which                | 🟢    |
| [03-creating-an-account.md](gitlab/03-creating-an-account.md) | Setting up your GitLab account step by step                | 🟢    |
| [04-gitlab-basics.md](gitlab/04-gitlab-basics.md)             | Repos, commits and pushing on GitLab                       | 🟢    |
| [05-merge-requests.md](gitlab/05-merge-requests.md)           | GitLab's version of pull requests                          | 🟡    |
| [06-gitlab-ci-cd.md](gitlab/06-gitlab-ci-cd.md)               | Pipelines, jobs and .gitlab-ci.yml                         | 🔴    |
| [07-gitlab-advanced.md](gitlab/07-gitlab-advanced.md)         | Runners, environments, packages and more                   | 🔴    |

---

### IDEs and Editors

| File                                                              | Topic                                                          | Level |
| ----------------------------------------------------------------- | -------------------------------------------------------------- | ----- |
| [01-what-is-an-ide.md](ides/01-what-is-an-ide.md)                 | What an IDE is and how it differs from a text editor           | 🟢    |
| [02-git-in-vscode.md](ides/02-git-in-vscode.md)                   | Using the built-in Git UI in VS Code                           | 🟢    |
| [03-git-in-jetbrains.md](ides/03-git-in-jetbrains.md)             | Git in IntelliJ IDEA, PyCharm, WebStorm, CLion, Rider and more | 🟢    |
| [04-git-in-visual-studio.md](ides/04-git-in-visual-studio.md)     | Git integration in Visual Studio 2026                          | 🟢    |
| [05-git-in-xcode.md](ides/05-git-in-xcode.md)                     | Git integration in Xcode for Apple platform developers         | 🟢    |
| [06-git-in-other-editors.md](ides/06-git-in-other-editors.md)     | Neovim, Sublime Text and other editors                         | 🟢    |
| [07-extensions-and-plugins.md](ides/07-extensions-and-plugins.md) | GitLens, Git Graph, GitHub Pull Requests and more              | 🟢    |
| [08-shortcuts-by-ide.md](ides/08-shortcuts-by-ide.md)             | Keyboard shortcuts for every major IDE                         | 🟢    |

---

### Terminal

| File                                                          | Topic                                                 | Level |
| ------------------------------------------------------------- | ----------------------------------------------------- | ----- |
| [01-without-git.md](terminal/01-without-git.md)               | What you can do in the terminal before installing Git | 🟢    |
| [02-windows-cmd.md](terminal/02-windows-cmd.md)               | Using Git in Windows Command Prompt                   | 🟢    |
| [03-windows-powershell.md](terminal/03-windows-powershell.md) | Using Git in Windows PowerShell                       | 🟢    |
| [04-mac-terminal.md](terminal/04-mac-terminal.md)             | Using Git in Mac Terminal                             | 🟢    |
| [05-linux-bash.md](terminal/05-linux-bash.md)                 | Using Git in Linux Bash                               | 🟢    |

---

### Real World

| File                                                                        | Topic                                       | Level |
| --------------------------------------------------------------------------- | ------------------------------------------- | ----- |
| [01-team-workflows.md](real-world/01-team-workflows.md)                     | Working on a team with Git - real scenarios | 🟡    |
| [02-open-source-contributing.md](real-world/02-open-source-contributing.md) | How to contribute to open source projects   | 🟡    |
| [03-recovering-from-mistakes.md](real-world/03-recovering-from-mistakes.md) | Fixing things when they go wrong            | 🟡    |
| [04-git-in-industry.md](real-world/04-git-in-industry.md)                   | How real companies and teams use Git        | 🔴    |

---

### Reference

| File                                                     | Topic                                                       |
| -------------------------------------------------------- | ----------------------------------------------------------- |
| [cheatsheet.md](reference/cheatsheet.md)                 | Every Git command in one quick-reference page               |
| [keyboard-shortcuts.md](reference/keyboard-shortcuts.md) | Shortcuts for the terminal, VS Code and GitHub web          |
| [glossary.md](reference/glossary.md)                     | Every term defined clearly                                  |
| [common-mistakes.md](reference/common-mistakes.md)       | The most common mistakes at every level and how to fix them |
| [tools.md](reference/tools.md)                           | Git GUIs, diff tools, terminal setups and recommended tools |

---

### Resources

| File                                                     | What is inside                                        |
| -------------------------------------------------------- | ----------------------------------------------------- |
| [videos.md](resources/videos.md)                         | Curated YouTube videos organised by topic             |
| [books.md](resources/books.md)                           | Free and paid books on Git and version control        |
| [practice-platforms.md](resources/practice-platforms.md) | Interactive platforms to practise Git in your browser |

---

## Callout Key

## Callout Key

Throughout every file in this course you will see these callouts:

> [!NOTE]
> Something worth knowing - background information or context.

> [!TIP]
> A helpful technique, shortcut or best practice.

> [!IMPORTANT]
> A pro tip that will meaningfully improve your workflow.

> [!WARNING]
> Read this before continuing - skipping it commonly causes problems.

> [!CAUTION]
> A security note - something that protects your credentials, data or accounts.

OS-specific instructions are clearly labelled as **Windows**, **Mac** or **Linux**. Difficulty levels use 🟢 Beginner, 🟡 Intermediate and 🔴 Advanced.

---

## Make Your First Contribution

The best way to learn Git is to use it on a real project. The [first-contribution](first-contribution/README.md) folder is a safe sandbox for absolute beginners. Add your name to the contributors list and make your very first pull request. It takes less than five minutes.

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
[![LinkedIn](https://img.shields.io/badge/linkedin-isaacadjei-0A66C2?style=for-the-badge&logo=linkedin)](https://linkedin.com/in/isaacadjei)

</div>
