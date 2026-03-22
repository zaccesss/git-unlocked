# How to Use This Course

**Difficulty:** 🟢 Beginner | **Time:** 5 minutes

Taking five minutes to understand how this course is structured will save you hours of confusion later.

---

## Table of Contents

- [How the course is organised](#how-the-course-is-organised)
- [Where to start](#where-to-start)
- [Difficulty levels](#difficulty-levels)
- [Callout key](#callout-key)
- [OS coverage](#os-coverage)
- [IDE and editor coverage](#ide-and-editor-coverage)
- [Where to type commands](#where-to-type-commands)
- [How to get the most out of this course](#how-to-get-the-most-out-of-this-course)
- [Getting help](#getting-help)
- [Sources and Further Reading](#sources-and-further-reading)

---

## How the Course is Organised

git-unlocked is split into folders. Each folder covers a specific area:

| Folder                | What is inside                                                              |
| --------------------- | --------------------------------------------------------------------------- |
| `introduction/`       | Start here - welcome, setup and how to navigate                             |
| `git/`                | Everything about Git itself - beginner to advanced                          |
| `github/`             | Everything about GitHub - accounts, repos, PRs, Actions and more            |
| `gitlab/`             | Everything about GitLab - basics, merge requests, CI/CD and more            |
| `ides/`               | Using Git inside VS Code, JetBrains, Visual Studio, Xcode and other editors |
| `terminal/`           | Using Git in the terminal - CMD, PowerShell, Bash and Zsh                   |
| `real-world/`         | Real scenarios - team workflows, open source, industry practices            |
| `reference/`          | Cheatsheet, glossary, keyboard shortcuts, common mistakes and tools         |
| `resources/`          | Curated videos, books and practice platforms                                |
| `first-contribution/` | Make your very first pull request here as practice                          |

Files are numbered within each folder so you always know the recommended reading order. You do not have to follow it - jump to whatever you need.

---

## Where to Start

**Never used Git before?** Work through these in order:

1. [Setting Up](03-setting-up.md)
2. [What is Version Control?](../git/01-what-is-version-control.md)
3. [Installing Git](../git/02-installing-git.md)
4. [How Git Works](../git/03-how-git-works.md)
5. [Core Concepts](../git/04-core-concepts.md)
6. [Basic Commands](../git/05-basic-commands.md)

**Know the basics but want to go further?**

- [Branching](../git/06-branching.md) - work on features without breaking things
- [Merging](../git/07-merging.md) - combine your work back together
- [Pull Requests](../github/05-pull-requests.md) - collaborate professionally
- [GitLab Basics](../gitlab/04-gitlab-basics.md) - if you also need GitLab

**Ready for advanced topics?**

- [Rebase](../git/12-rebase.md) - rewrite history cleanly
- [Cherry-pick](../git/13-cherry-pick.md) - grab specific commits
- [Reset and Revert](../git/14-reset-and-revert.md) - undo things safely
- [GitHub Actions](../github/06-github-actions.md) - automate everything
- [GitLab CI/CD](../gitlab/06-gitlab-ci-cd.md) - pipelines and deployment

**Just need a quick answer?**

- [FAQ](../FAQ.md)
- [Cheatsheet](../reference/cheatsheet.md)
- [Glossary](../reference/glossary.md)
- [Common Mistakes](../reference/common-mistakes.md)

---

## Difficulty Levels

Every file is marked with a difficulty level at the top:

| Level           | Means                                           |
| --------------- | ----------------------------------------------- |
| 🟢 Beginner     | No prior knowledge needed                       |
| 🟡 Intermediate | Comfortable with basic Git and GitHub           |
| 🔴 Advanced     | Confident with everyday Git, ready to go deeper |

The levels are a guide, not a gate. Do not let them stop you from reading ahead.

---

## Callout Key

Throughout this course you will see GitHub-native alert boxes for important information:

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

OS-specific instructions are clearly labelled as **Windows**, **Mac** or **Linux**.

---

## OS Coverage

Every command in this course is shown for all three major operating systems:

- **Windows** - both Command Prompt and PowerShell where they differ
- **Mac** - Terminal (Bash and Zsh, including Apple Silicon notes where relevant)
- **Linux** - Bash (applicable to Ubuntu, Debian, Fedora, Arch and most major distributions)

If a command is identical across all three systems, it is shown once without a label.

---

## IDE and Editor Coverage

This course covers Git inside all major code editors and IDEs - not just VS Code:

| IDE / Editor       | What is covered                                                                         |
| ------------------ | --------------------------------------------------------------------------------------- |
| VS Code            | Built-in Source Control panel, Source Control Graph, extensions, shortcuts              |
| JetBrains          | IntelliJ IDEA, PyCharm, WebStorm, CLion, Rider, GoLand and all other JetBrains products |
| Visual Studio 2026 | Full Git integration for Windows developers                                             |
| Xcode 26           | Git setup and usage for Apple platform developers                                       |
| Neovim and Vim     | Fugitive, Gitsigns, Neogit and LazyGit                                                  |
| Sublime Text       | GitSavvy plugin                                                                         |

> [!NOTE]
> An **IDE (Integrated Development Environment)** is an all-in-one application that combines a code editor, debugger, terminal and version control in one window. It is fully explained in [ides/01-what-is-an-ide.md](../ides/01-what-is-an-ide.md).

---

## Where to Type Commands

This is one of the most important things to understand as a beginner. When this course shows you a command, it always tells you exactly where to type it.

**In a terminal:**

- **Windows** - open **Command Prompt** (search `cmd`) or **PowerShell** (search `powershell`) or **Windows Terminal**
- **Mac** - open **Terminal** (press `Cmd + Space`, type `Terminal`, press Enter)
- **Linux** - open your **Terminal** application (usually `Ctrl + Alt + T`)

**Inside VS Code:**
Open the integrated terminal with `` Ctrl + ` `` (Windows/Linux) or `` Cmd + ` `` (Mac). Commands work exactly the same as in a standalone terminal.

**Inside JetBrains:**
Open the terminal panel with `Alt + F12` (Windows/Linux) or `Option + F12` (Mac).

> [!WARNING]
> Type commands **one at a time**. Press Enter after each one and read the output before typing the next command. Do not paste a block of multiple commands at once until you understand what each one does.

---

## How to Get the Most Out of This Course

**Do the exercises.** Every file includes a Try It Yourself section. Reading about Git without practising it is like reading about swimming without getting in the water. The exercises use a real repository so everything you do counts toward your GitHub activity.

**Commit as you go.** Use a real repository to practise as you work through the course. You will build a genuine commit history and your GitHub contribution graph will grow alongside your knowledge.

**Use the first-contribution sandbox.** The [first-contribution](../first-contribution/README.md) folder is where you can make your first pull request safely. It is designed for absolute beginners and takes less than five minutes.

**Do not skip the Common Mistakes sections.** These are based on real questions from Stack Overflow, Reddit and developer forums - the things that trip everyone up. Reading them before you encounter them will save you a lot of time.

**Read the security callouts.** Accidentally pushing a password or API key to a public GitHub repository happens to real developers regularly. The security guidance will protect you.

---

## Getting Help

| Need                 | Where to go                                                                |
| -------------------- | -------------------------------------------------------------------------- |
| Quick answer         | [FAQ.md](../FAQ.md)                                                        |
| Command reference    | [reference/cheatsheet.md](../reference/cheatsheet.md)                      |
| Term definition      | [reference/glossary.md](../reference/glossary.md)                          |
| Something went wrong | [reference/common-mistakes.md](../reference/common-mistakes.md)            |
| Ask a question       | [GitHub Discussions](https://github.com/zaccesss/git-unlocked/discussions) |
| Report an error      | [GitHub Issues](https://github.com/zaccesss/git-unlocked/issues)           |

---

## Next Step

Head to [Setting Up](03-setting-up.md) to get your accounts created, Git installed and everything configured.

---

## Sources and Further Reading

- [Official Git documentation](https://git-scm.com/doc) - the authoritative reference for all Git commands
- [GitHub documentation](https://docs.github.com) - everything about using GitHub
- [GitLab documentation](https://docs.gitlab.com) - everything about using GitLab
- [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) - the widely-adopted standard for structured commit messages

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
