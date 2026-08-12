# Contributing to git-unlocked

Thank you for being here. Every contribution, no matter how small, makes this course better for everyone - whether you are fixing a typo, improving an explanation, adding an exercise or suggesting a new topic.

Please read this guide carefully before opening a pull request.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [What you can contribute](#what-you-can-contribute)
- [Before you start](#before-you-start)
- [How to contribute - step by step](#how-to-contribute--step-by-step)
- [Writing style guide](#writing-style-guide)
- [File and folder naming](#file-and-folder-naming)
- [Commit message format](#commit-message-format)
- [Pull request guidelines](#pull-request-guidelines)
- [What happens after you submit](#what-happens-after-you-submit)

---

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating you agree to uphold it. Be respectful, be kind and be constructive.

---

## What You Can Contribute

- **Fix a typo or grammar mistake** - small but very appreciated
- **Improve an explanation** - if something was unclear to you, it is unclear to others too
- **Add a missing command or example** - if something should be here and is not, add it
- **Add a real-world scenario** - practical examples make concepts stick
- **Add or improve a Try It Yourself exercise** - hands-on practice is core to this course
- **Add a curated video or resource** - must be from a verified, reputable source
- **Translate a file** - help make this course accessible in more languages
- **Suggest a new topic** - open an issue first to discuss it
- **Report an error** - open an issue describing what is wrong and where

---

## Before You Start

1. Check **[open issues](https://github.com/zaccesss/git-unlocked/issues)** to see if someone is already working on it
2. Check **[open pull requests](https://github.com/zaccesss/git-unlocked/pulls)** to avoid duplicating work
3. **Open an issue first** if you are planning a large change - this saves everyone time
4. Read the **writing style guide** below before writing anything

---

## How to Contribute - Step by Step

### Step 1 - Fork the repo

Go to [github.com/zaccesss/git-unlocked](https://github.com/zaccesss/git-unlocked) and click **Fork**.

### Step 2 - Clone your fork

```bash
git clone https://github.com/YOUR_USERNAME/git-unlocked.git
```

```bash
cd git-unlocked
```

### Step 3 - Create a branch

Never work directly on `main`. Create a descriptive branch:

```bash
git checkout -b fix/typo-in-branching-file
```

```bash
git checkout -b add/cherry-pick-exercise
```

### Step 4 - Make your changes

Open the project in your editor and make your changes following the writing style guide below.

### Step 5 - Commit your changes

```bash
git add .
```

```bash
git commit -m "fix: correct typo in git/06-branching.md"
```

### Step 6 - Push to your fork

```bash
git push origin your-branch-name
```

### Step 7 - Open a pull request

Go to your fork on GitHub and click **Compare & pull request**. Fill in the PR template and submit.

---

## Writing Style Guide

All content in this course follows these rules exactly. Please match them.

### Language and tone

- Write in **UK English** - colour, organise, practise, licence (noun), recognise
- Do not use em dashes or en dashes in prose - use a hyphen, colon or rewrite the sentence
- Do not use the Oxford comma: write "X, Y and Z" without a comma before "and"
- Maintain a clear, professional and accessible tone throughout
- Assume the reader has zero prior knowledge unless the file is marked 🟡 or 🔴
- Do not use jargon without explaining it first
- Use correct grammar and spelling throughout

### Emoji policy

This is important. Emoji use is minimal and functional only.

**Always keep:**

- 🟢🟡🔴 difficulty labels
- 🪟🍎🐧 OS labels (functional and instantly recognisable)
- 🔓 in the footer only (brand symbol)

**Never use:**

- Emoji in body text paragraphs
- Emoji as bullet point markers
- Emoji in section headings
- Decorative emoji anywhere

### Callout boxes

Use GitHub's native alert syntax instead of emoji callouts. This renders properly on GitHub and is accessible to screen readers.

```markdown
> [!NOTE]
> Something worth knowing.

> [!TIP]
> A helpful technique or shortcut.

> [!IMPORTANT]
> A pro tip that will level up workflow.

> [!WARNING]
> Read this carefully before continuing.

> [!CAUTION]
> A security note - credentials, data, accounts.
```

Do **not** use `💡 **Tip:**`, `⚠️ **Warning:**`, `🔥 **Pro tip:**` or `🔒` in body text. Use the alert syntax above instead.

### Branding

- Author name: **Isaac Adjei (Zaccess)** - always in this order
- Website: [isaacadjei.me](https://isaacadjei.me)
- GitHub: [github.com/zaccesss](https://github.com/zaccesss) - **zaccesss with three s's**
- Branding line: **Access Granted. Success Unlocked.**
- Branding goes at the **bottom of every file only** - never at the top

### Structure

Every content file must follow this structure:

```markdown
# Title

**Difficulty:** 🟢 / 🟡 / 🔴 | **Time:** X minutes

One or two sentence description.

---

## Table of Contents

---

## Section heading

Content...

---

## Try It Yourself

Exercise...

---

## Common Mistakes

Mistakes and fixes...

---

## Summary

Key takeaways...

---

## Sources and Further Reading

- [Link title](URL) - brief description of what this link is for

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
```

### OS coverage

Show commands for all three operating systems. Label them clearly:

```
**Windows (Command Prompt or PowerShell):**
**Mac (Terminal):**
**Linux (Terminal):**
```

If a command is identical on all three, show it once without labels.

### Where to type commands

Every command block must specify where to type it - either in the OS label or in a sentence before the code block. Never leave the reader wondering.

### Bold and italic

- **Bold** for key terms, warnings, important values and emphasis
- _Italic_ for technical terms on first use, titles and light emphasis
- Never use bold or italic decoratively

### Links

- Use descriptive link text - never "click here" or "read more"
- Every link in Sources and Further Reading must include a brief description of what it is for

---

## File and Folder Naming

- All file names: **lowercase and hyphens only** - no spaces, underscores or capitals
- Follow the existing numbering: `01-`, `02-`, `03-` etc.
- Correct folder: `02-git/`, `03-github/`, `04-gitlab/`, `06-ides-and-editors/`, `07-terminal/`, `08-real-world/`, `09-reference/` or `10-resources/`
- Do not create new top-level folders without discussing it in an issue first

**Correct:** `02-git/06-branching.md`
**Wrong:** `02-git/Branching.md`, `02-git/branching_guide.md`, `02-git/branchingGuide.md`

---

## Commit Message Format

```
type: short description of what you did
```

| Type     | When to use                             |
| -------- | --------------------------------------- |
| `add`    | Adding new content or files             |
| `fix`    | Fixing a typo, error or broken link     |
| `update` | Improving or expanding existing content |
| `remove` | Removing something                      |
| `rename` | Renaming a file or folder               |

**Examples:**

```bash
git commit -m "add: cherry-pick exercise to 02-git/13-cherry-pick.md"
git commit -m "fix: correct typo in 03-github/04-issues.md"
git commit -m "update: improve merge conflict explanation"
```

---

## Pull Request Guidelines

- Give it a clear title describing what you changed
- Explain what you changed and why in the description
- Link related issues using `Closes #123` or `Relates to #123`
- Make sure your changes follow the writing style guide
- Check for typos and grammar errors before submitting
- Keep PRs focused - one topic per PR is much easier to review

---

## What Happens After You Submit

1. Your PR will be reviewed by [Isaac Adjei (Zaccess)](https://github.com/zaccesss)
2. You may receive feedback or requests for changes - this is normal and not personal
3. Once approved, your PR will be merged into `main`
4. Your name will be added to [HALL_OF_FAME.md](HALL_OF_FAME.md)

Thank you for helping make git-unlocked better.

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
