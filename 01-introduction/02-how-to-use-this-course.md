# How to use this course

**Difficulty:** 🟢 Beginner | **Time:** 5 minutes

How git-unlocked is organised, where to start, and how to get the most out of it.

---

## Table of contents

- [How the course is organised](#how-the-course-is-organised)
- [Where to start](#where-to-start)
- [Difficulty levels](#difficulty-levels)
- [OS coverage](#os-coverage)
- [Callout key](#callout-key)
- [How to get the most out of this course](#how-to-get-the-most-out-of-this-course)
- [Getting help](#getting-help)
- [Sources and Further Reading](#sources-and-further-reading)

---

## How the course is organised

git-unlocked is split into folders. Each folder covers a distinct area. You do not have to work through them in order - jump to whatever you need.

| Folder                | What is inside                                                                            |
| --------------------- | ----------------------------------------------------------------------------------------- |
| `introduction/`       | Start here - concepts, setup and how to navigate                                          |
| `git/`                | Everything about Git itself, from `git init` to internals                                 |
| `github/`             | Everything about GitHub - accounts, repos, PRs, Actions, security and more                |
| `gitlab/`             | Everything about GitLab - merge requests, CI/CD, Duo AI and self-hosting                  |
| `other-platforms/`    | Bitbucket, Azure DevOps, Gitea, Forgejo and Codeberg at full depth                        |
| `ides-and-editors/`   | Git integration in VS Code, JetBrains, Neovim, Cursor and every other major editor        |
| `terminal/`           | Shell setup, Git aliases, lazygit, delta, fzf, bat, tig and the full terminal toolchain   |
| `real-world/`         | Practical scenarios - open source contribution, team workflows, GitOps, disaster recovery |
| `reference/`          | Cheatsheet, glossary, common mistakes and security reference                              |
| `resources/`          | Curated external resources - books, videos, interactive tools, communities                |
| `first-contribution/` | Make your very first pull request here                                                    |

Files are numbered within each folder so you always know the recommended reading order. You can follow it or skip around freely.

---

## Where to start

**Never used Git or version control before?**

Work through these in order:

1. [What is version control?](04-version-control-concepts.md) - the concepts before any commands
2. [Setting up](03-setting-up.md) - install Git and configure your identity
3. [How Git works](../02-git/03-how-git-works.md) - the mental model
4. [Core concepts](../02-git/04-core-concepts.md) - repository, working tree, staging area, HEAD
5. [Basic commands](../02-git/05-basic-commands.md) - your first real Git commands

**Know the basics and want to go further?**

- [Branching](../02-git/06-branching.md) - work on features without breaking things
- [Merging](../02-git/07-merging.md) - combine your work back together
- [Pull requests](../03-github/08-pull-requests.md) - collaborate professionally on GitHub
- [Rebase](../02-git/13-rebase.md) - the most powerful history-management tool
- [Real-world workflows](../08-real-world/02-working-in-a-team.md) - how professional teams actually work

**Ready for advanced topics?**

- [Git internals](../02-git/25-git-internals-advanced.md) - packfiles, the object model, plumbing commands
- [GitOps](../08-real-world/03-gitops-workflows.md) - ArgoCD, Flux and Git as infrastructure source of truth
- [Monorepo patterns](../08-real-world/04-monorepo-patterns.md) - partial clone, sparse checkout, Nx, Turborepo, Bazel
- [Security](../09-reference/security.md) - gitleaks, TruffleHog, commit signing, supply chain security
- [Recovering from disasters](../08-real-world/06-recovering-from-disasters.md) - reflog, lost commits, committed secrets

**Just need a reference?**

- [Cheatsheet](../09-reference/cheatsheet.md) - every command organised by task
- [Glossary](../09-reference/glossary.md) - every term defined
- [Common mistakes](../09-reference/common-mistakes.md) - errors and exact fixes

**Want to make your first contribution right now?**

Go to [first-contribution/](../11-first-contribution/README.md). It takes less than ten minutes and uses a real GitHub workflow.

---

## Difficulty levels

Every file is labelled with one or more difficulty markers so you know what to expect before you start reading.

🟢 **Beginner** - no prior experience needed with the topic. Plain language, step-by-step instructions, nothing assumed.

🟡 **Intermediate** - you are comfortable with basic Git commands and can navigate a terminal. Some concepts build on earlier material.

🔴 **Advanced** - production-level depth. Complex scenarios, internals, edge cases. May reference concepts from multiple earlier files.

A file labelled 🟢🟡 covers beginner material first and builds toward intermediate. A file labelled 🟡🔴 starts at intermediate and reaches advanced depth by the end.

---

## OS coverage

Every file in this course covers 🪟 Windows, 🍎 Mac and 🐧 Linux side by side. No platform is treated as the default.

When a command is identical on all three platforms, it appears once without any label. When commands differ, they are labelled clearly:

```bash
# 🪟 Windows (PowerShell)
$env:PATH += ";C:\Program Files\Git\bin"

# 🍎 Mac / 🐧 Linux
export PATH="$PATH:/usr/local/bin"
```

Every command block specifies where to type it - either in the OS label above it or in a sentence before the block. You will never be left wondering which terminal to use.

---

## Callout key

Callout boxes highlight information that deserves extra attention. The course uses five types:

> [!NOTE]
> Worth knowing - useful context that is not critical but often answers a question you are about to have.

> [!TIP]
> A technique or shortcut that makes something easier or faster.

> [!IMPORTANT]
> A key point that will significantly improve your workflow or understanding.

> [!WARNING]
> Read this before continuing. Ignoring it may cause problems that are annoying to fix.

> [!CAUTION]
> A security or data safety issue. Ignoring it can lead to lost data, exposed credentials or broken repositories.

---

## How to get the most out of this course

**Type the commands yourself.** Reading about Git is useful. Typing the commands yourself is how you actually learn it. Every file has a Try It Yourself section with practical exercises. Do them.

**Use the reference files.** The [cheatsheet](../09-reference/cheatsheet.md), [glossary](../09-reference/glossary.md) and [common mistakes](../09-reference/common-mistakes.md) files are designed to be used repeatedly, not just read once. Bookmark them.

**Follow the links.** Files cross-reference each other throughout. When a concept comes from an earlier file, there is a link. Following those links builds a more complete picture than reading any single file in isolation.

**Contribute back.** If you spot a mistake, find a better explanation, or want to add content - open a pull request. Read [CONTRIBUTING.md](../CONTRIBUTING.md) first. Everyone who contributes is listed in [HALL_OF_FAME.md](../HALL_OF_FAME.md).

---

## Getting help

**Something is broken or incorrect in the course.**
Open an issue using the [bug report template](https://github.com/zaccesss/git-unlocked/issues/new?template=bug_report.md).

**You want to suggest new content.**
Open an issue using the [content suggestion template](https://github.com/zaccesss/git-unlocked/issues/new?template=content_suggestion.md).

**You are stuck on a Git problem.**
The [common mistakes](../09-reference/common-mistakes.md) file covers the most frequent issues with exact fix commands. If your problem is not there, [Stack Overflow's git tag](https://stackoverflow.com/questions/tagged/git) has answers to almost every Git question ever asked. The [GitHub Community Discussions](https://github.com/orgs/community/discussions) board is also active and helpful.

**You want to discuss the course or connect with other learners.**
The [GitHub Discussions tab](https://github.com/zaccesss/git-unlocked/discussions) on this repository is the right place for questions, ideas and conversation.

---

## Sources and Further Reading

- [Official Git documentation](https://git-scm.com/doc) - the authoritative reference for all Git commands
- [Pro Git book](https://git-scm.com/book/en/v2) - the free official Git book, available online in multiple languages
- [GitHub documentation](https://docs.github.com) - everything about using GitHub
- [GitLab documentation](https://docs.gitlab.com) - everything about using GitLab
- [resources/index.md](../10-resources/index.md) - 120+ curated resources for going further

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
