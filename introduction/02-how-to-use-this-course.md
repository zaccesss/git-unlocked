# 📖 How to Use This Course

> "Give me six hours to chop down a tree and I will spend the first four sharpening the axe." - Abraham Lincoln

**Difficulty:** 🟢 Beginner
**Time to complete:** 5 minutes

Taking five minutes to understand how this course is structured will save you hours of confusion later. This file explains everything you need to know before diving in.

---

## 📋 Table of Contents

- [How the course is organised](#-how-the-course-is-organised)
- [Choosing where to start](#-choosing-where-to-start)
- [Difficulty levels](#-difficulty-levels)
- [Callout key](#-callout-key)
- [OS coverage](#-os-coverage)
- [IDE and editor coverage](#-ide-and-editor-coverage)
- [Where to type commands](#-where-to-type-commands)
- [How to get the most out of this course](#-how-to-get-the-most-out-of-this-course)
- [How to contribute](#-how-to-contribute)
- [Getting help](#-getting-help)

---

## 🗂️ How the Course is Organised

git-unlocked is split into folders. Each folder covers a specific area:

| Folder | What is inside |
|---|---|
| `introduction/` | Start here - welcome, setup and how to navigate |
| `git/` | Everything about Git itself - beginner to advanced |
| `github/` | Everything about GitHub - accounts, repos, PRs, Actions and more |
| `gitlab/` | Everything about GitLab - basics, merge requests, CI/CD and more |
| `ides/` | Using Git inside VS Code, JetBrains, Visual Studio, Xcode and other editors |
| `terminal/` | Using Git in the terminal - CMD, PowerShell, Bash and Zsh |
| `real-world/` | Real scenarios - team workflows, open source, industry practices |
| `reference/` | Cheatsheet, glossary, keyboard shortcuts, common mistakes and tools |
| `resources/` | Curated videos, books and practice platforms |
| `first-contribution/` | Make your very first pull request here as practice |

Files are numbered within each folder so you always know the recommended reading order. You do not have to follow the order - jump to whatever you need.

---

## 🚀 Choosing Where to Start

### Never used Git before?

Start here and work in order:

1. [Setting Up](03-setting-up.md) - get Git installed on your machine
2. [What is Version Control?](../git/01-what-is-version-control.md)
3. [Installing Git](../git/02-installing-git.md)
4. [How Git Works](../git/03-how-git-works.md)
5. [Core Concepts](../git/04-core-concepts.md)
6. [Basic Commands](../git/05-basic-commands.md)

### Know the basics but want to go further?

- [Branching](../git/06-branching.md) - work on features without breaking things
- [Merging](../git/07-merging.md) - combine your work back together
- [Pull Requests](../github/05-pull-requests.md) - collaborate professionally
- [GitLab Basics](../gitlab/04-gitlab-basics.md) - if you need GitLab too

### Ready for advanced topics?

- [Rebase](../git/12-rebase.md) - rewrite history cleanly
- [Cherry-pick](../git/13-cherry-pick.md) - grab specific commits
- [Reset and Revert](../git/14-reset-and-revert.md) - undo things safely
- [GitHub Actions](../github/06-github-actions.md) - automate everything
- [GitLab CI/CD](../gitlab/06-gitlab-ci-cd.md) - pipelines and deployment

### Just need a quick answer?

- [FAQ](../FAQ.md) - the most commonly asked questions answered
- [Cheatsheet](../reference/cheatsheet.md) - every command in one place
- [Glossary](../reference/glossary.md) - every term defined simply
- [Common Mistakes](../reference/common-mistakes.md) - and how to fix them

---

## 🎯 Difficulty Levels

Every file is marked with a difficulty level at the top:

| Level | Means |
|---|---|
| 🟢 Beginner | No prior knowledge needed |
| 🟡 Intermediate | Comfortable with basic Git and GitHub |
| 🔴 Advanced | Confident with everyday Git, ready to go deeper |

Do not let the difficulty labels stop you from reading ahead. They are a guide, not a gate.

---

## 💬 Callout Key

Throughout this course you will see these callout icons. They are used consistently in every file:

| Icon | Meaning |
|---|---|
| 💡 | Tip - a helpful insight worth knowing |
| ⚠️ | Warning - read this carefully before continuing |
| 🔥 | Pro tip - a technique that will level up your workflow |
| 🪟 | Windows specific instruction or note |
| 🍎 | Mac specific instruction or note |
| 🐧 | Linux specific instruction or note |
| ⌨️ | Keyboard shortcut |
| 🎥 | Watch this - a curated video that explains the topic visually |
| 🛠️ | Try it yourself - a hands-on exercise |
| ❌ | Common mistake - and exactly how to fix it |
| 🔒 | Security note - something important to keep your work and credentials safe |

---

## 🖥️ OS Coverage

Every command in this course is shown for all three major operating systems. You will always see the relevant section for your system clearly marked.

🪟 **Windows** - both Command Prompt and PowerShell where they differ
🍎 **Mac** - Terminal (Bash and Zsh, including Apple Silicon notes where relevant)
🐧 **Linux** - Bash (applicable to most distributions including Ubuntu, Debian, Fedora and Arch)

If a command is identical across all three operating systems it is shown once without a label.

---

## 💻 IDE and Editor Coverage

This course covers Git inside all major code editors and IDEs - not just VS Code. The `ides/` folder has a dedicated file for each:

| IDE / Editor | Coverage |
|---|---|
| VS Code | Full coverage - built-in Source Control panel, Source Control Graph, extensions (GitLens, GitHub Pull Requests etc.), shortcuts |
| JetBrains IDEs | IntelliJ IDEA, PyCharm, WebStorm, CLion, Rider, GoLand and all other JetBrains products |
| Visual Studio 2026 | Full Git integration for Windows developers |
| Xcode 26 | Git setup and usage for Apple platform developers |
| Neovim and Vim | Fugitive, Gitsigns, Neogit and LazyGit |
| Sublime Text | GitSavvy plugin |
| Other editors | General guidance applicable to any editor |

💡 **What is an IDE?** An IDE (Integrated Development Environment) is an all-in-one application that combines a code editor, debugger, terminal and version control integration in one window. Think of it as a super word processor for code. You do not need to know what an IDE is to use this course - it is explained fully in [ides/01-what-is-an-ide.md](../ides/01-what-is-an-ide.md).

---

## ⌨️ Where to Type Commands

This is one of the most important things to understand as a beginner. When this course shows you a command, it always tells you exactly where to type it.

Commands in this course are typed in one of these places:

**A terminal (also called command line or shell):**

🪟 **Windows** - open **Command Prompt** (search for `cmd`) or **PowerShell** (search for `powershell`) or **Windows Terminal**
🍎 **Mac** - open **Terminal** (search in Spotlight with `Cmd + Space`, then type `Terminal`)
🐧 **Linux** - open your **Terminal** application (usually `Ctrl + Alt + T`)

**Inside VS Code:**
Open the integrated terminal with `` Ctrl + ` `` (Windows/Linux) or `` Cmd + ` `` (Mac). Commands typed here work exactly the same as in a standalone terminal.

**Inside JetBrains IDEs:**
Open the terminal panel at the bottom with `Alt + F12` (Windows/Linux) or `Option + F12` (Mac).

⚠️ **Important for beginners:** Type commands one at a time. Do not paste a block of multiple commands at once until you are comfortable with what each one does. One command, read the output, then the next.

---

## 🏆 How to Get the Most Out of This Course

### Do the exercises

Every file ends with a 🛠️ Try It Yourself section. Do them. Reading about Git without practising it is like reading about swimming without getting in the water. The exercises use a real repository so everything you do counts toward your GitHub activity.

### Commit as you go

As you work through the course, use a real repository to practise. Commit your work every time you learn something new. You will build a genuine commit history and your GitHub contribution graph will grow alongside your knowledge.

### Use the first-contribution sandbox

The [first-contribution](../first-contribution/README.md) folder is a real repository where you can make your first pull request safely. It is designed for absolute beginners and takes less than five minutes.

### Do not skip the mistakes sections

The ❌ Common Mistakes sections in every file are based on real questions from Stack Overflow, Reddit and developer forums. These are the things that trip everyone up. Reading them before you encounter them will save you a lot of frustration.

### Read the security notes

The 🔒 security callouts throughout this course are not optional extras. Accidentally pushing a password or API key to a public GitHub repository is a real thing that happens to real developers regularly. The security guidance will protect you.

### Enable GitHub Discussions

If you have a question that is not covered in the FAQ, open a [Discussion](https://github.com/zaccessss/git-unlocked/discussions). Other learners and contributors will help, and good questions get added to the FAQ.

---

## 🤝 How to Contribute

This course is open source and grows through contributions. If you spot a mistake, want to improve an explanation or want to add something entirely new - you are very welcome.

Read [CONTRIBUTING.md](../CONTRIBUTING.md) for the full guide. It covers everything including the writing style, file naming, commit message format and how to open a pull request.

Everyone who contributes gets listed in the [Hall of Fame](../HALL_OF_FAME.md).

---

## 💬 Getting Help

| Need | Where to go |
|---|---|
| Quick answer | [FAQ.md](../FAQ.md) |
| Command reference | [reference/cheatsheet.md](../reference/cheatsheet.md) |
| Term definition | [reference/glossary.md](../reference/glossary.md) |
| Something went wrong | [reference/common-mistakes.md](../reference/common-mistakes.md) |
| Visual explanation | [resources/videos.md](../resources/videos.md) |
| Ask a question | [GitHub Discussions](https://github.com/zaccessss/git-unlocked/discussions) |
| Report an error | [GitHub Issues](https://github.com/zaccessss/git-unlocked/issues) |

---

## ➡️ Next Step

Head to [Setting Up](03-setting-up.md) to get Git installed and configured on your machine. →

---

## 🔗 Sources and Further Reading

- [Official Git documentation](https://git-scm.com/doc) - the authoritative reference for all Git commands
- [GitHub official documentation](https://docs.github.com) - everything about using GitHub
- [GitLab official documentation](https://docs.gitlab.com) - everything about using GitLab
- [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) - the widely-adopted standard for writing commit messages

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
