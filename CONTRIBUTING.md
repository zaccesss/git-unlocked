# 🤝 Contributing to git-unlocked

Thank you for being here. Every contribution, no matter how small, makes this course better for everyone. Whether you are fixing a typo, improving an explanation, adding an exercise or suggesting a whole new topic - you are welcome here.

This guide explains how to contribute properly.

---

## 📋 Table of Contents

- [Code of Conduct](#-code-of-conduct)
- [What You Can Contribute](#-what-you-can-contribute)
- [Before You Start](#-before-you-start)
- [How to Contribute - Step by Step](#-how-to-contribute---step-by-step)
- [Writing Style Guide](#-writing-style-guide)
- [File and Folder Naming](#-file-and-folder-naming)
- [Commit Message Format](#-commit-message-format)
- [Pull Request Guidelines](#-pull-request-guidelines)
- [What Happens After You Submit](#-what-happens-after-you-submit)

---

## 🧭 Code of Conduct

This project is for everyone. Be respectful, be kind and be constructive. Discrimination, harassment or gatekeeping of any kind will not be tolerated.

If something does not feel right, open an issue or contact [Isaac Adjei (Zaccess)](https://zacess.com).

---

## ✅ What You Can Contribute

You can contribute in any of the following ways:

- **Fix a typo or grammar mistake** - small but very appreciated
- **Improve an explanation** - if something was unclear to you, it is unclear to others too
- **Add a missing command or example** - if something is missing, add it
- **Add a real world scenario** - practical examples make concepts stick
- **Add or improve a Try It Yourself exercise** - hands-on practice is core to this course
- **Add a curated video or resource** - must be from a verified, reputable source
- **Translate a file** - help make this course accessible in more languages
- **Suggest a new topic** - open an issue first to discuss it
- **Report an error** - open an issue describing what is wrong and where

---

## 🔍 Before You Start

1. **Check open issues** at [github.com/zaccessss/git-unlocked/issues](https://github.com/zaccessss/git-unlocked/issues) to see if someone is already working on it
2. **Check open pull requests** to avoid duplicating work
3. **Open an issue first** if you are planning a large change - this saves everyone time
4. **Read the writing style guide below** before writing anything

---

## 🛠️ How to Contribute - Step by Step

### Step 1 - Fork the repo

Go to [github.com/zaccessss/git-unlocked](https://github.com/zaccessss/git-unlocked) and click **Fork** in the top right corner. This creates your own copy of the repo.

### Step 2 - Clone your fork

```bash
# Replace YOUR_USERNAME with your GitHub username
git clone https://github.com/YOUR_USERNAME/git-unlocked.git
cd git-unlocked
```

### Step 3 - Create a branch

Always create a new branch for your changes. Never work directly on `main`.

```bash
# Use a descriptive branch name
git checkout -b fix/typo-in-branching-file
git checkout -b add/cherry-pick-exercise
git checkout -b improve/merge-conflict-explanation
```

### Step 4 - Make your changes

Open the project in VS Code and make your changes. Follow the writing style guide below.

### Step 5 - Commit your changes

```bash
git add .
git commit -m "fix: correct typo in git/06-branching.md"
```

Follow the commit message format described below.

### Step 6 - Push to your fork

```bash
git push origin your-branch-name
```

### Step 7 - Open a pull request

Go to your fork on GitHub and click **Compare & pull request**. Fill in the PR template clearly and submit.

---

## ✍️ Writing Style Guide

All content in this course follows these rules. Please match them exactly.

### Language and tone

- Write in clear, plain **UK English** - colour, organise, practise, licence, recognise etc.
- Write as if you are explaining to a friend - friendly but professional
- Do not talk down to the reader
- Do not use jargon without explaining it first
- No Oxford commas - "Git, GitHub and GitLab" not "Git, GitHub, and GitLab"
- No em dashes - use hyphens only when necessary
- Use correct grammar and spelling throughout

### Name and branding

- Always refer to the author as **Isaac Adjei (Zaccess)**
- Website: [zacess.com](https://zacess.com)
- GitHub: [github.com/zaccessss](https://github.com/zaccessss)
- Branding line: **Access Granted. Success Unlocked.**
- The 🔓 padlock is the course symbol - it represents knowledge being unlocked

### Structure

Every content file must follow this structure:

```markdown
# Title

> One sentence description of what this file covers.

**Difficulty:** 🟢 Beginner / 🟡 Intermediate / 🔴 Advanced  
**Time to complete:** X minutes

---

## 📋 Table of Contents

- [Section 1](#section-1)
- [Section 2](#section-2)

---

## Section 1

...content...

---

## 🛠️ Try It Yourself

...exercise...

---

## ❌ Common Mistakes

...mistakes and fixes...

---

## 📖 Summary

...key takeaways...

---

## 🔗 Sources and Further Reading

- [Official Git docs](https://git-scm.com/doc)
- [GitHub docs](https://docs.github.com)
- [GitLab docs](https://docs.gitlab.com)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
```

### OS coverage

Every command must be shown for all three operating systems:

````markdown
🪟 **Windows - Command Prompt / PowerShell**

```bash
git init
```
````

🍎 **Mac - Terminal**

```bash
git init
```

🐧 **Linux - Bash**

```bash
git init
```

```

### Callouts

Use these callout icons consistently:

| Icon | Use for |
|---|---|
| 💡 | Tips and helpful insights |
| ⚠️ | Warnings - things people must not skip |
| 🔥 | Pro tips for more advanced users |
| ⌨️ | Keyboard shortcuts |
| 🎥 | Curated video links |
| 🛠️ | Try it yourself exercises |
| ❌ | Common mistakes and their fixes |

---

## 📁 File and Folder Naming

- All file names use **lowercase** and **hyphens** only - no spaces, no underscores, no capitals
- Follow the existing numbering format: `01-`, `02-`, `03-` etc.
- Place files in the correct folder - `git/`, `github/`, `gitlab/`, `vscode/`, `terminal/`, `real-world/`, `reference/` or `resources/`
- Do not create new top-level folders without discussing it in an issue first

✅ Correct: `git/06-branching.md`
❌ Wrong: `git/Branching.md`, `git/branching_guide.md`, `git/branchingGuide.md`

---

## 📝 Commit Message Format

Use this format for all commits:

```

type: short description of what you did

````

| Type | When to use |
|---|---|
| `add` | Adding new content or files |
| `fix` | Fixing a typo, error or broken link |
| `update` | Improving or expanding existing content |
| `remove` | Removing something |
| `rename` | Renaming a file or folder |

**Examples:**

```bash
git commit -m "add: cherry-pick exercise to git/13-cherry-pick.md"
git commit -m "fix: correct typo in github/04-issues.md"
git commit -m "update: improve merge conflict explanation with diagram"
git commit -m "add: translate introduction/01-welcome.md to French"
````

---

## 🔀 Pull Request Guidelines

When opening a pull request:

- Give it a clear title that describes what you changed
- Explain what you changed and why in the description
- Link any related issues using `Closes #123` or `Relates to #123`
- Make sure your changes follow the writing style guide
- Make sure you have checked for typos and grammar errors
- Keep PRs focused - one topic per PR is much easier to review

---

## 🔄 What Happens After You Submit

1. Your PR will be reviewed by [Isaac Adjei (Zaccess)](https://github.com/zaccessss)
2. You may receive feedback or requests for changes - this is normal and not personal
3. Once approved, your PR will be merged into `main`
4. Your name will be added to [HALL_OF_FAME.md](HALL_OF_FAME.md)

Thank you for helping make git-unlocked better. 🔓

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
