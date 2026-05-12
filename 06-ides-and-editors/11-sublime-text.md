# Sublime Text

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

Sublime Text is a proprietary cross-platform code editor known for its speed, the Command Palette, multiple cursors and the goto-anything file switcher. It has no built-in Git integration beyond reading `.gitignore` for file exclusion. All Git functionality comes through the **GitSavvy** extension, which is arguably the most complete keyboard-driven Git interface in any text editor outside of Emacs with Magit. GitSavvy provides staging at file, hunk and line level, interactive rebase from a history view, blame, GitHub integration and a full suite of `git:` prefixed Command Palette commands. The companion **Sublime Merge** is a separate standalone Git GUI from the same publisher that pairs naturally with Sublime Text.

---

## Table of Contents

1. [History and background](#1-history-and-background)
2. [Installing Sublime Text](#2-installing-sublime-text)
3. [Installing Package Control and GitSavvy](#3-installing-package-control-and-gitsavvy)
4. [GitSavvy fundamentals](#4-gitsavvy-fundamentals)
5. [The status dashboard](#5-the-status-dashboard)
6. [Staging at file, hunk and line level](#6-staging-at-file-hunk-and-line-level)
7. [Committing](#7-committing)
8. [The Repo History view](#8-the-repo-history-view)
9. [Interactive rebase from Repo History](#9-interactive-rebase-from-repo-history)
10. [File History and Line History](#10-file-history-and-line-history)
11. [Blame view](#11-blame-view)
12. [Branching and remotes](#12-branching-and-remotes)
13. [Stashing](#13-stashing)
14. [GitHub integration](#14-github-integration)
15. [The integrated terminal for Git](#15-the-integrated-terminal-for-git)
16. [Sublime Merge](#16-sublime-merge)
17. [Recommended keybindings](#17-recommended-keybindings)
18. [Pricing](#18-pricing)
19. [Try It Yourself](#19-try-it-yourself)
20. [Common Mistakes](#20-common-mistakes)
21. [Summary](#21-summary)
22. [Sources](#22-sources)

---

## 1. History and background

Sublime Text was created by Jon Skinner, a former Google engineer, and first released in 2008. It introduced the Command Palette, goto-anything (`Ctrl+P` / `Cmd+P`) and multiple selections to a mainstream editor audience. These features were widely imitated - VS Code, Atom and many others adopted them. Sublime Text 2 and 3 dominated the editor market for web developers from roughly 2011 to 2016, when VS Code's momentum began to shift the landscape.

Sublime Text is now maintained by Sublime HQ, the company Jon Skinner founded. Sublime Text 4 was released in 2021 with significant performance improvements, multi-line find-and-replace, Apple Silicon support and Tab multi-select. The editor retains a loyal userbase that values its speed and polish.

Pricing changed from perpetual-plus-paid-upgrades to a subscription-or-perpetual model. A single licence is $99 and covers all updates for three years. After three years, the licence remains valid for the last version released during the licence period, but new version updates require renewal.

**GitSavvy** was originally created by Dale Myers and is now maintained by Jan Graichen (timbrel) with community contributions. It requires Sublime Text build 4000 or later (ST4) and Git 2.18 or later.

---

## 2. Installing Sublime Text

🪟 **Windows**

```bash
winget install SublimeHQ.SublimeText.4
```

Or download from [sublimetext.com](https://www.sublimetext.com).

🍎 **macOS**

```bash
brew install --cask sublime-text
```

Or download the `.dmg` from the website.

🐧 **Linux**

```bash
# Debian/Ubuntu
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | \
  gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/sublimehq-archive.gpg > /dev/null
echo "deb https://download.sublimetext.com/ apt/stable/" | \
  sudo tee /etc/apt/sources.list.d/sublime-text.list
sudo apt update && sudo apt install sublime-text

# Fedora/CentOS
sudo rpm -v --import https://download.sublimetext.com/sublimehq-rpm-pub.gpg
sudo dnf config-manager --add-repo https://download.sublimetext.com/rpm/stable/x86_64/sublime-text.repo
sudo dnf install sublime-text
```

Sublime Text requires Git installed separately. The `subl` command is added to PATH automatically on most platforms; on macOS you may need to run `subl --add-path-to-profile`.

---

## 3. Installing Package Control and GitSavvy

**Package Control** is Sublime Text's package manager. Install it by:

1. Open the Command Palette: `Ctrl+Shift+P` (Windows/Linux) or `Cmd+Shift+P` (Mac)
2. Type `Install Package Control` and press Enter
3. Wait for the confirmation message

**Installing GitSavvy**:

1. Open the Command Palette
2. Type `Package Control: Install Package` and press Enter
3. Wait for the package list to load
4. Type `GitSavvy` and press Enter

GitSavvy installs and is immediately available in any folder that contains a `.git` directory.

**Verifying the installation**: open the Command Palette and type `git:`. A list of `git:` commands should appear. If the list is empty, the installation did not complete - try `Package Control: Satisfy Dependencies` from the Command Palette.

> [!NOTE]
> GitSavvy requires Git 2.18 or later. Some advanced rebasing features require Git 2.38 or later. Run `git --version` to check. GitSavvy does not work with Sublime Text 3 (build below 4000).

---

## 4. GitSavvy fundamentals

GitSavvy adds two types of access to Git functionality:

**Command Palette commands**: all GitSavvy commands are accessible via the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) with the prefix `git:`. This means you can type `git: status`, `git: commit`, `git: push` etc. to trigger any operation. There is fuzzy matching, so typing `gbl` finds `git: blame`.

**Dedicated views**: GitSavvy has specialised full-screen views for status, history, blame and diffs. These views are interactive - pressing keys in the view triggers actions. Every view shows context-sensitive help by pressing `?`.

**The status bar**: after GitSavvy loads, the Sublime Text status bar at the bottom shows the current branch name and whether the working tree is dirty (has uncommitted changes). This is the only persistent visual indicator.

**GitSavvy intentionally ships zero global keybindings**. This avoids conflicting with your existing bindings. You must define your own keybindings in your user keybindings file to use GitSavvy efficiently. The recommended bindings are shown in section 17.

---

## 5. The status dashboard

The `git: status` command (recommended keybinding: `Ctrl+Shift+S`) opens the **status dashboard** - GitSavvy's central hub. It is a read-only buffer showing:

```
On branch main, ahead 1, behind 0

Staged:
  M src/auth/middleware.py

Unstaged:
  M src/api/orders.py
  M tests/test_orders.py

Untracked:
  docs/new-feature.md
```

**Keys in the status view**:

| Key | Action |
|---|---|
| `s` | Stage file or hunk under cursor |
| `u` | Unstage file or hunk under cursor |
| `d` | Discard changes in file under cursor |
| `l` | Show inline diff for file under cursor |
| `=` | Toggle inline diff for file |
| `c` | Commit (opens commit view) |
| `C` | Commit with all unstaged changes included |
| `f` | Fetch all remotes |
| `p` | Pull |
| `P` | Push |
| `g` | Open Repo History |
| `b` | Open branch management |
| `t` | Open tag management |
| `ss` | Stash |
| `sa` | Apply most recent stash |
| `sp` | Pop most recent stash |
| `M` | Open merge conflict resolution |
| `?` | Show all available keybindings |

---

## 6. Staging at file, hunk and line level

GitSavvy's staging interface is one of its standout features.

**File-level staging**: in the status view, move the cursor to any file and press `s` to stage it, `u` to unstage it. Files are listed under their section (Staged, Unstaged, Untracked).

**Hunk-level staging**: press `l` or `=` on a file to expand its inline diff. The diff appears directly in the status buffer below the filename. Move the cursor to any hunk and press `s` to stage only that hunk, `u` to unstage it, `d` to discard it.

**Line-level staging**: select specific lines within a diff using normal Sublime Text selection (click and drag, or `Shift+Arrow`). With lines selected, press `s` to stage only the selected lines. This is equivalent to `git add -p` but entirely visual.

**The diff view**: the `git: diff` command opens a standalone diff view showing all unstaged changes. In this view:

- `s` stages the hunk under the cursor
- `u` unstages it (when viewing staged changes)
- `d` discards the hunk
- `l` opens the diff in the editor for direct editing

---

## 7. Committing

**Opening the commit view**: from the status view, press `c`. Or run `git: commit` from the Command Palette.

The commit view is a full Sublime Text buffer where you write the commit message. This is a key advantage over editors that use a small text box: you can write long, multi-paragraph commit messages with the full power of Sublime Text's editing (multiple cursors, Find & Replace, macros, snippets). GitSavvy shows the diff of staged changes below the message area so you can review what you are committing while writing the message.

**Commit message conventions** are enforced visually:

- The first line is highlighted differently to make the subject line obvious
- GitSavvy warns (but does not block) if the subject exceeds 72 characters

**Finalising a commit**: press `Ctrl+Enter` / `Cmd+Enter` to commit. The buffer closes and the status dashboard updates.

**Amend**: run `git: commit --amend` from the Command Palette to open the amend view with the previous commit message pre-loaded.

**Fixup commit**: in the Repo History view, navigate to a commit and press `f` to create a fixup commit targeting that commit. This stages the appropriate changes and creates a `fixup! <message>` commit that interactive rebase can automatically squash.

---

## 8. The Repo History view

The `git: repo history` command (recommended keybinding: `Ctrl+Shift+G`) opens the **Repo History** view - a graphical commit log similar to `git log --oneline --graph --all`.

The view shows:

- The commit graph with branch lanes (ASCII art)
- Commit hash
- Author and date
- Commit message
- Branch, tag and HEAD labels

**Navigation**:

| Key | Action |
|---|---|
| `Enter` | Open the commit menu for the selected commit |
| `r` | Open the rebase menu (see next section) |
| `[space]` | Select this commit (select two for diff comparison) |
| `[N]` | Walk the reflog backward (pre-rebase states) |
| `[P]` | Walk the reflog forward |
| `f` | Search commit messages (pick-axe / text filter) |
| `l` | Limit view to a specific file path |
| `a` | Toggle --all flag (show all branches vs current) |
| `q` | Close the view |

**The commit menu** (press Enter on any commit) offers:

- Checkout this commit (detached HEAD)
- Create branch from here
- Cherry-pick to current branch
- Revert this commit
- Reset HEAD to here (soft, mixed or hard)
- Copy commit hash
- Show diff from this commit to HEAD

---

## 9. Interactive rebase from Repo History

The `[r]` key in the Repo History view opens the **rebase menu** - GitSavvy's interactive rebase interface. This is one of its most powerful and least-known features.

The rebase menu shows options relative to the selected commit:

- **Reword** - change the commit message only
- **Edit** - pause the rebase at this commit to make changes
- **Squash** - squash this commit into the previous one, combining messages
- **Fixup** - squash this commit into the previous one, discarding this message
- **Drop** - remove this commit entirely
- **Autosquash** - process all `fixup!` and `squash!` commits automatically

For more complex rebase operations (reordering commits, splitting a commit):

1. Navigate to the commit just before where you want to start
2. Press `r` to open the rebase menu
3. Choose the appropriate action
4. GitSavvy runs the rebase and opens an editor for any message changes needed

The Repo History view updates live as the rebase progresses, showing the new commit graph. Press `[N]` to see the previous reflog state if you need to undo the rebase.

---

## 10. File History and Line History

**File History**: run `git: file history` from the Command Palette to see every commit that touched the current file. The view is similar to Repo History but filtered to one file. Each commit shows the file-specific diff when you press Enter.

**Line History** (run `git: line history` or `git: show line history`): select one or more lines in any file, then run this command. GitSavvy shows every commit that modified any of the selected lines, with the diff for each commit. This is the "wtf did this line do?" command - the fastest way to trace a specific piece of code through history.

The Line History view is particularly useful for:

- Understanding why a seemingly odd piece of code was written
- Finding which commit introduced a bug to a specific line
- Tracing a variable's evolution through refactors

---

## 11. Blame view

Run `git: blame` from the Command Palette to open the blame view for the current file. GitSavvy opens a two-column view: the blame metadata (commit hash, author, date) on the left and the file content on the right, scrolled together.

**Keys in the blame view**:

| Key | Action |
|---|---|
| `Enter` | Jump to the commit that modified the line under the cursor |
| `[` / `]` | Previous / next commit in the blame |
| `q` | Close the blame view |

The blame view integrates with Line History: pressing Enter on a blame entry opens that commit's details, and from there you can navigate the full history of how that code evolved.

---

## 12. Branching and remotes

GitSavvy handles branching through Command Palette commands:

| Command | Action |
|---|---|
| `git: checkout` | Switch to an existing branch (fuzzy search list) |
| `git: checkout new branch` | Create and switch to a new branch |
| `git: delete branch` | Delete a local branch |
| `git: fetch` | Fetch all remotes |
| `git: pull` | Pull from the upstream of the current branch |
| `git: push` | Push the current branch |
| `git: push --force-with-lease` | Force push safely |
| `git: merge` | Merge another branch into the current branch |

**In the status view**: press `b` to open the branch list, which shows all local and remote branches with their upstream relationships. Press `Enter` on any branch to check it out; press `d` to delete a local branch; press `r` to rename it.

For fetch and push, GitSavvy shows the command output in the status bar or a temporary buffer. If authentication is needed, it delegates to your configured Git credential manager.

---

## 13. Stashing

GitSavvy handles stashing through two-letter key combinations in the status view:

| Keys | Action |
|---|---|
| `ss` | Stash all changes (prompt for description) |
| `su` | Stash untracked files as well |
| `sa` | Apply most recent stash (leave on stack) |
| `sp` | Pop most recent stash (remove from stack) |
| `sd` | Drop most recent stash |
| `sl` | List all stashes |

From the Command Palette:

- `git: stash` - stash all changes
- `git: stash pop` - pop the most recent stash
- `git: stash apply` - apply without removing from stack
- `git: stash drop` - drop a specific stash (shows list)

---

## 14. GitHub integration

GitSavvy includes GitHub integration via `github:` prefixed commands in the Command Palette. Configure your GitHub token in GitSavvy's settings:

```json
{
  "github_per_page_results": 100,
  "github_add_forks_as_remotes": false
}
```

And set your token via the Command Palette: `GitSavvy: Set GitHub Token`.

**GitHub commands**:

| Command | Action |
|---|---|
| `github: create public fork` | Fork the current repository to your account |
| `github: open file on remote` | Open the current file on GitHub at the selected line |
| `github: open commit on remote` | Open a commit on GitHub |
| `github: browse current branch` | Open the current branch on GitHub |
| `github: create pull request` | Open the GitHub PR creation page for the current branch |
| `github: checkout pull request` | Check out a PR branch locally (shows list of open PRs) |

**In commit messages**: GitSavvy provides autocompletion for GitHub issue numbers (`#123`) and collaborator usernames (`@username`) when writing commit messages in repositories with a GitHub remote configured.

---

## 15. The integrated terminal for Git

Sublime Text does not ship an integrated terminal. To run Git commands from within Sublime Text, you need a terminal extension:

**Terminus** (recommended): a full terminal emulator inside Sublime Text. Install via Package Control: search for `Terminus`. Open it with the Command Palette: `Terminus: Open Terminal in View`.

```bash
# After installing Terminus
# Add to your keybindings:
{ "keys": ["ctrl+`"], "command": "toggle_terminus_panel" }
```

With Terminus installed, you can run any Git command from within Sublime Text. Set Sublime Text as your Git editor in the Terminus terminal:

```bash
git config --global core.editor "subl -w"
```

The `-w` flag tells Sublime to keep the process open until you close the file - equivalent to `--wait` in VS Code.

**Without Terminus**: use your system terminal alongside Sublime Text. GitSavvy covers the most common operations so you need the terminal less often than with bare editors.

---

## 16. Sublime Merge

**Sublime Merge** is a separate, standalone Git GUI client built by Sublime HQ alongside Sublime Text. It is a paid product ($99 one-time, same model as Sublime Text) but integrates naturally with Sublime Text as an external diff tool and Git viewer.

Key Sublime Merge features:

- Line-by-line staging with syntax-highlighted diffs (40+ languages)
- Editable past commits (rewording, squashing) from the history view
- Submodule management
- git-flow integration
- Blame with syntax highlighting
- LFS lock/unlock support (added April 2026)
- Search across all commits, files and diffs
- Conflict resolution with a three-pane merge view

**Using Sublime Merge from GitSavvy**: press `M` in any GitSavvy view to open the current repository in Sublime Merge. You can also right-click any file in the Sublime Text sidebar and choose `Open in Sublime Merge`.

**Sublime Merge as a diff tool**: configure Sublime Merge as your Git diff and merge tool:

```bash
git config --global diff.tool smerge
git config --global difftool.smerge.cmd 'smerge -w difftool "$LOCAL" "$REMOTE"'
git config --global merge.tool smerge
git config --global mergetool.smerge.cmd 'smerge -w mergetool "$BASE" "$LOCAL" "$REMOTE" "$MERGED"'
```

---

## 17. Recommended keybindings

GitSavvy ships zero global keybindings. Add these to your user keybindings file (`Preferences > Key Bindings`):

```json
[
  // Status dashboard - the hub
  { "keys": ["ctrl+shift+s"], "command": "gs_show_status" },

  // Repo history / commit graph
  { "keys": ["ctrl+shift+g"],
    "command": "gs_graph",
    "args": { "all": true } },

  // Commit staged changes
  { "keys": ["ctrl+shift+c"], "command": "gs_commit" },

  // Inline diff for current file
  { "keys": ["alt+d"], "command": "gs_diff" },

  // File history
  { "keys": ["alt+h"], "command": "gs_show_file_history" },

  // Line history (works on selection)
  { "keys": ["alt+l"], "command": "gs_show_line_history" },

  // Blame for current file
  { "keys": ["alt+b"], "command": "gs_blame" },

  // Push
  { "keys": ["ctrl+shift+p"],
    "command": "gs_push",
    "args": {} }
]
```

---

## 18. Pricing

**Sublime Text**: $99 one-time personal licence (includes 3 years of updates; perpetual use on last version). Business licences: $65/seat/year. Evaluation period is unlimited - Sublime Text runs fully featured without a licence with occasional purchase prompts.

**Sublime Merge**: $99 one-time personal licence (same model as Sublime Text). A **bundle** of both Sublime Text and Sublime Merge is $168 - saving $30 over buying separately.

**GitSavvy**: free and open source (MIT licence). No subscription or payment required.

---

## 19. Try It Yourself

**Exercise 1 - stage individual lines with GitSavvy**

Open a repository in Sublime Text. Make changes in two different parts of the same file for two different purposes. Run `git: status` from the Command Palette. Press `l` or `=` on the file to expand its inline diff. Select only the lines for the first change using Sublime Text's multi-line selection. Press `s` to stage only those lines. Commit with `c`. Verify with `git: repo history` that the commit contains only the first change.

**Exercise 2 - interactive rebase from Repo History**

Open Repo History with `Ctrl+Shift+G` (or your configured keybinding). Navigate to a commit three or four commits back in your history. Press `r` to open the rebase menu. Choose **Squash** to squash it with the next commit. Confirm and let GitSavvy run the rebase. Verify the history has fewer commits.

**Exercise 3 - trace a line's history**

Open a file with significant commit history. Select a line of code that looks like it may have been written for a specific reason. Run `git: line history` from the Command Palette. Read through the commits that touched this line. Understand the evolution.

**Exercise 4 - use the blame view**

Run `git: blame` on a file. Navigate to a line you are curious about. Press Enter to jump to that commit. Read the commit message and diff. Press `q` to return to the blame view. Navigate to another suspicious line.

**Exercise 5 - create a pull request from GitSavvy**

Push a feature branch to GitHub. Run `github: create pull request` from the Command Palette. Verify that the GitHub PR creation page opens in your browser, pre-filled with your branch name.

---

## 20. Common Mistakes

Not adding keybindings assumes GitSavvy has a discoverable UI without them. GitSavvy's Command Palette integration is good for finding commands, but the status view, blame view and history view are all keyboard-driven with single-key commands. Without the `?` help popup and without keybindings to open the views quickly, GitSavvy feels harder to use than it is. Add the recommended keybindings from section 17 and press `?` in each view when you first explore it.

Forgetting that GitSavvy has no default global keybindings means that `Ctrl+Shift+G` and other bindings only work after you add them to your user keybindings. If a keybinding does not work, check whether it conflicts with an existing Sublime Text or plugin binding using `Preferences > Key Bindings` (the two-column view shows conflicts).

Committing without staging when there are mixed changes. If you press `c` (commit) in the status view with no staged changes, GitSavvy commits nothing. Make sure the files you intend to commit are in the Staged section before committing.

Missing the Line History feature. `git: line history` is arguably GitSavvy's most powerful and least-known command. Select lines in any file, run the command and get the full history of those specific lines. This is more precise than `git blame` for understanding complex code evolution.

Not installing Terminus means no integrated terminal. GitSavvy covers most operations but not all. For interactive rebase fine-tuning, worktree management, filter-repo and other advanced operations, you need a terminal. Install Terminus so you have one available without switching applications.

---

## 21. Summary

Sublime Text has no built-in Git integration beyond `.gitignore` support. GitSavvy, installed via Package Control, provides the full Git workflow: a status dashboard with single-key staging at file, hunk and line level; a commit view that is a full Sublime Text buffer supporting long commit messages; a Repo History graph with an interactive rebase menu (`[r]`) supporting reword, squash, fixup, drop and autosquash; File History and Line History for per-path and per-line archaeology; a blame view with inline diff; branching and remote management through Command Palette commands; stashing via two-letter key combinations; and GitHub integration for opening files, commits and branches on GitHub and creating pull requests. GitSavvy ships zero default global keybindings - add your own via Preferences > Key Bindings. Sublime Merge, the companion standalone Git GUI, integrates via an `M` key in GitSavvy views and provides a three-pane merge tool, syntax-highlighted diffs and submodule management. The Terminus extension adds an integrated terminal for operations not covered by GitSavvy. Sublime Text is $99 with a three-year update period; GitSavvy is free.

---

## 22. Sources

- [GitSavvy repository](https://github.com/timbrel/GitSavvy)
- [GitSavvy Package Control page](https://packagecontrol.io/packages/GitSavvy)
- [Sublime Text documentation](https://www.sublimetext.com/docs/)
- [Sublime Merge documentation](https://www.sublimemerge.com/docs/)
- [Terminus package](https://packagecontrol.io/packages/Terminus)
- [Package Control](https://packagecontrol.io)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
