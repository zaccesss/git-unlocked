# GitHub Desktop

**Difficulty:** 🟢 Beginner | **Time:** 30 minutes

GitHub Desktop is a free, official graphical application that lets you use Git and GitHub without ever typing a command. Instead of memorising `git commit`, `git push` and `git branch`, you click buttons, see your changes visually and manage your repositories through a clean interface. It is one of the best ways to get started with Git and GitHub, and many experienced developers continue using it alongside the command line for the visual diff view and streamlined commit workflow.

---

## Table of Contents

- [What is GitHub Desktop?](#what-is-github-desktop)
- [GitHub Desktop vs the command line](#github-desktop-vs-the-command-line)
- [System requirements](#system-requirements)
- [Installing on Windows](#installing-on-windows)
- [Installing on Mac](#installing-on-mac)
- [Installing on Linux](#installing-on-linux)
- [Signing in to your GitHub account](#signing-in-to-your-github-account)
- [The GitHub Desktop interface](#the-github-desktop-interface)
- [Cloning a repository](#cloning-a-repository)
- [Creating a new repository](#creating-a-new-repository)
- [Adding an existing local repository](#adding-an-existing-local-repository)
- [The daily workflow](#the-daily-workflow)
- [Branching in GitHub Desktop](#branching-in-github-desktop)
- [Merging branches](#merging-branches)
- [Resolving merge conflicts](#resolving-merge-conflicts)
- [Working with pull requests](#working-with-pull-requests)
- [Fetching, pulling and pushing](#fetching-pulling-and-pushing)
- [Viewing history and blame](#viewing-history-and-blame)
- [Stashing changes](#stashing-changes)
- [Undoing commits](#undoing-commits)
- [Co-authoring commits](#co-authoring-commits)
- [Opening in your editor or terminal](#opening-in-your-editor-or-terminal)
- [GitHub Desktop vs GitHub.com](#github-desktop-vs-githubcom)
- [Keyboard shortcuts](#keyboard-shortcuts)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is GitHub Desktop?

GitHub Desktop is an open source, Electron-based application built by GitHub. It provides a graphical user interface for Git operations and deep integration with GitHub.com. It is written in TypeScript and React, and its source code is publicly available at `github.com/desktop/desktop`.

**What GitHub Desktop lets you do:**

- Clone repositories from GitHub.com to your local machine with one click
- Create new repositories locally and publish them to GitHub
- See exactly what changed in every file before committing
- Stage specific files or even specific lines within a file
- Commit changes with a message and description
- Create, switch and delete branches
- Merge branches and resolve conflicts with a visual editor
- Push and pull from GitHub
- View commit history with a visual graph
- Open pull requests and review them
- Co-author commits with teammates
- Open the repository in your preferred code editor or terminal with one click

---

## GitHub Desktop vs the Command Line

GitHub Desktop and the Git command line are not competing tools - they are complementary. Understanding when to use each makes you more productive.

| Situation | GitHub Desktop | Command Line |
|---|---|---|
| Reviewing what changed before committing | Excellent visual diff | `git diff` (text only) |
| Staging specific lines (partial staging) | Easy - click individual lines | `git add -p` (requires keyboard) |
| Committing and pushing | One-click workflow | Two commands minimum |
| Creating and switching branches | Dropdown menus | `git switch -c` / `git switch` |
| Visualising commit history | Built-in visual graph | `git log --oneline --graph` |
| Interactive rebase | Not supported | `git rebase -i` |
| Complex scripting or automation | Not possible | Full shell access |
| Using Git hooks | Not visible or configurable | Directly accessible |
| Advanced operations (bisect, filter-repo) | Not supported | Full support |
| Working on a remote server | Not applicable | SSH + command line |

**Recommendation:** Use GitHub Desktop when you are starting out and for your everyday commit and push workflow. Learn the command line alongside it - many professional tasks require it and understanding both makes you a stronger developer. Covered in this course's `terminal/` folder.

---

## System Requirements

**Windows:**
- Windows 10 64-bit (version 1903 or later) or Windows 11
- x64 or ARM64 processor

**Mac:**
- macOS 11 (Big Sur) or later
- Intel or Apple Silicon (M1/M2/M3/M4/M5) - separate installers available

**Linux:**
- No official support from GitHub
- A community-maintained fork is available (see [Installing on Linux](#installing-on-linux))

---

## Installing on Windows

**Method 1 - Direct download (recommended for most users):**

1. Go to [desktop.github.com/download](https://desktop.github.com/download)
2. Click **Download for Windows**
3. Run the downloaded `.exe` installer
4. GitHub Desktop installs automatically and launches when complete
5. No administrator privileges required - installs to your user profile

**Method 2 - winget (Windows Package Manager):**

Open PowerShell or Command Prompt and run:

```powershell
winget install github-desktop
```

**Method 3 - Chocolatey:**

```powershell
choco install github-desktop
```

**Method 4 - MSI installer (for organisation-wide deployment):**

Download the MSI installer from [desktop.github.com/download](https://desktop.github.com/download) for deploying across multiple machines via group policy.

**Beta channel:**

To access new features before they are released, download the beta version from the same download page. Beta and stable can be installed side by side.

GitHub Desktop updates automatically in the background. You will see a notification when an update is ready to install.

---

## Installing on Mac

**Method 1 - Direct download:**

1. Go to [desktop.github.com/download](https://desktop.github.com/download)
2. Choose the correct version:
   - **Download for Mac with Apple Silicon** (M1, M2, M3, M4, M5 chips)
   - **Download for Mac with Intel chip** (older Macs)
3. Open the downloaded `.dmg` file
4. Drag **GitHub Desktop** to your **Applications** folder
5. Open it from Applications or Spotlight

> [!NOTE]
> If macOS shows a warning that the app is from an unidentified developer, go to **System Settings → Privacy and Security** and click **Open Anyway**. GitHub Desktop is a signed application from GitHub, Inc. and is safe to open.

**Method 2 - Homebrew:**

```bash
brew install --cask github
```

To update:

```bash
brew upgrade --cask github
```

**Apple Silicon note:** GitHub Desktop has native Apple Silicon support. If you accidentally downloaded the Intel version, it will run under Rosetta 2 but the native Apple Silicon version is faster. Check **GitHub Desktop → About GitHub Desktop** to confirm which architecture you are running.

---

## Installing on Linux

GitHub does not officially support Linux. However, a community-maintained fork by developer **Brendan Forster (shiftkey)** provides up-to-date Linux builds that work well.

> [!NOTE]
> The shiftkey fork is not maintained by GitHub. It generally stays close to the official version but may occasionally lag behind on new features. It works well for everyday use.

**Ubuntu/Debian (.deb):**

```bash
wget -qO - https://apt.packages.shiftkey.dev/gpg.key | gpg --dearmor | sudo tee /usr/share/keyrings/shiftkey-packages.gpg > /dev/null

sudo sh -c 'echo "deb [arch=amd64 signed-by=/usr/share/keyrings/shiftkey-packages.gpg] https://apt.packages.shiftkey.dev/ubuntu/ any main" > /etc/apt/sources.list.d/shiftkey-packages.list'

sudo apt update && sudo apt install github-desktop
```

**Red Hat/Fedora/openSUSE (.rpm):**

```bash
sudo rpm --import https://rpm.packages.shiftkey.dev/gpg.key

sudo sh -c 'echo -e "[shiftkey]\nname=GitHub Desktop\nbaseurl=https://rpm.packages.shiftkey.dev/rpm/\nenabled=1\ngpgcheck=1\ngpgkey=https://rpm.packages.shiftkey.dev/gpg.key" > /etc/yum.repos.d/shiftkey-packages.repo'

sudo dnf install github-desktop
```

**Direct download:** Visit `github.com/shiftkey/desktop/releases` for `.deb`, `.rpm` and `.AppImage` packages.

**Alternatives for Linux users who prefer a GUI:**

- **GitKraken** - full-featured Git GUI with native Linux support
- **Sourcetree** - not available on Linux
- **VS Code Source Control panel** - built into VS Code, works on Linux

---

## Signing In to Your GitHub Account

When GitHub Desktop first launches, it prompts you to sign in.

**Step 1.** Click **Sign in to GitHub.com**.

**Step 2.** GitHub Desktop opens your browser and takes you to GitHub's authorisation page.

**Step 3.** Click **Authorize desktop** to grant GitHub Desktop access to your account.

**Step 4.** Your browser returns you to GitHub Desktop, which is now authenticated.

**Configure Git identity (if not already set):**

GitHub Desktop uses the name and email from your GitHub account by default. If you want to use a custom name or the noreply email address for privacy:

Go to **GitHub Desktop → Settings (Preferences on Mac) → Git** and update the name and email fields.

Use your noreply address here if you enabled email privacy:

```
12345678+YOUR_USERNAME@users.noreply.github.com
```

**Multiple accounts:**

GitHub Desktop supports multiple accounts. Go to **Settings → Accounts** to add additional GitHub.com or GitHub Enterprise accounts. You can switch between accounts per repository.

---

## The GitHub Desktop Interface

The GitHub Desktop window has four main areas:

```
┌─────────────────────────────────────────────────────────────┐
│ Current Repository  │  Current Branch  │  Fetch/Push Button │  ← Top bar
├─────────────────────┴──────────────────┴────────────────────┤
│                                                              │
│  Left panel:           │  Right panel:                       │
│                        │                                     │
│  Changes tab:          │  File diff view:                    │
│  - Files with changes  │  - Red lines: removed               │
│  - Checkboxes to stage │  - Green lines: added               │
│                        │                                     │
│  History tab:          │  Repository view                    │
│  - Commit history      │  (when no changes)                  │
│  - Graph of branches   │                                     │
│                        │                                     │
├────────────────────────┤                                     │
│  Commit message field  │                                     │
│  Description field     │                                     │
│  [Commit to branch]    │                                     │
└────────────────────────┴─────────────────────────────────────┘
```

**Top bar - three key controls:**

**Current Repository dropdown:** Shows which repository you are working in. Click to switch between repositories or add new ones.

**Current Branch dropdown:** Shows the active branch. Click to create a new branch, switch branches or see all branches.

**Fetch/Pull/Push button:** Updates based on context - shows **Fetch origin** when you have not checked recently, **Pull origin** when there are remote commits to download, **Push origin** when you have local commits to upload.

**Left panel - Changes tab:**

Lists every file with uncommitted changes. Tick the checkbox next to each file to include or exclude it from the next commit. You can also right-click files for additional options.

**Left panel - History tab:**

Shows every commit in the current branch with the commit message, author, date and a visual branch graph on the left side. Click any commit to see exactly what changed.

**Diff view (right panel):**

When you select a file in the Changes or History tab, the right panel shows a line-by-line diff. Red lines were removed. Green lines were added. Click the blue dot next to any line to stage or unstage that specific line - a feature called **partial staging**.

**Commit area (bottom of left panel):**

Two fields: a short **Summary** (required) and an optional longer **Description**. Click **Commit to BRANCH_NAME** to commit the staged changes.

---

## Cloning a Repository

Cloning downloads a repository from GitHub to your local machine.

**From GitHub.com:**

1. Go to any repository on GitHub
2. Click the green **Code** button
3. Click **Open with GitHub Desktop**
4. GitHub Desktop opens and asks where to save it
5. Choose a local path and click **Clone**

**From within GitHub Desktop:**

1. Go to **File → Clone repository** (or `Ctrl+Shift+O` / `Cmd+Shift+O`)
2. Three tabs appear:
   - **GitHub.com:** Lists all your repositories and starred repos
   - **GitHub Enterprise:** For enterprise accounts
   - **URL:** Paste any Git repository URL (GitHub, GitLab, Bitbucket or self-hosted)
3. Choose a local path
4. Click **Clone**

---

## Creating a New Repository

1. Go to **File → New repository** (or `Ctrl+N` / `Cmd+N`)
2. Fill in the details:
   - **Name:** Your repository name (hyphens and underscores are fine)
   - **Description:** Optional
   - **Local path:** Where on your computer to create it
   - **Initialise this repository with a README:** Recommended
   - **Git ignore:** Choose a template for your language
   - **Licence:** Choose MIT, Apache 2.0 etc.
3. Click **Create repository**

The repository is created locally. To publish it to GitHub:

4. Click **Publish repository** in the top bar
5. Choose a name, description and whether to keep it private
6. Click **Publish repository**

---

## Adding an Existing Local Repository

If you already have a Git repository on your machine that is not yet in GitHub Desktop:

1. Go to **File → Add local repository** (or `Ctrl+O` / `Cmd+O`)
2. Click **Choose** and navigate to the folder containing the `.git` directory
3. Click **Add repository**

If the folder is not yet a Git repository, GitHub Desktop offers to initialise it for you.

---

## The Daily Workflow

The everyday GitHub Desktop workflow follows these steps:

**Step 1 - Fetch and pull the latest changes**

Click **Fetch origin** at the top right. If new commits exist, the button changes to **Pull origin**. Click again to download them.

Always fetch before starting work to ensure you are building on the latest code.

**Step 2 - Create a branch (optional)**

Click the **Current Branch** dropdown → **New branch** → type a name → **Create branch**.

**Step 3 - Make changes in your editor**

Open your editor, make changes to files, save them.

**Step 4 - Review your changes**

Switch back to GitHub Desktop. The **Changes** tab lists every modified file. Click each file to see the diff - red lines removed, green lines added.

**Step 5 - Stage your changes**

Tick the checkbox next to each file you want to include in the commit. Untick files you want to save for a later commit.

To stage only specific lines in a file, hover over the diff - a blue circle appears on the left of each line. Click it to include or exclude that individual line.

**Step 6 - Write a commit message**

Type a clear **Summary** in the bottom field. Keep it concise and descriptive. Add an optional **Description** for more detail.

> [!TIP]
> Good commit messages use the same format as the git/ folder: `add: new feature`, `fix: broken button`, `update: revise README`. Consistency makes history readable.

**Step 7 - Commit**

Click **Commit to BRANCH_NAME**.

**Step 8 - Push**

Click **Push origin** in the top bar to upload your commits to GitHub.

---

## Branching in GitHub Desktop

**Create a new branch:**

1. Click the **Current Branch** dropdown
2. Click **New branch**
3. Type a name (e.g., `feature/dark-mode` or `fix/login-bug`)
4. Choose whether to base it on the current branch or another branch
5. Click **Create branch**

GitHub Desktop automatically switches to the new branch.

**Switch branches:**

Click the **Current Branch** dropdown and click any branch name.

> [!WARNING]
> If you have uncommitted changes when switching branches, GitHub Desktop will either bring the changes to the new branch (if they do not conflict) or ask you to stash them first.

**Delete a branch:**

Right-click the branch in the Current Branch dropdown → **Delete**. You can only delete branches other than the one you are currently on.

---

## Merging Branches

To merge another branch into your current branch:

1. Make sure you are on the **destination** branch (e.g., `main`)
2. Go to **Branch → Merge into current branch**
3. Select the branch you want to merge from
4. Click **Create a merge commit** (or **Squash and merge** if available)

If there are no conflicts, the merge completes immediately. If there are conflicts, GitHub Desktop pauses and shows the conflicted files.

---

## Resolving Merge Conflicts

When a merge conflict occurs, GitHub Desktop shows the conflicted files with a warning icon.

**Step 1.** Click a conflicted file to see it highlighted in the diff view.

**Step 2.** Click **Open in editor** to open the file in your configured code editor.

**Step 3.** The file contains standard Git conflict markers:

```
<<<<<<< HEAD
Your version of the code
=======
The incoming version of the code
>>>>>>> feature/dark-mode
```

**Step 4.** Edit the file to keep the correct version. Remove all conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).

**Step 5.** Save the file.

**Step 6.** Back in GitHub Desktop, the conflict warning on that file should disappear. Mark it as resolved.

**Step 7.** Once all conflicts are resolved, click **Continue merge** to complete it.

> [!TIP]
> For complex conflicts, click **Open in Visual Studio Code** (or your configured editor) which provides a three-way merge editor showing your version, their version and the result simultaneously.

---

## Working with Pull Requests

**Create a pull request:**

After pushing a branch, GitHub Desktop shows a **Create pull request** button in the top bar or a banner at the bottom of the screen. Click it to open the pull request form on GitHub.com in your browser.

Or go to **Branch → Create pull request** from the menu.

**View open pull requests:**

Go to **Branch → View pull request on GitHub** to see the PR on GitHub.com.

**Check out a pull request locally:**

From the **Current Branch** dropdown, click the **Pull requests** tab. All open pull requests appear here. Click one to check it out locally and review the changes in GitHub Desktop's diff view.

---

## Fetching, Pulling and Pushing

**Fetch:** Downloads remote changes without applying them. Lets you see if there is anything new.

**Pull:** Downloads remote changes and applies them to your local branch (equivalent to `git pull`).

**Push:** Uploads your local commits to GitHub (equivalent to `git push`).

The top-right button in GitHub Desktop cycles through these states automatically:

- **Fetch origin** - no known changes, clicking checks for updates
- **Pull origin (N)** - N commits available to download
- **Push origin (N)** - N local commits ready to upload

> [!NOTE]
> GitHub Desktop fetches automatically every few minutes in the background. The button shows the latest state without you needing to manually fetch first.

---

## Viewing History and Blame

**History tab:**

Click the **History** tab in the left panel to see every commit on the current branch. Each entry shows:

- Commit message
- Author name and avatar
- Time since the commit
- Changed files

Click any commit to see the full diff of what changed. Click individual files within the commit to see their specific changes.

**Revert a commit from History:**

Right-click any commit in the History tab → **Revert changes in commit**. This creates a new commit that undoes the selected commit - safe even for pushed commits.

**Cherry-pick a commit:**

Right-click any commit in the History tab → **Cherry-pick commit**. Applies that specific commit to the current branch.

**Blame view:**

Not directly available in GitHub Desktop. Use GitHub.com's blame view instead: navigate to any file → click **Blame** to see who last changed each line and when.

---

## Stashing Changes

If you need to switch branches but have uncommitted changes you are not ready to commit, stash them.

**Stash changes:**

GitHub Desktop stashes automatically when you switch branches with uncommitted changes. It asks whether to bring the changes to the new branch or leave them on the current branch.

To manually stash: **Branch → Stash all changes**.

**View and restore stashed changes:**

Go to **Branch → Pop stashed changes** to restore the most recent stash.

> [!NOTE]
> GitHub Desktop supports only one stash at a time. For more complex stash management, use the command line (`git stash list`, `git stash apply stash@{N}` etc.).

---

## Undoing Commits

**Undo the last commit (keeps changes staged):**

Go to **Edit → Undo last commit** (or `Ctrl+Z` / `Cmd+Z` after committing).

This works on the most recent unpushed commit only. It puts the changes back into the staging area so you can modify and recommit.

**Amend the last commit message:**

Right-click the most recent commit in the History tab → **Amend commit** → edit the message → **Amend last commit**.

Only works on commits not yet pushed to GitHub.

**Discard all changes (not yet committed):**

Right-click any file in the Changes tab → **Discard changes** → confirm.

Or to discard everything: right-click in the Changes tab header → **Discard all changes**.

> [!WARNING]
> Discarding changes is permanent. GitHub Desktop does not keep a recycle bin for discarded changes. Make sure you want to throw them away before confirming.

---

## Co-authoring Commits

GitHub Desktop makes it easy to add co-authors to a commit - useful when pair programming or crediting someone who helped with a change.

**Add a co-author:**

1. In the commit message area, click the person icon below the description field (or press `Ctrl+G` / `Cmd+G`)
2. Type a collaborator's GitHub username or name
3. GitHub Desktop suggests matching GitHub users
4. Select the co-author

GitHub Desktop adds the standard `Co-authored-by:` line to the commit automatically. The commit shows both authors in the History view and on GitHub.

---

## Opening in Your Editor or Terminal

GitHub Desktop integrates with your code editor and terminal so you can move between them without leaving the app.

**Open in editor:**

Click **Open in EDITOR_NAME** in the top menu or press `Ctrl+Shift+A` / `Cmd+Shift+A`. This opens the repository root in your configured editor.

**Configure your preferred editor:**

**Windows:** GitHub Desktop → Settings → Integrations → External editor → choose from VS Code, Visual Studio, Atom, Sublime Text, Notepad++, WebStorm, PyCharm and others.

**Mac:** GitHub Desktop → Settings → Integrations → External editor → choose from VS Code, Atom, Sublime Text, BBEdit, TextMate, Xcode, WebStorm, PyCharm and others.

**Open in terminal:**

Click **Open in terminal** (accessible via the Repository menu or right-click on the repository in the left panel). Opens the repository directory in your configured terminal.

**Configure your preferred terminal:**

**Windows:** Settings → Integrations → Shell → choose Command Prompt, PowerShell, Git Bash, Windows Subsystem for Linux or others.

**Mac:** Settings → Integrations → Shell → choose Terminal, iTerm2 or Hyper.

---

## GitHub Desktop vs GitHub.com

GitHub Desktop and GitHub.com complement each other. Some tasks are better on Desktop, others on the web.

| Task | GitHub Desktop | GitHub.com |
|---|---|---|
| Visual diff of local changes | ✅ Best tool | Only after push |
| Commit and push | ✅ Primary tool | Limited (one file at a time) |
| Create and merge branches | ✅ Easy GUI | ✅ Easy web UI |
| Review pull requests in detail | View files | ✅ Better inline review |
| Create issues | Opens browser | ✅ Full editor |
| GitHub Actions logs | Opens browser | ✅ Full logs |
| Repository settings | Opens browser | ✅ Full settings |
| Browse commit history visually | ✅ Good | ✅ Also good |
| Resolve merge conflicts | ✅ Opens editor | Limited |
| Access on any device | Install required | ✅ Browser only |
| github.dev / Codespaces | Opens browser | ✅ Press `.` key |
| Manage secrets and variables | Opens browser | ✅ Settings |
| View Dependabot alerts | Opens browser | ✅ Security tab |

**The recommended workflow:** Use GitHub Desktop for your local coding work (staging, committing, pushing, branching). Use GitHub.com for collaboration (reviewing PRs, managing issues, viewing Actions, configuring settings).

---

## Keyboard Shortcuts

| Shortcut (Windows) | Shortcut (Mac) | Action |
|---|---|---|
| `Ctrl+N` | `Cmd+N` | New repository |
| `Ctrl+O` | `Cmd+O` | Add local repository |
| `Ctrl+Shift+O` | `Cmd+Shift+O` | Clone repository |
| `Ctrl+Shift+A` | `Cmd+Shift+A` | Open in external editor |
| `Ctrl+Enter` | `Cmd+Enter` | Commit staged changes |
| `Ctrl+P` | `Cmd+P` | Push to remote |
| `Ctrl+Shift+P` | `Cmd+Shift+P` | Pull from remote |
| `Ctrl+Z` | `Cmd+Z` | Undo last commit |
| `Ctrl+G` | `Cmd+G` | Add co-author |
| `Ctrl+1` | `Cmd+1` | Show Changes tab |
| `Ctrl+2` | `Cmd+2` | Show History tab |
| `Ctrl+B` | `Cmd+B` | Show branch list |
| `Ctrl+R` | `Cmd+R` | Refresh repository |
| `Ctrl+W` | `Cmd+W` | Close window |
| `F5` | `Cmd+R` | Reload |

---

## Try It Yourself

**Step 1.** Install GitHub Desktop for your operating system using the instructions above.

**Step 2.** Sign in with your GitHub account.

**Step 3.** Configure your editor and terminal in Settings → Integrations.

**Step 4.** Clone a repository:
- Go to **File → Clone repository**
- Click the **GitHub.com** tab
- Find `hello-github` (or any repository you created earlier)
- Choose a local path
- Click **Clone**

**Step 5.** Open the repository in your editor:
- Click **Open in EDITOR_NAME** in the top bar

**Step 6.** Make a change:
- Edit `README.md` in your editor - add any text
- Save the file

**Step 7.** Review the change in GitHub Desktop:
- Switch back to GitHub Desktop
- The `README.md` file appears in the Changes tab
- Click it to see the diff - green lines show your additions

**Step 8.** Commit the change:
- Make sure `README.md` is ticked
- Type a commit message: `update: add content to README`
- Click **Commit to main**

**Step 9.** Push to GitHub:
- Click **Push origin** in the top bar

**Step 10.** Check GitHub.com:
- Go to your repository on GitHub
- Your change should appear

**Step 11.** Practice branching:
- Click the **Current Branch** dropdown
- Click **New branch** → name it `feature/practice` → **Create branch**
- Make another change to `README.md` in your editor
- Commit the change in GitHub Desktop
- Click **Publish branch** then **Create pull request**
- GitHub.com opens with the pull request form
- Submit and merge the pull request
- Back in GitHub Desktop, switch to `main` and click **Pull origin**

---

## Common Mistakes

**Not fetching before starting work.**

If you do not fetch first, you may commit on top of an outdated branch and create unnecessary merge conflicts. Always click **Fetch origin** before starting work each day.

**Committing to the wrong branch.**

The current branch is shown in the top bar. Check it before committing. Committing to `main` directly when you meant to commit to a feature branch is a common mistake. If this happens, use **Edit → Undo last commit** immediately (before pushing).

**Forgetting to push after committing.**

A commit is local until pushed. Your commits are not on GitHub until you click **Push origin**. Teammates cannot see your work and it is not backed up remotely until you push.

**Discarding changes accidentally.**

Right-clicking a file in the Changes tab shows **Discard changes** prominently. There is no undo for this. Read carefully before confirming. If you are unsure, commit a work-in-progress commit and push it - it is always easier to undo a commit than to recover discarded changes.

**Expecting GitHub Desktop to do everything.**

GitHub Desktop does not support interactive rebase, `git bisect`, `git filter-repo`, hooks configuration or many other advanced Git operations. For these, use the command line. GitHub Desktop's **Open in terminal** button makes switching easy.

**Using the Linux community fork without knowing what it is.**

The shiftkey fork is reliable but is not maintained by GitHub. It may occasionally be out of date with new features. Know what you are installing and keep it updated via your package manager.

---

## Summary

- GitHub Desktop is a free, official GUI application for Git and GitHub available on Windows and Mac (Linux via community fork)
- Install on Windows via direct download, winget or Chocolatey; on Mac via direct download or Homebrew
- Sign in with your GitHub account and configure your preferred editor and terminal in Settings → Integrations
- The interface has a top bar (repository, branch, fetch/push), a left panel (Changes, History) and a diff view
- Daily workflow: fetch → branch → edit in editor → review diff → stage → commit → push
- GitHub Desktop supports partial staging (staging individual lines), co-authoring, stashing, undoing commits and visual conflict resolution
- Use GitHub Desktop for local work; use GitHub.com for collaboration, issues, Actions, settings and security
- GitHub Desktop does not support interactive rebase, bisect or other advanced operations - use the command line for those

---

## Sources and Further Reading

- [GitHub Desktop official website](https://desktop.github.com) - download, release notes and documentation
- [GitHub Desktop documentation](https://docs.github.com/en/desktop) - complete official documentation
- [GitHub Desktop source code](https://github.com/desktop/desktop) - open source repository on GitHub
- [shiftkey/desktop](https://github.com/shiftkey/desktop) - community-maintained Linux fork
- [GitHub Desktop release notes](https://desktop.github.com/release-notes/) - changelog for all releases
- [Keyboard shortcuts - GitHub Desktop Docs](https://docs.github.com/en/desktop/overview/github-desktop-keyboard-shortcuts) - complete keyboard shortcut reference

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
