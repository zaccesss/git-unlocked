# GitHub Desktop

**Difficulty:** 🟢 Beginner | **Time:** 25 minutes

GitHub Desktop is a free, visual Git client built and maintained by GitHub. It is designed for developers who want a clean graphical interface for their day-to-day Git workflow without needing to learn terminal commands. It is deliberately scoped - it does not try to expose every Git feature, but what it does cover, it makes easy to understand and hard to get wrong. It is an excellent starting point for beginners and a practical companion tool for experienced developers who prefer a visual interface for commit review and branch management.

---

## Table of Contents

1. [History and background](#1-history-and-background)
2. [Installing GitHub Desktop](#2-installing-github-desktop)
3. [Signing in and setting up](#3-signing-in-and-setting-up)
4. [The interface at a glance](#4-the-interface-at-a-glance)
5. [Cloning and creating repositories](#5-cloning-and-creating-repositories)
6. [Staging and committing](#6-staging-and-committing)
7. [Copilot commit message generation](#7-copilot-commit-message-generation)
8. [Branches](#8-branches)
9. [Pushing, pulling and fetching](#9-pushing-pulling-and-fetching)
10. [Viewing history](#10-viewing-history)
11. [Merge conflicts](#11-merge-conflicts)
12. [Stashing](#12-stashing)
13. [Cherry-pick](#13-cherry-pick)
14. [Pull requests](#14-pull-requests)
15. [Git hooks support](#15-git-hooks-support)
16. [Forking and open source workflows](#16-forking-and-open-source-workflows)
17. [Linux support](#17-linux-support)
18. [Keyboard shortcuts](#18-keyboard-shortcuts)
19. [Try It Yourself](#19-try-it-yourself)
20. [Common Mistakes](#20-common-mistakes)
21. [Summary](#21-summary)
22. [Sources](#22-sources)

---

## 1. History and background

GitHub Desktop was first released in 2012 as a native Mac application called GitHub for Mac, followed by GitHub for Windows in 2012. Both were proprietary apps with separate codebases. In 2017, GitHub unified and rewrote them into a single Electron-based application called GitHub Desktop, open-sourced under the MIT licence at [github.com/desktop/desktop](https://github.com/desktop/desktop).

The rewrite made GitHub Desktop cross-platform (Windows and macOS) with a shared codebase, and the open-source model allowed community contributions. The application uses a bundled Git binary rather than your system Git, so it works correctly regardless of which version of Git you have installed.

GitHub Desktop v3.5.0 (June 2025) added GitHub Copilot commit message generation as a generally available feature. Version 3.5.5 (February 2026) fixed Git hooks support, which had been unreliable with version managers like nvm, pyenv and rbenv due to shell environment issues. The current stable release is v3.5.7 (April 2026).

---

## 2. Installing GitHub Desktop

🪟 **Windows**

```bash
winget install GitHub.GitHubDesktop
```

Or download the installer from [desktop.github.com](https://desktop.github.com). Both an installer (`GitHubDesktopSetup-x64.exe`) and an MSI for enterprise deployment are available. After installation, GitHub Desktop is accessible from the Start menu and the `github` command is added to the PATH.

🍎 **macOS**

```bash
brew install --cask github
```

Or download the `.zip` from the website, unzip it and drag the app to Applications. GitHub Desktop supports both Apple Silicon and Intel chips; the website offers a universal download or separate downloads per architecture.

🐧 **Linux**

GitHub does not publish an official Linux build. A community-maintained fork at [github.com/shiftkey/desktop](https://github.com/shiftkey/desktop) provides packages for common distributions:

```bash
# Debian/Ubuntu - via APT repository
wget -qO - https://apt.packages.shiftkey.dev/gpg.key | \
  gpg --dearmor | sudo tee /usr/share/keyrings/shiftkey-packages.gpg > /dev/null

sudo sh -c 'echo "deb [arch=amd64 signed-by=/usr/share/keyrings/shiftkey-packages.gpg] \
  https://apt.packages.shiftkey.dev/ubuntu/ any main" > \
  /etc/apt/sources.list.d/shiftkey-packages.list'

sudo apt update && sudo apt install github-desktop

# Fedora/RPM - via YUM repository
sudo rpm --import https://rpm.packages.shiftkey.dev/gpg.key
sudo sh -c 'echo -e "[shiftkey-packages]\nname=GitHub Desktop\nbaseurl=https://rpm.packages.shiftkey.dev/rpm/\nenabled=1\ngpgcheck=1\nrepo_gpgcheck=1\ngpgkey=https://rpm.packages.shiftkey.dev/gpg.key" > /etc/yum.repos.d/shiftkey-packages.repo'
sudo dnf install github-desktop

# Flatpak (all Linux distributions)
flatpak install flathub io.github.shiftey.Desktop
```

> [!NOTE]
> The Linux fork maintained by shiftkey tracks the official GitHub Desktop releases closely but is not an official GitHub product. It may lag slightly behind the official release on Windows and macOS. Copilot commit message generation is available in the Linux fork from version 3.5 onwards.

---

## 3. Signing in and setting up

On first launch, GitHub Desktop asks you to sign in to GitHub. You can sign in to multiple accounts simultaneously - GitHub.com, GitHub Enterprise Server instances, and other domains are all supported since the multi-domain feature was added in v3.5.0.

After signing in, configure your name and email address for commits if they are not already in your global Git config. GitHub Desktop reads from `~/.gitconfig` and shows your existing settings. You can override them per repository in `File > Options > Git` (Windows/Linux) or `GitHub Desktop > Settings > Git` (Mac).

**Configuring an external editor**: GitHub Desktop can open files and repositories in your preferred editor. Go to `File > Options > Integrations` (Windows/Linux) or `GitHub Desktop > Settings > Integrations` (Mac) and choose your editor from the dropdown. Supported editors include VS Code, JetBrains IDEs, Sublime Text, Atom, Notepad++ and others. The **Open in Editor** button in the toolbar opens the repository root in that editor instantly.

**Configuring a shell**: similarly, the **Open in Shell** button opens your preferred terminal at the repository root. Configure the shell in the same Integrations settings panel.

---

## 4. The interface at a glance

GitHub Desktop has a deliberately simple interface with four main areas:

**The toolbar** at the top shows:
- The current repository (click to switch repositories)
- The current branch (click to switch or create branches)
- **Fetch/Push/Pull** button showing sync status
- **Open in Editor** and **View on GitHub** buttons

**The left panel** has two tabs:
- **Changes** - files modified in the working tree, with checkboxes for including/excluding from the next commit
- **History** - the commit log for the current branch

**The right panel** shows:
- In Changes tab: the diff for the selected file
- In History tab: the diff for the selected commit and the list of files it changed

**The commit area** at the bottom of the left panel (visible in Changes tab) contains:
- A **Summary** input for the commit message subject line
- An optional **Description** input for the commit body
- The **Commit to [branch]** button

---

## 5. Cloning and creating repositories

**Cloning a repository from GitHub**: go to `File > Clone Repository`. You can browse your GitHub repositories by name, paste a URL, or enter a path to a local repository. Choose a local path and click **Clone**.

**Cloning from the command line and opening in GitHub Desktop**:

```bash
# Clone normally then open in GitHub Desktop
git clone https://github.com/owner/repo.git
github repo/   # opens in GitHub Desktop (if the github command is on PATH)
```

**Creating a new repository**: go to `File > New Repository`. Enter the name, description and local path. Optionally initialise with a README, a `.gitignore` template and a licence. Click **Create Repository**.

**Adding an existing local repository**: go to `File > Add Local Repository` and navigate to the folder. GitHub Desktop detects the `.git` directory and adds the repository to its sidebar.

**Publishing to GitHub**: for a new local repository that does not yet have a remote, click **Publish repository** in the toolbar. GitHub Desktop creates the repository on GitHub with your chosen name, visibility and organisation.

---

## 6. Staging and committing

GitHub Desktop uses a checkbox-based staging model rather than the index-based model of the terminal. Every modified file starts checked; uncheck a file to exclude it from the next commit.

**Viewing the diff**: click any file in the Changes tab to see its diff in the right panel. Changed lines are shown in red (removed) and green (added). The diff is read-only in the right panel - you cannot edit files directly from the diff.

**Staging individual lines**: click the checkbox that appears in the gutter of the diff for any specific line or group of lines to include only those lines in the next commit. This is equivalent to `git add -p` but visual. Use the filter box at the top of the file list (added in v3.4, filterable changes) to find specific files quickly when you have many changes.

**Image diffs**: GitHub Desktop renders visual diffs for images with four modes: 2-up (side-by-side), swipe (drag to reveal before/after), onion-skin (opacity blend) and difference (pixel difference). This is far more useful than `git diff` for image assets.

**Writing the commit message**: type the summary in the **Summary** box. GitHub convention is an imperative present-tense subject under 72 characters (for example, "Fix login redirect on mobile"). Use the **Description** box for a longer explanation if needed.

**Adding co-authors**: click the person icon next to the **Summary** box to add co-authors. GitHub Desktop shows your collaborators' GitHub usernames as suggestions. Co-authorship is recorded using `Co-authored-by:` trailers in the commit message, following the GitHub convention for attributing pair-programming sessions.

**Committing**: click **Commit to [branch]** or press `Ctrl+Enter` (Windows/Linux) / `Cmd+Enter` (Mac). If you have unchecked files, they stay in the Changes list for the next commit.

---

## 7. Copilot commit message generation

Since v3.5.0 (June 2025), GitHub Desktop includes GitHub Copilot commit message generation. A sparkle icon appears in the **Summary** input when:

- You have a GitHub Copilot subscription (Free, Pro, Pro+, Business or Enterprise)
- You are signed in to the matching GitHub account
- You have at least one file checked for inclusion in the commit

Click the sparkle icon and GitHub Desktop sends your staged diff to Copilot. Within a few seconds, a commit message appears in the Summary and optionally the Description boxes. The message follows conventional commit conventions by default.

You can regenerate the message by clicking the sparkle icon again if the first suggestion is not quite right. Edit the generated message freely before committing - it is a suggestion, not a final decision.

> [!NOTE]
> Copilot commit messages in GitHub Desktop are English-only as of April 2026. The feature analyses only the checked (staged) changes - unchecked files are not included in the context sent to Copilot.

---

## 8. Branches

**Viewing branches**: click the current branch name in the toolbar to open the Branches panel. It shows all local branches and, in a separate tab, all remote branches. The search box filters by name.

**Switching branches**: click any branch in the list to check it out. If you have uncommitted changes, GitHub Desktop asks whether to bring them to the new branch or stash them first.

**Creating a branch**: in the Branches panel, click **New Branch**. Enter a name and choose whether to base it off the current branch or another branch. Click **Create Branch**.

**Renaming a branch**: right-click a local branch in the Branches panel and select **Rename**. GitHub Desktop renames it locally and offers to rename the remote tracking branch as well.

**Deleting a branch**: right-click a local branch and select **Delete**. GitHub Desktop warns you if the branch has unmerged commits. Remote branch deletion is offered as an option.

**Merging into the current branch**: go to `Branch > Merge into Current Branch`, select the branch to merge from, and click **Merge**. If there are conflicts, GitHub Desktop transitions to the conflict resolution UI.

**Comparing branches**: right-click a branch in the panel and select **Compare on GitHub** to open the branch comparison page in the browser, showing ahead/behind commit counts and the diff.

---

## 9. Pushing, pulling and fetching

The toolbar button at the top right changes depending on the state of your branch:

- **Fetch origin** - when local and remote are in sync, fetches to check for new remote commits
- **Pull origin** (with a count badge) - when the remote has commits your local branch does not, pulls them down
- **Push origin** (with a count badge) - when your local branch has commits the remote does not, pushes them up

GitHub Desktop always fetches automatically in the background at a configurable interval (default: every 15 minutes) so the badge counts stay current.

**Force push**: when a rebase or amend has rewritten history, the standard push will be rejected. Go to `Repository > Push` with the `--force` option available via the dropdown arrow next to the Push button. GitHub Desktop uses `--force-with-lease` to avoid overwriting others' commits.

---

## 10. Viewing history

Click the **History** tab in the left panel to see the commit log for the current branch. Each row shows the commit message, author avatar, relative time and the number of files changed.

**Viewing a commit's diff**: click any commit to see the list of files it changed in the right panel. Click any file to see its diff. Use the unified/split toggle at the top right to switch between view modes.

**Searching commits**: use the search box at the top of the History tab to filter commits by message, author name or SHA.

**Blame**: GitHub Desktop does not have a built-in blame view. Use **View on GitHub** to open the file in the browser and use GitHub's blame interface, or use your configured external editor's blame feature.

---

## 11. Merge conflicts

When a merge, rebase or pull produces conflicts, GitHub Desktop shows a conflict resolution dialog listing each conflicting file with a traffic light indicator:

- 🟢 - conflict resolved
- 🔴 - conflict unresolved

For each conflicting file you have three choices:

1. **Use the configured editor** - click **Open in Editor** to resolve the conflict manually in VS Code or your configured editor. The standard conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) are present in the file. Save and close, then return to GitHub Desktop.
2. **Use ours** - right-click the file and select **Use the modified file from [current branch]** to discard the incoming changes entirely.
3. **Use theirs** - right-click and select **Use the modified file from [incoming branch]** to discard your changes entirely.

Once all files are marked green, click **Continue Merge** and then **Commit Merge** to finalise.

> [!TIP]
> For complex conflicts, using the external editor (VS Code with its 3-way merge editor, or JetBrains with its merge tool) is usually faster than trying to resolve manually in a plain text editor. GitHub Desktop integrates cleanly with both.

---

## 12. Stashing

GitHub Desktop has a single stash slot per branch. When you switch branches with uncommitted changes, GitHub Desktop offers to stash the changes automatically.

**Stashing manually**: go to `Branch > Stash All Changes`. The Changes tab shows a banner saying your changes have been stashed.

**Restoring the stash**: a **Stashed Changes** item appears at the bottom of the Changes tab when a stash exists. Click **Restore** to apply it, or **Discard** to throw it away.

**Limitation**: GitHub Desktop supports only one stash per branch. If you need multiple stashes, use the integrated terminal and `git stash push -m "name"` to manage a stack of stashes by name.

---

## 13. Cherry-pick

Cherry-pick copies one or more commits from another branch into the current branch. In GitHub Desktop, cherry-pick works via drag-and-drop:

1. Open the **History** tab
2. Switch to the branch containing the commit you want
3. Find the commit and drag it onto the target branch in the Branches panel on the left

Alternatively, right-click a commit in the History tab and select **Cherry-pick Commit**. GitHub Desktop shows a branch picker and moves the commit to the selected branch.

For multi-commit cherry-picks, hold `Shift` and click to select a range of commits, or hold `Ctrl`/`Cmd` to select individual commits. Right-click the selection and choose **Cherry-pick Commits**.

---

## 14. Pull requests

**Creating a pull request**: after pushing a branch, GitHub Desktop shows a **Create Pull Request** button in the toolbar. Clicking it opens the GitHub website pre-filled with your branch and the default base branch. The PR is created on GitHub, not inside GitHub Desktop itself.

**Viewing pull requests**: go to `Branch > View Pull Request` to open the PR page for the current branch on GitHub.

**Checking out a PR branch**: in the Branches panel, switch to the **Pull Requests** tab. It lists all open PRs on the repository. Click any PR to check out its branch locally. This is useful for reviewing and testing a colleague's work before approving.

**PR status**: the toolbar shows a CI status icon for the current branch when it is associated with a PR. A green tick indicates passing checks; a red cross indicates failures. Click it to open the PR's checks page on GitHub.

---

## 15. Git hooks support

Git hooks are scripts that run automatically at specific points in the Git workflow (before a commit, before a push, after a merge and so on). They are stored in `.git/hooks/`.

GitHub Desktop v3.5.5 (February 2026) fixed a long-standing issue where hooks using version managers like nvm (Node), pyenv (Python) or rbenv (Ruby) would fail silently because GitHub Desktop did not load the full shell environment when running hooks. The fix loads the hook's required shell environment correctly.

To use hooks with GitHub Desktop, place your hook scripts in `.git/hooks/` with the correct name (`pre-commit`, `commit-msg`, `pre-push` etc.) and make them executable:

```bash
chmod +x .git/hooks/pre-commit
```

If you use a tool like Husky, Lefthook or pre-commit (Python), they install hooks into `.git/hooks/` automatically. These work with GitHub Desktop after v3.5.5.

---

## 16. Forking and open source workflows

**Forking**: when you clone a repository you do not own and do not have push access to, GitHub Desktop detects this and asks whether you want to fork it to your own account. Clicking **Fork This Repository** creates a fork on GitHub and sets up the remote correctly.

**Contributing to a fork**: after forking, GitHub Desktop uses the standard fork-and-PR workflow:
1. Create a feature branch from the fork's default branch
2. Make changes and commit
3. Push the branch to your fork
4. Create a pull request from your fork's branch to the upstream repository

The **Upstream** remote is visible in the repository settings and can be used to sync your fork with the original repository via `Branch > Merge into Current Branch` with the upstream's default branch selected.

---

## 17. Linux support

As noted in the installation section, the official GitHub Desktop does not support Linux. The [shiftkey/desktop](https://github.com/shiftkey/desktop) fork is the community-maintained alternative. It tracks the official release closely and is available as:

- `.deb` packages for Debian/Ubuntu (APT repository)
- `.rpm` packages for Fedora/RHEL (YUM repository)
- AppImage (universal Linux)
- Flatpak via Flathub (`io.github.shiftey.Desktop`)

The shiftkey fork includes all features of the official release including Copilot commit messages, multi-domain auth and filterable changes.

---

## 18. Keyboard shortcuts

| Action | Windows/Linux | Mac |
|---|---|---|
| Switch to Changes tab | `Ctrl+1` | `Cmd+1` |
| Switch to History tab | `Ctrl+2` | `Cmd+2` |
| Commit | `Ctrl+Enter` | `Cmd+Enter` |
| Push | `Ctrl+P` | `Cmd+P` |
| Pull | `Ctrl+Shift+P` | `Cmd+Shift+P` |
| Fetch | `Ctrl+Shift+T` | `Cmd+Shift+T` |
| New branch | `Ctrl+Shift+N` | `Cmd+Shift+N` |
| Switch repository | `Ctrl+B` | `Cmd+B` |
| Stash changes | `Ctrl+Shift+S` | `Cmd+Shift+S` |
| Preferences / Settings | `Ctrl+,` | `Cmd+,` |
| Open in editor | `Ctrl+Shift+A` | `Cmd+Shift+A` |
| Open in shell | `Ctrl+\`` | `Cmd+\`` |
| View on GitHub | `Ctrl+Shift+G` | `Cmd+Shift+G` |
| Undo last commit | `Ctrl+Z` | `Cmd+Z` |

---

## 19. Try It Yourself

**Exercise 1 - stage individual lines**

Make several changes to a file in different sections - for example, change a variable name at the top and fix a typo in a comment at the bottom. In the Changes tab, find the file and look at its diff. Click the checkbox on the gutter next to only the typo fix lines. Commit just those lines with a message like "fix: correct typo in comment". Verify in the History tab that the commit contains only the typo change.

**Exercise 2 - cherry-pick a commit**

Create two branches from the same base. On branch A, make three commits: a bug fix, a feature addition and a style change. Switch to branch B. In the History tab, drag the bug fix commit from branch A to the Branches panel onto branch B. Verify that branch B now contains the bug fix but not the feature or style changes.

**Exercise 3 - resolve a merge conflict**

Create two branches. On each branch, edit the same line of the same file differently and commit. Merge one branch into the other to create a conflict. In the conflict resolution dialog, click **Open in Editor** to resolve the conflict in VS Code. Remove the conflict markers, keep the desired content, save and return to GitHub Desktop. Click **Continue Merge** and commit.

**Exercise 4 - use Copilot commit messages**

If you have a Copilot subscription, make a meaningful change to a file - for example, add a new function or fix a bug with several lines changed. Stage all the changes. Click the sparkle icon in the Summary box. Read the generated message. Edit it if needed. Commit. Compare the generated message with what you would have written yourself.

**Exercise 5 - fork and create a pull request**

Find a public open-source repository on GitHub that accepts contributions. In GitHub Desktop, clone it. GitHub Desktop should detect that you do not have push access and offer to fork it. Fork it. Create a branch, make a small improvement (fix a typo in the README, for example), commit and push. Click **Create Pull Request** in the toolbar to open GitHub and submit your first open-source PR.

---

## 20. Common Mistakes

Assuming GitHub Desktop has parity with VS Code or JetBrains for advanced Git operations is a common misconception. GitHub Desktop deliberately omits interactive rebase, the blame view, the full commit graph, stash management beyond one slot, and submodule support. It is designed to cover the common daily workflow cleanly, not to be a complete Git client. For anything outside its scope, use the **Open in Shell** button to drop into a terminal.

Forgetting to check individual lines leads to committing unrelated changes together. GitHub Desktop makes line-level staging visual and accessible - use it. Committing everything at once because it is faster produces a messier history that is harder to review and harder to revert.

Ignoring the stash one-slot limitation causes lost work. If you stash changes in GitHub Desktop and then stash again on the same branch, the second stash overwrites the first. Use the terminal for multiple stashes: `git stash push -m "descriptive name"` creates named stashes you can manage with `git stash list`.

Clicking **Discard Changes** without intending to is a risk in the GitHub Desktop interface because the option is visible in the right-click context menu. Discarded changes are gone immediately with no undo. If you are unsure, stash first.

Using the Linux shiftkey fork without checking its release version occasionally means running a version behind the latest official release. Check [github.com/shiftkey/desktop/releases](https://github.com/shiftkey/desktop/releases) to verify you have the current version.

---

## 21. Summary

GitHub Desktop is a free, open-source, cross-platform Git client focused on clarity and ease of use. It covers cloning, repository creation, line-level staging with checkboxes and image diffs, Copilot commit message generation, branching and merging, push and pull with automatic background fetching, history browsing, merge conflict resolution with external editor integration, a single-slot stash, drag-and-drop cherry-pick, pull request creation and checkout, Git hooks support (fixed in v3.5.5), and forking for open-source contributions. It does not try to expose every Git feature - interactive rebase, multi-stash management, blame, and a full commit graph are outside its scope. For those workflows, use the integrated terminal or switch to a more complete client like GitKraken, Sourcetree, or the Git integration in VS Code or a JetBrains IDE. Linux users need the community-maintained shiftkey fork rather than the official build.

---

## 22. Sources

- [GitHub Desktop documentation](https://docs.github.com/en/desktop)
- [GitHub Desktop keyboard shortcuts](https://docs.github.com/en/desktop/overview/github-desktop-keyboard-shortcuts)
- [GitHub Desktop repository](https://github.com/desktop/desktop)
- [GitHub Desktop release notes](https://desktop.github.com/release-notes/)
- [GitHub Desktop 3.5 - Copilot commit message generation](https://github.blog/changelog/2025-06-23-github-desktop-3-5-github-copilot-commit-message-generation-now-generally-available/)
- [GitHub Desktop 3.5.5 - Git hooks support](https://github.blog/changelog/2026-02-18-github-desktop-3-5-5-adds-git-hooks-support/)
- [shiftkey/desktop Linux fork](https://github.com/shiftkey/desktop)
- [Copilot in GitHub Desktop - responsible use](https://docs.github.com/en/copilot/responsible-use/copilot-in-github-desktop)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
