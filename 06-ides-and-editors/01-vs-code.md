# Visual Studio Code

**Difficulty:** 🟢 Beginner | **Time:** 35 minutes

Visual Studio Code is the most widely used code editor in the world and the reference point for Git integration across the entire industry. It is free, open-source and maintained by Microsoft and it ships a Git integration that covers the full daily workflow - staging, committing, branching, merging, diffing and conflict resolution - without a single extension installed. Its extension marketplace then layers on GitLens, GitHub Pull Requests, GitLab Workflow and GitHub Copilot to take that baseline into territory that rivals dedicated Git GUI clients. Every agentic IDE covered in the rest of this section is built on VS Code, which means everything you learn here transfers directly to Cursor, Windsurf, Antigravity and Kiro.

---

## Table of Contents

1. [History and background](#1-history-and-background)
2. [Installing VS Code](#2-installing-vs-code)
3. [The built-in Source Control panel](#3-the-built-in-source-control-panel)
4. [Staging changes](#4-staging-changes)
5. [Committing](#5-committing)
6. [Branches and remotes](#6-branches-and-remotes)
7. [Diffing and the diff editor](#7-diffing-and-the-diff-editor)
8. [The 3-way merge editor](#8-the-3-way-merge-editor)
9. [The Source Control Graph](#9-the-source-control-graph)
10. [Inline blame and gutter indicators](#10-inline-blame-and-gutter-indicators)
11. [The Timeline view](#11-the-timeline-view)
12. [The integrated terminal for Git](#12-the-integrated-terminal-for-git)
13. [GitLens](#13-gitlens)
14. [GitHub Pull Requests extension](#14-github-pull-requests-extension)
15. [GitLab Workflow extension](#15-gitlab-workflow-extension)
16. [GitHub Copilot](#16-github-copilot)
17. [GitLab Duo](#17-gitlab-duo)
18. [Key keyboard shortcuts](#18-key-keyboard-shortcuts)
19. [Try It Yourself](#19-try-it-yourself)
20. [Common Mistakes](#20-common-mistakes)
21. [Summary](#21-summary)
22. [Sources](#22-sources)

---

## 1. History and background

Microsoft released VS Code publicly in November 2015 after a preview at Build 2015. It was designed as a lightweight cross-platform editor built on Electron (Chromium + Node.js), initially targeting web developers who found full Visual Studio too heavy. Git support was part of the product from day one - Microsoft recognised that version control integration was non-negotiable for a developer tool. The extension API opened in 2016 and rapidly attracted a plugin ecosystem that dwarfed its competitors.

By 2022 VS Code had the largest share of any code editor in the Stack Overflow developer survey, a position it has held every year since. As of 2025 it has over 50,000 extensions and hundreds of millions of active users. Its influence on the industry is hard to overstate: when Cursor, Windsurf, Google Antigravity and AWS Kiro were built, their teams all chose VS Code or its open-source variant Code-OSS as the foundation, inheriting its extension API, keybindings, settings format and - crucially - its entire Git integration.

VS Code's source code is at [github.com/microsoft/vscode](https://github.com/microsoft/vscode) under the MIT licence.

---

## 2. Installing VS Code

🪟 **Windows**

```bash
winget install Microsoft.VisualStudioCode
```

Or download the installer from [code.visualstudio.com](https://code.visualstudio.com). The system installer is recommended for most users; it adds `code` to your PATH so you can open files from the terminal with `code filename.md` or open a folder with `code .`.

🍎 **macOS**

```bash
brew install --cask visual-studio-code
```

Or download the `.zip` from the website, unzip it and drag the app to your Applications folder. After installing, open the Command Palette (`Cmd+Shift+P`) and run `Shell Command: Install 'code' command in PATH`.

🐧 **Linux**

Debian/Ubuntu:

```bash
sudo apt install apt-transport-https
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] \
  https://packages.microsoft.com/repos/code stable main" | \
  sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null
sudo apt update
sudo apt install code
```

Fedora/RHEL:

```bash
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'
sudo dnf install code
```

VS Code requires Git to be installed separately. See [git/02-installing-git.md](../02-git/02-installing-git.md) if you have not done that yet. VS Code needs Git 2.0.0 or later; install a current release (2.53 or above) to get the latest features and fixes.

---

## 3. The built-in Source Control panel

Open the Source Control panel with `Ctrl+Shift+G` on Windows/Linux or `Cmd+Shift+G` on Mac or click the branching icon in the Activity Bar on the left. VS Code detects Git repositories automatically when you open a folder that contains a `.git` directory. If the folder is not yet a repository, the panel shows an **Initialize Repository** button.

The panel is divided into sections:

- **Merge Changes** - files with merge conflicts that need resolution (only visible during a merge or rebase)
- **Staged Changes** - files added to the index with `git add`, ready to be committed
- **Changes** - files with modifications not yet staged
- **Untracked** - new files that Git is not yet tracking (shown in some configurations)

Each file entry has a one-letter indicator: `M` (modified), `A` (added/staged), `D` (deleted), `U` (untracked), `C` (conflict). Hovering over a file shows action icons: open file, discard changes (unstaged) and stage/unstage. Right-clicking a file gives you the full context menu including **Stage Selected Ranges**, **Open Changes**, **Open File**, **Revert File** and **Copy Relative Path**.

The three-dot menu (`...`) at the top of the panel is the overflow menu. It contains every Git operation that does not have a dedicated button: pull, push, fetch, clone, stash, cherry-pick, rebase, merge and more. If you cannot find a Git operation, look here first or use the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) and type `Git:` to see every available command.

---

## 4. Staging changes

**Staging a whole file**: click the `+` icon next to the file name in the Changes section or right-click and select **Stage Changes**.

**Staging a hunk**: click on the file to open the diff editor. Right-click on any hunk (a grouped block of changes) and select **Stage Hunk**. Alternatively, select specific lines in the diff and right-click to choose **Stage Selected Ranges** - this stages only the highlighted lines.

**Staging all changes at once**: click the `+` icon next to the **Changes** heading at the section level or run `Git: Stage All Changes` from the Command Palette.

**Unstaging**: click the `-` icon next to any staged file or right-click and choose **Unstage Changes**. To unstage everything at once, click the `-` on the **Staged Changes** heading.

**Discarding changes**: right-click a file in Changes and select **Discard Changes** to revert it to the last committed state. This is equivalent to `git checkout -- <file>`. You can also discard a hunk from inside the diff editor.

> [!CAUTION]
> Discarding changes in VS Code is immediate and permanent for unstaged modifications. There is no trash or undo. Make sure you want to throw away those changes before clicking.

---

## 5. Committing

Type your commit message in the text box at the top of the Source Control panel and press `Ctrl+Enter` (Windows/Linux) or `Cmd+Enter` (Mac) to commit. You can also click the **Commit** button (the tick icon).

**AI commit message generation**: if you have a Copilot subscription, a sparkle icon appears in the commit message box. Clicking it sends your staged diff to Copilot and populates the box with a generated message. The message follows conventional commit style by default. You can edit it before committing.

**Amending the last commit**: click the `...` overflow menu and select **Commit Staged (Amend)**. This opens a text editor with the previous commit message so you can modify it. The amended commit replaces the previous one in your local history.

**Commit and push in one step**: the dropdown arrow next to the **Commit** button exposes **Commit & Push** and **Commit & Sync** options. Commit & Sync pulls before pushing, which is safer if others are working on the same branch.

**Empty commits**: to create a commit with no staged changes (useful for triggering CI), run `Git: Commit Empty` from the Command Palette.

---

## 6. Branches and remotes

**Current branch**: the branch name is always visible in the Status Bar at the bottom left of the window. Click it to open the Branches picker.

**Switching branches**: click the branch name in the Status Bar and choose from the list. If the branch does not exist locally, VS Code offers to check it out from the remote.

**Creating a branch**: click the branch name, then type a new name. VS Code runs `git checkout -b <name>` for you. You can also run `Git: Create Branch` from the Command Palette.

**Deleting a branch**: right-click a branch in the Branches section of the Source Control panel sidebar and select **Delete Branch**. VS Code will warn you if the branch has unmerged commits.

**Pushing**: click the push icon in the Status Bar (the cloud with an up arrow) or use `Ctrl+Shift+P > Git: Push`. If the branch has no upstream, VS Code offers to publish it to the configured remote.

**Pulling**: click the pull icon (the cloud with a down arrow) or use `Git: Pull`. The Status Bar shows how many commits are ahead and behind the remote.

**Fetching**: `Git: Fetch` updates your local remote-tracking branches without merging anything. Use this to see what has changed on the remote before deciding to pull.

**Managing remotes**: run `Git: Add Remote` or `Git: Remove Remote` from the Command Palette. You can also edit `.git/config` directly and then refresh.

---

## 7. Diffing and the diff editor

Clicking on any modified file in the Source Control panel opens the diff editor. By default this is a side-by-side view with the previous version on the left and your changes on the right. Deletions are shown in red, additions in green.

**Switching between unified and side-by-side**: use the toggle button in the top-right corner of the diff editor or set `"diffEditor.renderSideBySide": false` in your settings.

**Inline diff in the editor**: without opening the full diff view, VS Code shows coloured gutter indicators (green for added lines, blue for modified, red triangles for deleted). Clicking any indicator opens an inline peek diff with **Stage Hunk**, **Revert Hunk** and navigation arrows.

**Comparing arbitrary refs**: use `Git: Open Comparison` from the Command Palette to compare branches, tags or commits. You can also right-click a file in the Explorer and choose **Select for Compare**, then right-click another file and **Compare with Selected**.

**Word-level diffs**: VS Code highlights changed words within lines in addition to whole lines. This is controlled by `"diffEditor.wordWrap"` and `"diffEditor.diffAlgorithm"` settings.

---

## 8. The 3-way merge editor

When VS Code encounters a merge conflict, it marks the conflicting files with `!` in the Source Control panel. Opening a conflicting file shows the standard conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`). You can edit these manually or click **Resolve in Merge Editor** at the top of the file to open the 3-way merge editor.

The merge editor has three panes:

- **Incoming** (top left) - the changes from the branch being merged in
- **Current** (top right) - your changes on the current branch
- **Result** (bottom) - the final output you are building

Each conflict has checkboxes: **Accept Incoming**, **Accept Current**, **Accept Both** (current first), **Accept Both** (incoming first). For simple conflicts, clicking one of these is enough. For complex conflicts where you need parts of both, edit the Result pane directly.

> [!TIP]
> The **Accept Both** options let you keep changes from both sides in sequence. If you need to reorder or merge them differently, accept both and then edit the Result pane by hand.

Once all conflicts are resolved in the Result pane, click **Complete Merge** to stage the file automatically. Repeat for each conflicting file, then commit as normal.

---

## 9. The Source Control Graph

The Source Control Graph is a commit history visualisation built into VS Code. Access it from the Source Control panel by clicking the graph icon or run `Git: Show Git Graph` from the Command Palette.

The graph shows:

- The commit history for the current repository with branch, tag and HEAD labels
- Author avatars and commit messages
- Relationships between commits - merges, rebases and diverging branches are all visible

From the graph you can right-click any commit to: checkout, create a branch from it, cherry-pick it, revert it, reset to it (soft, mixed or hard), copy the commit hash or view the full diff.

**Filtering**: the search box filters commits by message, author or hash. The branch picker in the top bar limits the graph to a specific branch or all branches.

The Source Control Graph was promoted out of experimental in VS Code 1.93 (August 2024) and continues to gain features in each release.

---

## 10. Inline blame and gutter indicators

**Inline blame** shows the commit author, date and message for the current line at the end of the line in faded text, without opening any separate panel. Enable it with `git.blame.editorDecoration.enabled: true` in settings or toggle it with `Git: Toggle Inline Blame` from the Command Palette. Hovering over the blame annotation shows a popup with the full commit hash, author email and a **Show Commit** link that opens the diff for that commit.

**Gutter indicators** appear in the thin column between the line numbers and the code:

- Green bar: line was added since the last commit
- Blue bar: line was modified since the last commit
- Red triangle: one or more lines were deleted above this point

Clicking a gutter indicator opens a peek panel with the diff for that hunk and buttons to **Stage Hunk**, **Revert Hunk** and navigate between hunks with `Alt+F3` / `Shift+Alt+F3`.

Both features use your local Git history and update in real time as you edit.

---

## 11. The Timeline view

The Timeline view shows the complete history of the currently open file. Open it at the bottom of the Explorer panel or via `View > Open View > Timeline`.

Each entry in the Timeline is either a Git commit that touched the file, a local save (if VS Code's autosave creates timeline entries) or an extension event. Clicking a Git entry opens a diff showing exactly what changed in that commit for that specific file. This is equivalent to `git log -p -- <filename>` but with a UI.

The Timeline is useful for tracking how a specific file evolved over time without leaving the editor, especially when you cannot remember which commit introduced a particular change.

---

## 12. The integrated terminal for Git

Open the integrated terminal with `` Ctrl+` `` on Windows/Linux or `` Cmd+` `` on Mac. You can have multiple terminal sessions - the `+` icon creates a new one and the dropdown switches between them.

VS Code wires two important environment variables into the integrated terminal:

- `GIT_EDITOR` is set to `code --wait`, so `git commit` and `git rebase -i` open their files inside VS Code rather than in nano or vim. The `--wait` flag tells VS Code to keep the process open until you close the tab.
- `GIT_ASKPASS` handles credential prompts through VS Code's UI.

This means you get the full power of the VS Code editor when writing commit messages or editing rebase todo files from the terminal. You also get syntax highlighting for rebase todo files (`pick`, `squash`, `reword` etc.) through VS Code's language support.

**Useful Git commands to run in the VS Code terminal**:

```bash
# Interactive rebase - opens todo file in VS Code
git rebase -i HEAD~5

# View a specific file at a previous commit
git show HEAD~3:src/main.py

# Find which commit introduced a bug
git bisect start
git bisect bad
git bisect good v1.0

# Clean up all merged local branches
git branch --merged main | grep -v "^\* main$" | xargs git branch -d

# Show a compact graph of all branches
git log --oneline --graph --all --decorate
```

> [!TIP]
> Run `code <filename>` from the VS Code terminal to open a file in the editor instantly. Run `code .` to open the current directory. This works even if VS Code is already running - it opens the file in the existing window.

---

## 13. GitLens

GitLens is the most popular Git extension for VS Code, with over 40 million installs. It is published by GitKraken and available free from the VS Code Marketplace. Install it by opening the Extensions panel (`Ctrl+Shift+X` / `Cmd+Shift+X`) and searching for GitLens.

**Free Community features**:

- **Current-line blame** at the end of every line with commit author, date and message
- **Git CodeLens** above functions and classes showing the most recent commit that touched them
- **File History** view listing every commit that modified the current file
- **Line History** view tracking how a specific selection changed over time
- **Heatmap annotations** colour-coding lines by how recently they were changed (hot = recent, cold = old)
- **Commit Details** side panel showing the full diff and metadata for any selected commit
- **Interactive Rebase Editor** - a UI for reordering, rewording, squashing and dropping commits
- **Stash management** with a dedicated Stashes view

**Pro features** (requires a GitLens Pro subscription, approximately $10/month):

- **Commit Graph** - a full visual graph of your repository history with branch lanes, filters by author/branch/date and interactive rebase from the graph
- **Visual File History** - a timeline chart showing the evolution of a file with bubbles sized by the number of changes in each commit
- **Worktrees** view for managing multiple working trees
- **Cloud Patches** - sharing work-in-progress diffs with teammates via an encrypted link before committing
- **Launchpad** - a unified inbox for pull requests and issues across GitHub, GitLab, Bitbucket and Azure DevOps, showing what needs review, what is blocked and what you should work on next
- **GitKraken AI** - AI-powered commit message generation, commit explanations and AI-assisted PR descriptions

**Key GitLens views** (accessible from the GitLens icon in the Activity Bar):

- **Home** - shows your current branch, recent branches, the Launchpad summary and quick actions
- **Commits** - the commit log for the current branch with inline diffs
- **Branches** - all local and remote branches with tracking info and quick checkout
- **Remotes** - configured remotes and their branches
- **Stashes** - all stashes with preview diffs
- **Tags** - all tags
- **Worktrees** - all active worktrees (Pro)
- **Contributors** - a leaderboard of commit authors

**Configuring GitLens**: most features can be toggled individually in VS Code settings under `gitlens.*`. If the line blame annotations are too distracting, try `gitlens.currentLine.enabled: false` and use the hover blame instead.

---

## 14. GitHub Pull Requests extension

The **GitHub Pull Requests** extension (by GitHub, not GitKraken) lets you create, review and manage GitHub pull requests directly inside VS Code. Install it from the Marketplace by searching for **GitHub Pull Requests**.

After installing, sign in to GitHub via the Accounts icon in the Activity Bar or the prompt that appears when you open a GitHub repository.

**Creating a pull request**:

1. Make your commits on a feature branch
2. Open the Source Control panel and click **Create Pull Request** (or run `GitHub Pull Requests: Create Pull Request` from the Command Palette)
3. Select the base branch and fill in the title and description
4. If your repository has a pull request template, it is loaded automatically
5. Set reviewers, labels, assignees and milestone from the sidebar
6. Click **Create** - VS Code offers to publish your branch to the remote if it is not already there

**Reviewing a pull request**:

1. Open the Pull Requests panel (GitHub icon in the Activity Bar)
2. Click any open PR to enter Review Mode
3. All changed files appear in a PR-specific file tree
4. Click a file to open its diff in the diff editor
5. Hover over any line and click the comment icon to leave an inline comment
6. Use suggestion blocks to propose specific code changes that the author can accept with one click
7. Click **Submit Review** to approve, request changes or leave a comment
8. Click **Merge Pull Request** when the PR is ready

**Working on issues**: the extension also shows GitHub Issues in a sidebar. Click any issue to see its details. Use the **Start Working on Issue** button to automatically create a branch named after the issue (for example `issue-42-fix-login-bug`) and switch to it. The issue number is auto-linked in commit messages and PR descriptions.

**Hover cards**: in any file that mentions `@username` or `#123`, hovering shows a card with the user's GitHub profile or the issue/PR details without leaving the editor.

---

## 15. GitLab Workflow extension

The **GitLab Workflow** extension brings GitLab merge requests, pipelines and CI/CD status into VS Code. Install it from the Marketplace by searching for **GitLab Workflow**.

After installing, add your GitLab account via `GitLab: Add Account` from the Command Palette. For GitLab.com, use a personal access token with `api` scope. For self-managed instances, configure the instance URL in settings.

**Key features**:

- **Merge request creation** - create MRs from the current branch with a form pre-filled from the branch name
- **Pipeline status** in the Status Bar - a coloured icon shows whether the current branch's last pipeline passed, failed or is running; click it to see the full pipeline log
- **MR review** - browse open MRs, see their diffs and leave comments from inside VS Code
- **GitLab Duo Chat** - the Duo sidebar panel lets you ask questions about your codebase and get AI responses powered by GitLab's models
- **Code Suggestions** - GitLab Duo's inline AI completions (requires a GitLab subscription with Duo features)
- **Snippet creation** - create GitLab snippets from selected code

> [!NOTE]
> GitLab Duo's IDE integration is more limited than GitHub Copilot in VS Code. Code Suggestions are available but the agent and multi-file editing capabilities of Copilot have no equivalent in Duo for VS Code as of April 2026. Duo's strongest features are on the GitLab web platform itself.

---

## 16. GitHub Copilot

GitHub Copilot became a built-in feature of VS Code in version 1.116 (February 2026). New users no longer need to install a separate extension - Copilot is present from the first launch. The chat panel is `Ctrl+Alt+I` / `Cmd+Alt+I`; inline chat is `Ctrl+I` / `Cmd+I`.

**Copilot's Git-related capabilities in VS Code**:

- **AI commit messages** - the sparkle icon in the commit message box generates a message from your staged diff. The message uses conventional commit format by default and can be regenerated or edited freely.
- **Merge conflict resolution** - in the 3-way merge editor, Copilot can suggest how to resolve the conflict in the Result pane. Click the Copilot icon that appears above conflicting regions.
- **Agent mode** - in the Copilot chat panel, switching to Agent mode lets Copilot run tools: it can read files, run terminal commands (including `git` commands), edit files and iterate until the task is complete. You can ask it to "create a feature branch called feature/login-redesign, implement the changes in src/auth.py and commit with an appropriate message".
- **PR description generation** - when creating a pull request via the GitHub Pull Requests extension, Copilot can generate the PR title and description from the commits and diffs.
- **Code review** - `@workspace /review` asks Copilot to review your uncommitted changes and suggest improvements before you commit.

**Copilot model picker**: as of April 2026, the model picker in the chat panel includes Claude Sonnet 4.6 (1x usage multiplier), Claude Opus 4.6 (3x), GPT-5 and several variants. Different models have different strengths - Claude models tend to produce more careful reasoning; GPT-5 variants are fast and good at code generation.

**Pricing**:

| Plan       | Price                                        | Premium requests |
| ---------- | -------------------------------------------- | ---------------- |
| Free       | $0                                           | 50/month         |
| Pro        | $10/month                                    | 300/month        |
| Pro+       | $39/month                                    | 1,500/month      |
| Business   | $19/user/month                               | 300/month        |
| Enterprise | $39/user/month (on top of GitHub Enterprise) | 300/month        |

Overages are billed at $0.04 per premium request.

---

## 17. GitLab Duo

GitLab Duo is available in VS Code through the GitLab Workflow extension. It provides **Code Suggestions** (inline AI completions as you type) and **Duo Chat** (a sidebar AI assistant for questions about your code, Git history and project). Both require a GitLab subscription that includes Duo features.

**Code Suggestions**: similar to Copilot completions - grey ghost text appears as you type, Tab to accept. Duo's suggestions are generated by GitLab's own AI models trained with security and code quality in mind.

**Duo Chat for Git questions**: you can ask Duo questions like "explain this merge conflict", "what changed in the last 5 commits?" or "write a commit message for these staged changes". Duo has access to your local file context via the extension.

**Limitations compared to Copilot in VS Code**: Duo does not have an agent mode, cannot run terminal commands, cannot create branches or commits autonomously and does not have a PR creation workflow inside the IDE. These capabilities live on the GitLab web platform rather than in the VS Code extension. For developers who want IDE-side agentic Git workflows and use GitLab, the common pattern is to use Copilot for in-IDE AI assistance alongside the GitLab platform for MR and CI/CD workflows.

---

## 18. Key keyboard shortcuts

| Action                     | Windows/Linux                               | Mac                      |
| -------------------------- | ------------------------------------------- | ------------------------ |
| Open Source Control panel  | `Ctrl+Shift+G`                              | `Cmd+Shift+G`            |
| Open Command Palette       | `Ctrl+Shift+P`                              | `Cmd+Shift+P`            |
| Open integrated terminal   | `` Ctrl+` ``                                | `` Cmd+` ``              |
| Open Extensions panel      | `Ctrl+Shift+X`                              | `Cmd+Shift+X`            |
| Commit (in commit box)     | `Ctrl+Enter`                                | `Cmd+Enter`              |
| Stage hunk (in diff)       | right-click > Stage Hunk                    | right-click > Stage Hunk |
| Next change in diff        | `F7`                                        | `F7`                     |
| Previous change in diff    | `Shift+F7`                                  | `Shift+F7`               |
| Next merge conflict        | `Alt+F3`                                    | `Opt+F3`                 |
| Previous merge conflict    | `Shift+Alt+F3`                              | `Shift+Opt+F3`           |
| Toggle inline blame        | Command Palette: `Git: Toggle Inline Blame` | same                     |
| Open Copilot chat          | `Ctrl+Alt+I`                                | `Cmd+Alt+I`              |
| Copilot inline chat        | `Ctrl+I`                                    | `Cmd+I`                  |
| Accept Copilot suggestion  | `Tab`                                       | `Tab`                    |
| Switch branch (Status Bar) | click branch name                           | click branch name        |

---

## 19. Try It Yourself

**Exercise 1 - stage individual lines**

Open a file and make several unrelated changes in different parts of the file. Open the Source Control panel and click on the file to see its diff. Select only the lines for one of the changes in the diff editor. Right-click and choose **Stage Selected Ranges**. Verify in the Staged Changes section that only those lines are staged. Commit that partial change with a descriptive message.

**Exercise 2 - create and review a pull request without leaving VS Code**

If you have a GitHub repository and the GitHub Pull Requests extension installed, create a new branch, make a small change, commit and push it, then use the extension to create a pull request. Open your own PR in the Pull Requests panel and leave yourself a comment on a specific line of the diff. Merge the PR from inside VS Code.

**Exercise 3 - use the 3-way merge editor**

Create two branches from the same commit. On each branch, edit the same line of the same file differently. Merge one branch into the other to create a conflict. Open the conflicting file and click **Resolve in Merge Editor**. Use the Incoming and Current panes to understand both sets of changes, then build the correct Result in the Result pane. Complete the merge and commit.

**Exercise 4 - interactive rebase with VS Code as the editor**

In the integrated terminal, run:

```bash
git rebase -i HEAD~3
```

The rebase todo file opens inside VS Code. Reorder a commit by dragging the line, change `pick` to `squash` on one commit, save and close the file. VS Code signals the rebase to continue. If Git needs you to edit a commit message during squash, it opens that file in VS Code as well.

**Exercise 5 - explore GitLens inline blame**

Install GitLens and open any file in a repository with a reasonable commit history. Enable inline blame from the Command Palette. Read through the file and hover over several blame annotations to see the commit details. Click **Show Commit** to jump to the full diff for that commit. Then open the File History view to see the list of all commits that touched the file.

---

## 20. Common Mistakes

Staging everything at once when you have unrelated changes mixed together is one of the most common VS Code Git mistakes. VS Code makes it easy to stage individual lines and hunks - use that capability to keep commits focused on a single purpose. A commit that fixes a bug, reformats a function and updates a config file is harder to review and harder to revert than three separate focused commits.

Clicking Discard Changes without realising it is permanent catches many beginners. Unlike staging and committing, discarding unstaged changes cannot be undone through Git. If you are unsure whether you want to keep changes, stash them first (`Git: Stash`) rather than discarding.

Using the overflow menu (`...`) without exploring it fully means missing useful operations. The overflow menu contains stash, cherry-pick, rebase, merge and many other operations that have no dedicated button. Developers who never open it end up going to the terminal for things VS Code can do natively.

Ignoring the Output panel when debugging Git problems is a missed opportunity. When VS Code's Git operations behave unexpectedly, `View > Output > Git` shows the exact commands and their output. This is often the fastest way to understand why a push was rejected or why a fetch returned an unexpected result.

Assuming Copilot commit messages are always correct is a mistake. Copilot generates messages based on the staged diff, but it can produce vague or inaccurate messages for complex or subtle changes. Always read the generated message and edit it if needed before committing.

---

## 21. Summary

VS Code ships a capable Git integration out of the box that handles staging at line and hunk level, committing with AI-generated messages, branching and remote management, a 3-way merge editor for conflict resolution, a Source Control Graph for history, inline blame and gutter indicators. GitLens adds a richer Git history UI, a Commit Graph and collaborative features. The GitHub Pull Requests extension brings PR creation and review into the editor. GitHub Copilot adds AI assistance for commit messages, merge conflicts, code review and autonomous agent tasks. GitLab Workflow adds pipeline status and Duo AI for GitLab users. The integrated terminal with `GIT_EDITOR` set to VS Code means even complex terminal Git operations like interactive rebase feel natural inside the editor. Every agentic IDE covered later in this section inherits this entire baseline - learning VS Code's Git integration is the most efficient foundation for understanding the whole landscape.

---

## 22. Sources

- [VS Code Source Control documentation](https://code.visualstudio.com/docs/sourcecontrol/overview)
- [VS Code Git quickstart](https://code.visualstudio.com/docs/sourcecontrol/quickstart)
- [VS Code GitHub integration](https://code.visualstudio.com/docs/sourcecontrol/github)
- [VS Code December 2025 release notes](https://code.visualstudio.com/updates/v1_108)
- [GitLens documentation](https://help.gitkraken.com/gitlens/gitlens-home/)
- [GitLens pricing](https://www.gitkraken.com/pricing)
- [GitHub Pull Requests extension repository](https://github.com/microsoft/vscode-pull-request-github)
- [GitLab Workflow extension documentation](https://gitlab.com/gitlab-org/gitlab-vscode-extension)
- [GitHub Copilot in VS Code documentation](https://code.visualstudio.com/docs/agents/overview)
- [GitHub Copilot pricing](https://github.com/features/copilot#pricing)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
