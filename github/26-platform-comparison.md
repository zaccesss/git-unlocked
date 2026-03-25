# Where Can You Do This? Platform Comparison

**Difficulty:** 🟢 Beginner | **Time:** 15 minutes

GitHub is available on four surfaces: the web browser, GitHub Desktop, the mobile app and the GitHub CLI. Each surface has different capabilities. This file maps every common GitHub task to where you can do it, so you always know which tool to reach for.

---

## Table of Contents

- [The four GitHub surfaces](#the-four-github-surfaces)
- [Feature matrix - what works where](#feature-matrix---what-works-where)
- [Web browser - github.com](#web-browser---githubcom)
- [GitHub Desktop](#github-desktop)
- [GitHub Mobile](#github-mobile)
- [GitHub CLI](#github-cli)
- [github.dev - the browser editor](#githubdev---the-browser-editor)
- [GitHub Codespaces](#github-codespaces)
- [Which surface should I use](#which-surface-should-i-use)
- [Try It Yourself](#try-it-yourself)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## The Four GitHub Surfaces

| Surface | What it is | Install required |
|---|---|---|
| **Web** (`github.com`) | Full GitHub in the browser | No |
| **GitHub Desktop** | GUI app for Git operations | Yes |
| **GitHub Mobile** | iOS and Android app | Yes |
| **GitHub CLI** (`gh`) | Command-line interface | Yes |
| **github.dev** | VS Code in the browser | No |
| **Codespaces** | Full cloud dev environment | No |

---

## Feature Matrix - What Works Where

| Task | Web | Desktop | Mobile | CLI |
|---|---|---|---|---|
| View repository contents | Yes | Yes | Yes | Yes |
| Clone a repository | Yes | Yes | No | Yes |
| Create a repository | Yes | Yes | No | Yes |
| Stage and commit changes | No | Yes | No | No (use git) |
| Push and pull | No | Yes | No | No (use git) |
| Create a branch | Yes | Yes | No | Yes |
| Edit a file | Yes | No | No | No |
| Open a pull request | Yes | Yes | No | Yes |
| Review a pull request | Yes | Yes | Yes | Yes |
| Merge a pull request | Yes | No | Yes | Yes |
| Create an issue | Yes | No | Yes | Yes |
| Comment on an issue | Yes | No | Yes | Yes |
| Close an issue | Yes | No | Yes | Yes |
| View Actions run logs | Yes | No | Yes | Yes |
| Trigger an Actions workflow | Yes | No | No | Yes |
| Manage releases | Yes | No | No | Yes |
| Manage Gists | Yes | No | No | Yes |
| Manage SSH keys | Yes | No | No | Yes |
| View notifications | Yes | No | Yes | No |
| View contribution graph | Yes | No | Yes | No |
| Configure branch protection | Yes | No | No | No |
| Configure repository settings | Yes | No | No | Yes (limited) |
| Search code globally | Yes | No | Yes | Yes |
| Run a terminal | No | No | No | Yes (native terminal) |
| Write and run code | No | No | No | No (use Codespaces) |

---

## Web Browser - github.com

**Best for:** Everything that is specific to GitHub as a platform - repository settings, branch protection, Actions configuration, code review, issue management and organisation management.

**What it does uniquely well:**

- Full repository settings and security configuration
- All Actions UI - viewing runs, secrets, environments
- Organisation and team management
- Advanced search with qualifiers
- GitHub Projects boards, tables and roadmaps
- Insights and traffic stats
- Dependency graphs and Dependabot alerts

**Limitation:** You cannot commit from github.com itself without using the built-in single-file editor, `github.dev` or a Codespace.

**Open in browser:** `github.com`

---

## GitHub Desktop

**Best for:** Developers who prefer a visual Git interface over the command line, particularly beginners learning Git workflows.

**What it does:**

- Clone, create and manage local repositories
- See file changes visually with a side-by-side diff viewer
- Stage specific files or individual lines (partial staging)
- Commit with a message and description
- Push, pull and fetch
- Create and switch branches
- View commit history with a visual graph
- Resolve merge conflicts with a visual merge tool
- Open pull requests (redirects to the web browser)

**What it does not do:**

- Rebase, cherry-pick or other advanced Git operations
- Manage GitHub-specific features (issues, Actions, settings)
- Work on Linux officially (a community port exists)

**Install:** Download from `desktop.github.com`. Available for 🪟 Windows and 🍎 Mac.

---

## GitHub Mobile

**Best for:** Staying on top of notifications, reviewing PRs and managing issues when away from a computer.

**What it does uniquely well:**

- Push notifications for mentions, review requests and CI results
- Reading diffs and leaving line-level review comments
- Approving and merging pull requests
- Managing issues on the go
- Bookmarking notifications for later

**What it cannot do:**

- Write or run code
- Push commits
- Create branches
- Manage repository or organisation settings in depth

**Covered in full:** `22-mobile.md`

---

## GitHub CLI

**Best for:** Developers who live in the terminal and want to manage GitHub without switching to a browser.

**What it does uniquely well:**

- Create repositories, issues, PRs and releases from the terminal
- Checkout a PR branch in one command (`gh pr checkout 15`)
- Scripting and automation using `--json` and `--jq` output
- Triggering workflows and watching run logs live in the terminal
- Managing Gists from scripts

**What it does not do:**

- Replace `git` - it handles GitHub's API layer, not local version control
- Provide a GUI of any kind

**Covered in full:** `21-cli.md`

---

## github.dev - The Browser Editor

Press `.` (full stop) on any GitHub repository page to open it in `github.dev` - a full VS Code experience running entirely in your browser tab, with no installation needed.

**What it can do:**

- Edit files with VS Code's editor, syntax highlighting and autocomplete
- Search across the repository
- Use a subset of VS Code extensions that work in the browser
- Commit changes directly to any branch
- Create and switch branches

**What it cannot do:**

- Run a terminal
- Install packages
- Run code, tests or build scripts
- Use extensions that require a local runtime

**When to use it:** Quick documentation edits, fixing typos, adding a file or making a small code change without setting up a local environment.

---

## GitHub Codespaces

Codespaces is a full cloud development environment. It gives you a complete Linux machine with VS Code, a terminal, installed tools and your repository - all accessible from your browser or connected to your local VS Code.

**What it can do:**

- Run a full Linux terminal
- Install packages and dependencies with any package manager
- Build and run code
- Run tests
- Use all VS Code extensions including those requiring a runtime
- Expose ports from the cloud machine (for web apps)
- Persist the environment between sessions

**Limits on the free plan:**

- 120 core-hours per month (a 2-core machine runs 60 hours)
- 15 GB storage
- Students on the Developer Pack get 180 core-hours

**Open a Codespace:**

1. Go to any repository
2. Click the green **Code** button
3. Click the **Codespaces** tab
4. Click **Create codespace on main**

Or from the CLI:

```bash
gh codespace create --repo OWNER/REPO
gh codespace list
gh codespace code          # open in local VS Code
```

---

## Which Surface Should I Use

| Situation | Recommended surface |
|---|---|
| Learning Git, new to the command line | GitHub Desktop + git in terminal |
| Day-to-day development | VS Code + git + gh CLI |
| Quick documentation fix | github.dev (press `.` on the repo) |
| Working on a computer without your tools | Codespaces |
| On your phone, a PR needs review | GitHub Mobile |
| Automating GitHub tasks in a script | GitHub CLI with `--json` |
| Configuring branch protection or Actions | Web browser |
| Managing a team or organisation | Web browser |
| Reviewing a large PR | Web browser |

---

## Try It Yourself

Try accessing the same repository from three different surfaces.

**Step 1 - Web:** Go to `github.com/zaccessss/git-unlocked` in your browser. Browse the file list.

**Step 2 - github.dev:** On that same page, press the `.` key. VS Code opens in the browser with syntax highlighting. Press `.` again or click the back button to return to github.com.

**Step 3 - CLI:**

```bash
gh repo view zaccessss/git-unlocked
```

**Step 4 - Mobile:** Open the GitHub app on your phone. Search for `zaccessss/git-unlocked`. Star the repository.

All four surfaces are looking at the same repository data.

---

## Summary

- GitHub has six surfaces: **web**, **Desktop**, **Mobile**, **CLI**, **github.dev** and **Codespaces**
- **Web** is the most capable for GitHub-specific management (settings, Actions, org management)
- **Desktop** is best for beginners who want a visual Git workflow on Windows or Mac
- **Mobile** is for review and triage on the go - not for coding
- **CLI** is for power users who want GitHub in the terminal and in automation scripts
- **github.dev** (press `.` on any repo) gives a quick browser VS Code editor with no terminal
- **Codespaces** is a full cloud dev environment - use it when you cannot or do not want to set up locally
- For most active developers: **git + gh + VS Code locally**, with the web browser for settings

---

## Sources and Further Reading

- [GitHub Desktop](https://desktop.github.com) - download and documentation
- [GitHub Mobile documentation](https://docs.github.com/en/get-started/using-github/github-mobile) - official guide
- [GitHub CLI documentation](https://cli.github.com/manual/) - full command reference
- [github.dev documentation](https://docs.github.com/en/codespaces/the-githubdev-web-based-editor) - browser editor guide
- [Codespaces documentation](https://docs.github.com/en/codespaces) - full Codespaces reference

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
