# Where Can You Do This? Platform Comparison

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

GitHub is available on six surfaces: the web browser, GitHub Desktop, the mobile app, the GitHub CLI, the browser-based editor (`github.dev`) and GitHub Codespaces. Each surface has different capabilities, different strengths and different ideal use cases. This file maps every common GitHub task to where you can do it, explains each surface in depth and helps you decide which combination to use for your workflow.

---

## Table of Contents

- [The six GitHub surfaces](#the-six-github-surfaces)
- [Feature matrix - what works where](#feature-matrix---what-works-where)
- [Web browser - github.com](#web-browser---githubcom)
- [GitHub Desktop](#github-desktop)
- [GitHub Mobile](#github-mobile)
- [GitHub CLI](#github-cli)
- [github.dev - the browser editor](#githubdev---the-browser-editor)
- [GitHub Codespaces](#github-codespaces)
- [Setting up each surface](#setting-up-each-surface)
- [Which surface for which task](#which-surface-for-which-task)
- [Recommended combinations by developer type](#recommended-combinations-by-developer-type)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## The Six GitHub Surfaces

| Surface                | Type                  | Install required     | OS                           |
| ---------------------- | --------------------- | -------------------- | ---------------------------- |
| **Web** (`github.com`) | Browser               | No                   | Any                          |
| **GitHub Desktop**     | App                   | Yes                  | 🪟 Windows, 🍎 Mac           |
| **GitHub Mobile**      | App                   | Yes                  | iOS, Android                 |
| **GitHub CLI** (`gh`)  | Terminal              | Yes                  | 🪟 Windows, 🍎 Mac, 🐧 Linux |
| **github.dev**         | Browser editor        | No                   | Any                          |
| **Codespaces**         | Cloud dev environment | No (runs in browser) | Any                          |

---

## Feature Matrix - What Works Where

| Task                         | Web | Desktop | Mobile       | CLI          | github.dev       | Codespaces      |
| ---------------------------- | --- | ------- | ------------ | ------------ | ---------------- | --------------- |
| View repository contents     | ✅  | ✅      | ✅           | ✅           | ✅               | ✅              |
| Clone a repository           | ✅  | ✅      | ❌           | ✅           | ❌               | ✅              |
| Create a repository          | ✅  | ✅      | ❌           | ✅           | ❌               | ❌              |
| Stage changes                | ❌  | ✅      | ❌           | ❌ (use git) | ✅               | ✅              |
| Commit changes               | ❌  | ✅      | ❌           | ❌ (use git) | ✅               | ✅              |
| Push and pull                | ❌  | ✅      | ❌           | ❌ (use git) | ✅ (commit only) | ✅              |
| Create a branch              | ✅  | ✅      | ❌           | ✅           | ✅               | ✅              |
| Edit a single file           | ✅  | ❌      | ❌           | ❌           | ✅               | ✅              |
| Edit multiple files          | ❌  | ✅      | ❌           | ❌           | ✅               | ✅              |
| Run a terminal               | ❌  | ❌      | ❌           | ✅ (native)  | ❌               | ✅              |
| Install packages             | ❌  | ❌      | ❌           | ❌           | ❌               | ✅              |
| Run code and tests           | ❌  | ❌      | ❌           | ❌           | ❌               | ✅              |
| Open a pull request          | ✅  | ✅      | ❌           | ✅           | ❌               | ✅ (via web)    |
| Review a pull request        | ✅  | ✅      | ✅           | ✅           | ❌               | ✅ (via web)    |
| Inline PR review comments    | ✅  | ✅      | ✅           | ✅           | ❌               | ✅ (via web)    |
| Approve/request changes      | ✅  | ✅      | ✅           | ✅           | ❌               | ✅ (via web)    |
| Merge a pull request         | ✅  | ❌      | ✅           | ✅           | ❌               | ✅ (via web)    |
| Create an issue              | ✅  | ❌      | ✅           | ✅           | ❌               | ✅ (via web)    |
| Comment on an issue          | ✅  | ❌      | ✅           | ✅           | ❌               | ✅ (via web)    |
| Close an issue               | ✅  | ❌      | ✅           | ✅           | ❌               | ✅ (via web)    |
| View Actions run logs        | ✅  | ❌      | ✅           | ✅           | ❌               | ✅ (via web)    |
| Trigger a workflow           | ✅  | ❌      | ❌           | ✅           | ❌               | ❌              |
| Manage releases              | ✅  | ❌      | ❌           | ✅           | ❌               | ❌              |
| Manage Gists                 | ✅  | ❌      | ❌           | ✅           | ❌               | ✅              |
| Manage SSH keys on account   | ✅  | ❌      | ❌           | ✅           | ❌               | ❌              |
| View notifications           | ✅  | ❌      | ✅           | ❌           | ❌               | ❌              |
| View contribution graph      | ✅  | ❌      | ✅           | ❌           | ❌               | ❌              |
| Repository settings          | ✅  | ❌      | ❌           | ✅ (limited) | ❌               | ❌              |
| Branch protection            | ✅  | ❌      | ❌           | ❌           | ❌               | ❌              |
| Organisation/team management | ✅  | ❌      | ✅ (limited) | ❌           | ❌               | ❌              |
| GitHub Actions configuration | ✅  | ❌      | ❌           | ❌           | ✅ (edit YAML)   | ✅ (edit + run) |
| Advanced search              | ✅  | ❌      | ✅           | ✅           | ❌               | ❌              |
| VS Code extensions           | ❌  | ❌      | ❌           | ❌           | ✅ (subset)      | ✅ (all)        |

---

## Web Browser - github.com

**What it is:** The full GitHub interface accessed through any web browser - Chrome, Firefox, Safari, Edge. No installation required.

**Best for:** Everything that is unique to GitHub as a platform - repository settings, branch protection rules, Actions configuration, code review, project management, organisation settings, security configuration and anything that does not require editing multiple files or running code.

**Unique capabilities (only possible on the web):**

- Full repository settings including branch protection rules, rulesets, webhooks and deploy keys
- GitHub Actions interface - viewing all runs, configuring secrets and environments, editing workflow YAML
- Organisation management - member roles, team management, billing, audit log, domain verification
- GitHub Projects v2 - full board/table/roadmap management
- Insights - traffic statistics, contributor graphs, network graphs, dependency graphs
- Security tab - Dependabot alerts, code scanning results, secret scanning alerts
- GitHub Marketplace - installing apps and Actions
- Discussions - creating polls, marking answers
- GitHub Pages configuration

**Limitations:**

- You cannot write and execute code from the web interface - only read, edit single files and view
- Editing multiple files requires creating separate commits for each file (unless using github.dev)
- The web editor for single files (`Edit this file` pencil icon) is basic compared to a proper editor

**Access:** `github.com` - no setup needed

---

## GitHub Desktop

**What it is:** A graphical desktop application for Git operations. It provides a visual interface for staging changes, committing, branching, pushing and pulling - replacing the terminal for all Git commands.

**Best for:** Beginners learning Git who are not yet comfortable with the terminal or developers who prefer a visual workflow for day-to-day Git operations.

**What it does particularly well:**

- **Visual diff view:** See exactly what changed in each file with a side-by-side or unified diff, colour-coded additions and deletions
- **Partial staging:** Stage specific files or specific lines within a file - without using `git add -p` in the terminal
- **Commit history graph:** Visual branch and merge history showing how branches relate
- **Merge conflict resolution:** Side-by-side visual conflict resolver - choose which version to keep per conflict marker
- **One-click pull requests:** Opens a pre-filled PR form in the browser from the Desktop app
- **Stash management:** Stash and unstash changes with a button click

**What it cannot do:**

- Rebase, cherry-pick, bisect or other advanced Git operations (use the terminal for these)
- Manage GitHub-specific features - issues, Actions, releases, settings
- Work on 🐧 Linux officially (a community-maintained port called `shiftkey/desktop` exists but is unofficial)
- Work without a GUI (it is a desktop application, not a terminal tool)

**Install:**

1. Go to `desktop.github.com`
2. Click **Download for macOS** or **Download for Windows**

🪟 **Windows** also: `winget install GitHub.GitHubDesktop`

🍎 **Mac** also: `brew install --cask github`

After installation:

1. Open GitHub Desktop
2. Click **Sign in to GitHub.com**
3. Complete the browser authentication
4. Choose **Clone a repository** and select from your GitHub repositories or add an existing local repository

**Workflow in GitHub Desktop:**

1. Make changes to files in any editor (VS Code, Notepad, etc.)
2. GitHub Desktop automatically detects the changes
3. Review the diff in the Changes panel
4. Write a commit message in the Summary field
5. Click **Commit to BRANCH-NAME**
6. Click **Push origin** to push to GitHub

---

## GitHub Mobile

**What it is:** The official iOS and Android app for GitHub. Primarily a review, triage and communication tool.

**Best for:** Staying on top of notifications, reviewing pull requests and managing issues from your phone when away from a computer.

**Unique strength:** Push notifications. The mobile app sends real-time push notifications to your phone for mentions, review requests, CI failures and security alerts. This is the primary reason to install it.

**What it does particularly well:**

- Reading and triaging the notification feed
- Reviewing PR diffs with inline commenting
- Approving or requesting changes on pull requests
- Managing issues (creating, commenting, labelling, closing)
- Browsing repository code with syntax highlighting
- Built-in TOTP authenticator for GitHub 2FA

**What it cannot do:**

- Edit or create files
- Push commits or create branches
- Manage repository settings
- Access Codespaces

**Install:**

- 🍎 iOS: App Store → search "GitHub"
- 🤖 Android: Google Play → search "GitHub"

Covered in full: `22-mobile.md`

---

## GitHub CLI

**What it is:** `gh`, the official command-line tool for the GitHub API layer. It manages PRs, issues, Actions, releases and more - without leaving the terminal.

**Best for:** Developers who work primarily in the terminal and want to manage GitHub without opening a browser and for scripting and automation.

**What it does that no other surface does:**

- Machine-readable JSON output (`--json` + `--jq`) for scripting
- Direct access to the GitHub REST and GraphQL APIs via `gh api`
- Watching workflow run logs live in the terminal (`gh run watch`)
- Checking out a PR branch in one command (`gh pr checkout 42`)
- Batch operations in shell scripts (close 50 stale issues, create releases for multiple repos)
- Copilot in the terminal (`gh copilot suggest` / `gh copilot explain`)

**What it cannot do:**

- Replace `git` - it handles GitHub's API layer, not local version control. Always use `git` for commits, branches and pushes.
- Provide a graphical interface

**Install:**

🪟 `winget install --id GitHub.cli`

🍎 `brew install gh`

🐧 `sudo apt install gh` or `sudo dnf install gh`

After install: `gh auth login`

Covered in full: `21-cli.md`

---

## github.dev - The Browser Editor

**What it is:** A full VS Code editor that runs entirely in your browser tab. No installation, no sign-in required to view public repositories. Press `.` (full stop) on any GitHub repository page to open it instantly.

**Best for:** Quick edits, documentation fixes, reviewing code with syntax highlighting and multi-file navigation, when you do not have your tools available but need to make a change.

**How to access:**

- Press `.` on any GitHub repository page
- Change `github.com` to `github.dev` in the URL
- Click **Edit in github.dev** from the web editor for a single file

**What it can do:**

- Edit any file with full VS Code syntax highlighting, autocomplete and multi-cursor support
- Navigate the entire repository file tree in the Explorer panel
- Search across all files in the repository (Ctrl+Shift+F / Cmd+Shift+F)
- Use a subset of VS Code extensions (those that do not require a local runtime)
- Create and switch branches
- Stage individual files and commit with a message
- View Git diff for staged changes before committing
- Use the Source Control panel (same as VS Code)

**What it cannot do:**

- Run a terminal - there is no terminal access at all
- Install packages (`npm install`, `pip install` etc.)
- Run code, tests or build scripts
- Use VS Code extensions that require a local Node.js or Python runtime
- Preview a running web application

**Limitations:**

The `github.dev` editor commits directly to GitHub - there is no local working copy. It is best for changes you can verify by reading the code, not changes that require running the application. For running and testing code, use Codespaces.

**Port to Codespaces:**

From `github.dev`, press `>` (greater-than sign) to open the command palette and select **Continue Working in New Codespace**. This opens a full Codespace with your current changes preserved.

---

## GitHub Codespaces

**What it is:** A full cloud development environment running on a Linux virtual machine hosted by GitHub. It runs VS Code (or any JetBrains IDE via the JetBrains Gateway) in your browser or connected to your local VS Code installation.

**Best for:** Working on a project without needing to set up a local development environment, contributing to a repository with complex dependencies, working from a machine you do not own (school/library computer), onboarding to a new project quickly.

**What it can do that no other surface can:**

- A full Linux terminal with root access
- Install any package with `apt`, `npm`, `pip`, `cargo` etc.
- Run code and tests
- Run a web server and preview it in your browser (port forwarding)
- Use all VS Code extensions including those requiring a local runtime
- Keep a persistent environment between sessions (files and installed packages are saved)
- Pre-build the environment so it starts instantly

**What it cannot do:**

- Manage GitHub settings - still use the web for repository and organisation configuration
- Replace `gh` for GitHub API operations - though Codespaces has `gh` pre-installed

**Limits:**

| Plan               | Free core-hours/month | Storage       |
| ------------------ | --------------------- | ------------- |
| Free               | 120 core-hours        | 15 GB         |
| Pro (and students) | 180 core-hours        | 20 GB         |
| Paid               | Billed per usage      | Billed per GB |

A 2-core machine uses 1 core-hour per clock hour. So 120 core-hours = 60 hours of runtime on a 2-core machine per month.

**Open a Codespace:**

1. Go to any GitHub repository
2. Click the green **Code** button
3. Click the **Codespaces** tab
4. Click **Create codespace on main**

Or via CLI:

```bash
gh codespace create --repo OWNER/REPO
gh codespace list
gh codespace code          # open in your local VS Code
gh codespace ssh           # SSH into the running codespace
```

**Dev containers:**

A repository can include a `.devcontainer/devcontainer.json` file that configures the Codespace environment - what base image to use, which VS Code extensions to install, which ports to forward, which post-create commands to run (like `npm install`).

```json
{
  "name": "My Project",
  "image": "mcr.microsoft.com/devcontainers/python:3.12",
  "postCreateCommand": "pip install -r requirements.txt",
  "customizations": {
    "vscode": {
      "extensions": ["ms-python.python", "github.copilot"]
    }
  },
  "forwardPorts": [8000]
}
```

With a dev container, every developer and every Codespace gets the exact same environment. No more "works on my machine".

**Stop a Codespace when not using it:**

Codespaces continue to use core-hours while running even if you close the browser. Stop the Codespace when you are done:

- In the browser: Click the three-dot menu on the Codespace → **Stop codespace**
- Via CLI: `gh codespace stop`
- Set auto-stop timeout: Go to `github.com/settings/codespaces` → **Default idle timeout**

---

## Setting Up Each Surface

### Web browser

No setup. Go to `github.com` and sign in.

### GitHub Desktop

```
1. Download from desktop.github.com
2. Open and sign in to GitHub.com
3. Clone your first repository
```

🪟 Or: `winget install GitHub.GitHubDesktop`

🍎 Or: `brew install --cask github`

### GitHub Mobile

```
1. App Store (iOS) or Google Play (Android) → search "GitHub"
2. Install and sign in
```

### GitHub CLI

🪟 `winget install --id GitHub.cli` then `gh auth login`

🍎 `brew install gh` then `gh auth login`

🐧 `sudo apt install gh` then `gh auth login`

After auth, also run: `gh auth setup-git` to configure `git` to use `gh` for credentials.

### github.dev

No setup. On any repository, press `.` or change the URL from `github.com` to `github.dev`.

### Codespaces

No install needed - runs in the browser. Go to any repository → green **Code** button → **Codespaces** tab → **Create codespace on main**.

For the best experience, install the **GitHub Codespaces** VS Code extension to open Codespaces in your local VS Code instead of the browser.

---

## Which Surface for Which Task

| Task                                      | Best surface                       | Why                                                         |
| ----------------------------------------- | ---------------------------------- | ----------------------------------------------------------- |
| Fix a typo in documentation               | github.dev (press `.`)             | No setup, commit directly, see the whole file               |
| Write and run new code                    | VS Code locally + git + gh         | Full tooling, fastest iteration                             |
| Write code without a local setup          | Codespaces                         | Full environment in the browser                             |
| Review a PR thoroughly                    | Web browser                        | Best context controls, comment threading, approval workflow |
| Review a small PR away from desk          | GitHub Mobile                      | Push notifications, inline comments on phone                |
| Check CI status on the go                 | GitHub Mobile                      | Push notifications, run logs visible                        |
| Manage branch protection rules            | Web browser                        | Only available on the web                                   |
| Create a release with binaries            | GitHub CLI                         | `gh release create v1.0.0 dist/*` in one command            |
| Automate GitHub in a shell script         | GitHub CLI                         | `--json` + `--jq` for scripting                             |
| Learn Git visually                        | GitHub Desktop                     | Visual diff, staging, commit graph                          |
| Configure repository settings             | Web browser                        | All settings only available on the web                      |
| Manage organisation members               | Web browser (or Mobile for basics) | Full controls only on the web                               |
| Quick code edit on a public repo          | github.dev                         | No clone needed, immediate                                  |
| Contribute to a project with complex deps | Codespaces                         | Pre-configured dev container                                |
| Onboard a new team member                 | Codespaces with dev container      | Instant identical environment for everyone                  |
| Batch operations on many repos            | GitHub CLI                         | Scriptable with loops and JSON output                       |

---

## Recommended Combinations by Developer Type

**Student/beginner learning Git:**

- **GitHub Desktop** for Git operations (visual, forgiving, shows what git commands would do)
- **Web browser** for issues, PRs and repository settings
- **GitHub Mobile** for staying on top of notifications

**Intermediate developer (comfortable with terminal):**

- **VS Code** with local `git` for writing code
- **GitHub CLI** (`gh`) for managing PRs, issues and releases from the terminal
- **Web browser** for settings, code review and anything that needs the GitHub UI
- **github.dev** for quick fixes when away from your main machine

**Advanced/power user:**

- **Terminal** with `git` and `gh` for everything possible in the terminal
- **Web browser** for repository/organisation management
- **Codespaces** for projects that need specific environments or onboarding
- **GitHub Mobile** for push notification awareness

**Student on the GitHub Student Developer Pack:**

- **GitHub Copilot** in VS Code (free with the pack)
- **Codespaces** for working on library computers or tablets (180 core-hours free with the pack)
- **GitHub CLI** for scripting assignments and automation
- **GitHub Mobile** for staying notified

---

## Try It Yourself

Open the same repository from four different surfaces and notice what each one shows you differently.

**Surface 1 - Web browser:**

```
1. Go to github.com/zaccesss/git-unlocked
2. Click around: Issues, Pull requests, Actions, Insights tabs
3. Open any file and click the pencil (edit) icon - note: this edits one file at a time
```

**Surface 2 - github.dev:**

```
1. On the same repository page, press the . (full stop) key
2. VS Code opens in your browser
3. Use Ctrl+Shift+F / Cmd+Shift+F to search across all files
4. Press . or navigate back to return to github.com
```

**Surface 3 - GitHub CLI:**

```bash
gh repo view zaccesss/git-unlocked
gh issue list --repo zaccesss/git-unlocked --state open
```

**Surface 4 - GitHub Mobile:**

```
1. Open the GitHub app
2. Search for zaccesss/git-unlocked
3. Star the repository
4. Browse the file list - notice the syntax highlighting
```

---

## Common Mistakes

**Using github.dev when you need to run code.** `github.dev` has no terminal - you cannot run tests or preview a web app. Use Codespaces if you need to execute code.

**Using Codespaces without stopping them.** Codespaces keep consuming core-hours while running, even if you close the browser. Always stop a Codespace when you are finished. Set an auto-stop timeout in your Codespaces settings.

**Expecting GitHub Desktop to handle advanced Git operations.** GitHub Desktop does not support rebase, cherry-pick, bisect or interactive staging (`git add -p`). For these, use the terminal with git directly alongside Desktop.

**Using the web browser to edit multiple files.** Each file edit through the web editor creates a separate commit. For multi-file changes, use github.dev (multiple files, one commit) or clone and use a local editor.

**Not installing `gh` alongside `git`.** Most developers who use the terminal use `git` but not `gh`. Installing `gh` adds PR creation, issue management and Actions monitoring to your terminal workflow with no extra overhead.

**Using Codespaces for every project.** The 120/180 free core-hours go faster than expected if you use Codespaces as your default development environment. Use them for projects that genuinely need a configured environment, onboarding or working from a non-personal machine. Use a local setup for day-to-day work.

---

## Summary

- GitHub has six surfaces: **web**, **Desktop**, **Mobile**, **CLI**, **github.dev** and **Codespaces**
- The **web** is the most capable for GitHub-specific management - all settings, branch protection, Actions configuration and organisation management are web-only
- **GitHub Desktop** is the best starting point for beginners - visual staging, diffs and commit history without a terminal
- **GitHub Mobile** is for review and triage on the go; its main value is push notifications
- **GitHub CLI** (`gh`) is for power users who want GitHub in the terminal and in automation scripts; use `--json` + `--jq` for scripting
- **github.dev** (press `.` on any repo) gives a full VS Code editor in the browser with no install - for quick edits with no terminal
- **Codespaces** is a full cloud Linux dev environment - use it when you cannot set up locally, when a project has complex dependencies or when onboarding a team
- For most developers: **VS Code locally + git + gh** for daily work, **web browser** for settings and organisation management, **Mobile** for notifications
- Students: use **Codespaces** (180 free core-hours via the Developer Pack) for projects that need specific environments

---

## Sources and Further Reading

- [GitHub Desktop](https://desktop.github.com) - download and documentation
- [GitHub Desktop documentation](https://docs.github.com/en/desktop) - full Desktop guide
- [GitHub Mobile documentation](https://docs.github.com/en/get-started/using-github/github-mobile) - official guide
- [GitHub CLI documentation](https://cli.github.com/manual/) - full command reference
- [github.dev documentation](https://docs.github.com/en/codespaces/the-githubdev-web-based-editor) - browser editor guide
- [Codespaces documentation](https://docs.github.com/en/codespaces) - full Codespaces reference including dev containers
- [Dev containers specification](https://containers.dev) - devcontainer.json format reference

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
