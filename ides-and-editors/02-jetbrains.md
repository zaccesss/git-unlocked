# JetBrains IDEs

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

JetBrains produces the most complete built-in Git integration of any IDE family. IntelliJ IDEA, PyCharm, WebStorm, Rider, GoLand and CLion all share the same underlying Git implementation - learn it once and you know it across every JetBrains product. What you get is not just a staging panel but a full Git porcelain: changelists, shelving, an interactive rebase editor, a three-pane merge tool, a searchable commit log with a graphical branch view, cherry-pick, blame annotations, partial commits, and deep GitHub and GitLab integration. This file covers the Git workflow across the entire JetBrains family and explains the concepts unique to JetBrains that have no equivalent in VS Code.

---

## Table of Contents

1. [History and background](#1-history-and-background)
2. [JetBrains IDE family and pricing](#2-jetbrains-ide-family-and-pricing)
3. [Installing a JetBrains IDE](#3-installing-a-jetbrains-ide)
4. [The Git menu and VCS operations popup](#4-the-git-menu-and-vcs-operations-popup)
5. [The Commit tool window](#5-the-commit-tool-window)
6. [Changelists](#6-changelists)
7. [Shelving vs stashing](#7-shelving-vs-stashing)
8. [The Log tab and commit history](#8-the-log-tab-and-commit-history)
9. [Branching and remotes](#9-branching-and-remotes)
10. [Interactive rebase](#10-interactive-rebase)
11. [The three-pane merge tool](#11-the-three-pane-merge-tool)
12. [Annotate with Git Blame](#12-annotate-with-git-blame)
13. [Partial commits](#13-partial-commits)
14. [GitHub integration](#14-github-integration)
15. [GitLab integration](#15-gitlab-integration)
16. [The integrated terminal for Git](#16-the-integrated-terminal-for-git)
17. [JetBrains AI Assistant](#17-jetbrains-ai-assistant)
18. [Android Studio](#18-android-studio)
19. [Key keyboard shortcuts](#19-key-keyboard-shortcuts)
20. [Try It Yourself](#20-try-it-yourself)
21. [Common Mistakes](#21-common-mistakes)
22. [Summary](#22-summary)
23. [Sources](#23-sources)

---

## 1. History and background

JetBrains was founded in Prague in 2000 by three Russian developers - Sergey Dmitriev, Valentin Kipyatkov and Eugene Belyaev - who built IntelliJ IDEA as a smarter Java IDE. The company's philosophy from the start was deep language understanding over generic text editing: every IDE knows the semantics of its primary language, not just its syntax, so refactoring, navigation and code analysis work at a level that generic editors cannot match. Git integration was added in IntelliJ IDEA 8 (2008) alongside Subversion and CVS support, and has been refined continuously since. The `Git4Idea` plugin - the shared Git implementation across all JetBrains IDEs - is now one of the most complete GUI Git implementations available.

IntelliJ IDEA 2025.3, released December 2025, made a significant licensing change: the Community edition was expanded to include core Java, Kotlin, SQL, JavaScript and TypeScript support for **free forever including commercial use**, removing the previous restriction that required an Ultimate subscription for commercial JavaScript/TypeScript work. Ultimate remains the paid tier with advanced framework tooling, database support and full AI Assistant integration.

---

## 2. JetBrains IDE family and pricing

All IDEs share the same Git implementation. The differences are in language support and specialised tooling.

| IDE | Primary language(s) | Community (free) | Ultimate pricing |
|---|---|---|---|
| IntelliJ IDEA | Java, Kotlin, Scala | Yes (Java/Kotlin) | ~$719/year individual |
| PyCharm | Python | Yes (core Python) | ~$299/year individual |
| WebStorm | JavaScript, TypeScript | No | ~$299/year individual |
| Rider | C#, .NET, Unity | No | ~$299/year individual |
| GoLand | Go | No | ~$299/year individual |
| CLion | C, C++, Rust | No | ~$299/year individual |

All Products Pack (all IDEs including AI Pro): ~$979/year individual. Student and open-source licences are available free of charge. The **All Products Pack** is the best value if you work across multiple languages.

**JetBrains AI Assistant** is a separate add-on: Free (3 credits/month, local models only), Pro (~$10/month, cloud AI models), Ultimate (~$30/month, frontier models including Junie agentic coding and Claude integration). AI Pro is bundled with All Products Pack.

---

## 3. Installing a JetBrains IDE

The recommended way to install and manage JetBrains IDEs is **JetBrains Toolbox App**, a launcher that handles installation, updates and licence management for all JetBrains products.

🪟 **Windows**

Download Toolbox from [jetbrains.com/toolbox-app](https://www.jetbrains.com/toolbox-app/) and run the installer. After installing Toolbox, open it and install whichever IDE you need. Toolbox places each IDE on the PATH automatically.

🍎 **macOS**

```bash
brew install --cask jetbrains-toolbox
```

Or download from the website. After installing Toolbox, install your IDE from the Toolbox interface.

🐧 **Linux**

```bash
# Download the Toolbox tarball
tar -xzf jetbrains-toolbox-*.tar.gz
./jetbrains-toolbox-*/jetbrains-toolbox
```

Toolbox installs to `~/.local/share/JetBrains/Toolbox` and adds shell scripts to `~/.local/bin`.

JetBrains IDEs require Java and ship their own bundled JDK - you do not need to install Java separately. Git must be installed separately; see [git/02-installing-git.md](../git/02-installing-git.md). JetBrains detects your system Git automatically on first launch.

---

## 4. The Git menu and VCS operations popup

When a project is under Git version control, IntelliJ IDEA replaces the generic **VCS** menu with a **Git** menu. This menu is the primary way to trigger Git operations that do not have a keyboard shortcut. Key items include:

- **Fetch** - fetch all remotes
- **Pull** - pull with merge or rebase (configurable per repository)
- **Push** - push current branch
- **Merge** - merge a branch into the current branch
- **Rebase** - rebase the current branch onto another
- **Reset HEAD** - soft, mixed or hard reset
- **Tag** - create, push or delete tags
- **Stash Changes / Unstash Changes** - standard git stash
- **Shelve Changes** - JetBrains-specific (covered below)
- **Patch** - create or apply a patch file
- **Show Git Log** - open the Log tab in the Version Control tool window

The **VCS Operations Popup** (`` Alt+` `` on Windows/Linux, `` Ctrl+V `` on Mac) is a quick-access menu for the most common operations. It is context-aware - it shows different options depending on what is selected in the editor or file tree.

**The Version Control tool window** (`Alt+9` on Windows/Linux, `Cmd+9` on Mac) is the central hub. It has three tabs: **Local Changes** (the staging area), **Log** (the commit graph and history) and **Console** (Git command output). Open it whenever you need to see the current repository state.

---

## 5. The Commit tool window

Press `Ctrl+K` (Windows/Linux) or `Cmd+K` (Mac) to open the Commit tool window. This is the primary commit interface and replaces the older **Commit Changes** dialog from earlier IntelliJ versions.

The Commit tool window shows all modified files grouped into changelists (see the next section). For each file you can:

- Tick or untick to include or exclude from the commit
- Click the file to open its diff in the main editor area
- Right-click to stage/unstage individual files, open the full diff, show history, or revert

**The commit message box** is at the bottom of the panel. It shows a character counter for the subject line (the first line should be under 72 characters). Press `Ctrl+Enter` / `Cmd+Enter` to commit immediately, or click the **Commit** button. Click the dropdown arrow next to **Commit** to access **Commit and Push** in one step.

**Commit checks** run automatically before each commit and can be configured per project:

- Reformat code
- Optimise imports
- Analyse code (run inspections)
- Check TODO items
- Clean up code
- Run tests
- Check for unresolved merge conflicts
- Detect malicious dependencies

Disable any check by unchecking it in the commit panel. These run locally before the commit is created - they do not replace CI.

**Amend**: tick the **Amend** checkbox at the top of the commit message box to amend the previous commit. The previous message and the changes already in that commit are loaded automatically. Useful for fixing typos or adding forgotten files to the last commit before pushing.

**AI commit message**: JetBrains AI Assistant adds a **Generate Commit Message** action (sparkle icon) to the commit message box. It analyses your staged diff and produces a Conventional Commits-style message. Available with AI Pro or Ultimate.

---

## 6. Changelists

Changelists are JetBrains' mechanism for organising local file modifications into logical groups before committing. They have **no equivalent in VS Code** and no parallel in the Git index - they are an IDE-only concept.

Every modified file belongs to exactly one changelist. By default there is a single changelist called **Default Changelist**. You can create additional changelists and move files between them:

1. In the Commit tool window or the Local Changes tab, right-click any file or group
2. Select **Move to Another Changelist** or **New Changelist**
3. Name the new changelist (for example, "Refactor auth" or "Fix #142")

When you commit, you commit the contents of one changelist at a time. This lets you work on two features simultaneously (editing files for both) and commit them separately without worrying about the Git index at all.

**How changelists map to Git staging**: when you click Commit, JetBrains stages the selected changelist's files temporarily, creates the commit and then unstages them - all behind the scenes. The Git index is used only transiently. If you run `git status` in a terminal while working with changelists, you will see the working tree state, not a staged state, because JetBrains manages staging at commit time rather than as a separate step.

**Git Staging mode**: if you prefer working directly with the Git index (for compatibility with external tools or to match the VS Code mental model), enable **Git Staging mode** in `Settings > Version Control > Git > Stage changes manually using Git staging area`. This replaces changelists with a Staged/Unstaged pair that maps directly to `git add`.

---

## 7. Shelving vs stashing

JetBrains gives you two ways to temporarily set aside work: **stashing** (standard Git) and **shelving** (JetBrains-specific).

**Git stash** (via `Git > Stash Changes`):

- Saves changes to `git stash` - portable, visible to the CLI and other tools
- Stashes the entire working tree by default; partial stash is possible but requires the CLI
- Applies with `Git > Unstash Changes`, which shows a list of all stashes
- Works across machines because stashes are stored in the Git object store

**JetBrains shelve** (via `Git > Shelve Changes`):

- Saves changes as a `.patch` file in `.idea/shelf/` (not tracked by Git)
- Supports per-file and per-hunk granularity from the Shelve dialog
- Visible only inside JetBrains IDEs on the same machine
- Not affected by Git operations - safe to shelve before a merge, rebase or branch switch
- Can have a custom name and description

The 2026 recommendation from JetBrains documentation is: **use shelves for granular WIP that stays on one machine, use stashes for anything that must survive a machine switch or be visible to CLI tools**.

---

## 8. The Log tab and commit history

The **Log tab** in the Version Control tool window (`Alt+9`) is the most powerful commit history viewer in any IDE. Open it and you see a graphical representation of your repository's commit history with branch lanes, merge lines and tag/branch labels.

**Columns**: Hash, Subject, Author, Date. Click any column header to sort. The branch graph on the left shows the topology visually.

**Filtering**: the toolbar has filters for:

- **Branch** - limit to commits reachable from a specific branch or all branches
- **User** - filter by commit author
- **Date** - filter by date range
- **Path** - filter to commits that touched a specific file or folder
- **Text** - search commit messages

**Viewing a commit**: click any row to open the **Commit Details** panel on the right. It shows the full commit message, author, date, hash and the diff of every file changed in that commit. Click any file in the diff to open it in the main editor.

**Actions from the Log**: right-click any commit to:

- **Checkout** this commit (detached HEAD)
- **Create Branch** from this commit
- **Cherry-Pick** this commit to the current branch
- **Revert Commit** (creates a new commit that undoes the changes)
- **Reset Current Branch to Here** (soft, mixed or hard)
- **Interactively Rebase from Here** (see the next section)
- **Copy Revision Number** (the commit hash)
- **Open on GitHub / GitLab** (opens the commit in the browser)

**Multi-repository projects**: if your project contains multiple Git repositories (a monorepo pattern or a project with submodules), the Log tab shows all of them in one unified view with a colour indicator per repository.

---

## 9. Branching and remotes

**The Branches popup**: press `Ctrl+Shift+\`` `` (backtick) on Windows/Linux or `Ctrl+G` on Mac to open the Branches popup, which lists all local and remote branches. Start typing to filter. Press Enter or double-click to checkout. The current branch is shown at the top.

**Creating a branch**: from the Branches popup, click **+ New Branch**. Enter the name and choose whether to checkout immediately. JetBrains runs `git checkout -b <name>` behind the scenes.

**Comparing branches**: right-click any branch in the popup and choose **Compare with Current**. This opens a diff view showing commits that are in one branch but not the other - useful for understanding what a feature branch contains before merging.

**Merging**: right-click a branch and choose **Merge into Current**. JetBrains prompts for merge options (squash, no-fast-forward, fast-forward only).

**Rebasing**: right-click a branch and choose **Rebase Current onto Selected** or use `Git > Rebase`.

**Fetch and pull**: `Ctrl+T` opens the Update Project dialog, which defaults to your configured strategy (merge or rebase). `Git > Fetch` fetches without merging. The Branches popup shows ahead/behind counts for each branch relative to its remote.

**Force push with lease**: when pushing commits that rewrite history, JetBrains always uses `--force-with-lease` instead of `--force`. This is safer because it fails if someone else has pushed to the branch since your last fetch, preventing you from overwriting their work.

---

## 10. Interactive rebase

The interactive rebase editor is one of JetBrains' most distinctive features. To access it, right-click any commit in the Log tab and choose **Interactively Rebase from Here**. You can also run `Git > Rebase` and choose interactive mode.

The editor opens a table showing all commits between the selected commit and HEAD, ordered oldest at the top and newest at the bottom (the opposite of the standard rebase todo file ordering). Each row has:

- A **tick/untick** checkbox to include or exclude (equivalent to `pick` vs `drop`)
- A dropdown action: **Pick**, **Reword**, **Squash**, **Fixup**, **Edit**, **Drop**
- The commit hash
- The commit message

You can drag rows up and down to reorder commits. Reordering is live - JetBrains shows a preview of the resulting commit graph on the right side.

To squash commits: change the action of the commits you want to squash to **Squash** (keeps the message) or **Fixup** (discards the message). The combined message is built automatically from all squashed commit messages. After clicking **Start Rebasing**, JetBrains handles the rebase and resolves straightforward conflicts automatically, pausing only when manual intervention is needed.

> [!NOTE]
> Interactive rebase rewrites commit history. If you have already pushed the commits being rebased, you will need to force push after the rebase completes. Do not rebase commits that others have based work on.

---

## 11. The three-pane merge tool

When a merge or rebase creates a conflict, JetBrains opens the three-pane merge tool automatically for each conflicting file. The three panes are:

- **Left (Local)** - your changes on the current branch
- **Right (Incoming/Server)** - the changes from the branch being merged or rebased
- **Centre (Result)** - the final output you are building, starting from the common ancestor

Each conflict has chevron buttons (`>>` and `<<`) to accept the left or right change, and an X to reject it. For complex conflicts where you need parts of both sides, click **Accept Left/Right** for the parts you want and then edit the Result pane directly.

**Magic Resolve**: the wand icon at the top of the merge tool attempts to auto-resolve non-overlapping changes. It applies all changes from both sides that do not conflict and leaves only the genuinely conflicting regions for manual resolution. In practice this handles a large proportion of conflicts automatically.

**Resolve with AI**: with JetBrains AI Assistant, a **Resolve with AI** button appears above conflicting regions. It reads both sides and the common ancestor and proposes a resolution in the Result pane. You can accept, modify or reject the suggestion.

**Finishing**: once all conflicts are resolved in the Result pane, click **Apply** to save the file in its resolved state. JetBrains stages the resolved file automatically. After resolving all files, return to the Commit tool window and commit the merge.

---

## 12. Annotate with Git Blame

To see blame annotations for the current file, right-click in the gutter (the grey margin to the left of line numbers) and choose **Annotate with Git Blame**, or use the keyboard shortcut `Ctrl+Shift+Alt+B` on Windows/Linux. The gutter fills with coloured blocks showing the commit hash, author and date for each line.

Clicking any blame annotation opens the **Commit Details** panel showing the full commit message, author, date and diff for that commit. From the Commit Details panel you can cherry-pick the commit, revert it, create a branch from it, or open it on GitHub/GitLab.

**Navigate through history**: with a line selected and blame active, press `Ctrl+Shift+A` / `Cmd+Shift+A` and search for **Show Diff from Annotation** to see the diff for the commit that last modified the selected line. This is equivalent to `git show <hash>` but opens the diff in JetBrains' diff viewer.

**Hide blame**: right-click the gutter again and choose **Close Annotations**, or use the same keyboard shortcut that opened it.

---

## 13. Partial commits

In the Commit tool window, you can commit a subset of the changes in a changelist:

- **File-level**: untick specific files to exclude them from the commit. They stay in the changelist and appear in the next commit.
- **Hunk-level**: click on a file to open its diff. In the diff, right-click any hunk and choose **Include or Exclude Changes** to add or remove specific hunks from the current commit.
- **Line-level**: if you need line-level precision, use the **Move to Another Changelist** action on specific hunks and then commit changelists separately.

This capability - committing only part of a file's changes - is what changelists make easy. In VS Code you would need to stage individual lines explicitly using the diff editor. In JetBrains you organise changes into changelists first and then commit cleanly.

---

## 14. GitHub integration

GitHub integration is bundled into all JetBrains IDEs through the **GitHub plugin**, which is enabled by default. Add your GitHub account in `Settings > Version Control > GitHub` using a personal access token or by authorising through the browser.

**Key GitHub features in JetBrains IDEs**:

- **Clone from GitHub**: `File > New > Project from Version Control > GitHub` shows your repositories and organisation repos in a searchable list
- **Create pull request**: `Git > GitHub > Create Pull Request` opens a dialog pre-filled with the branch name and the diff. Set reviewers, labels, milestone and base branch. The PR is created on GitHub and a link appears in the IDE
- **View pull requests**: `Git > GitHub > View Pull Requests` lists open PRs with their status. Clicking a PR shows its diff, checks status and review comments
- **Submit review**: leave inline comments on PR diffs and submit reviews without opening the browser
- **Open on GitHub**: right-clicking a file, commit or branch in most views shows an **Open on GitHub** option that opens the corresponding page in your browser

GitHub Actions status is not shown natively in the JetBrains IDE. The separate **GitHub Actions** plugin (marketplace) adds a sidebar showing workflow run status and logs.

---

## 15. GitLab integration

GitLab support in JetBrains IDEs is provided by two plugins: the **official GitLab plugin** (`GitLab`) published by GitLab on the JetBrains Marketplace, and the third-party **JetLab** plugin. As of 2026, the official GitLab plugin is the recommended choice for most users.

**Official GitLab plugin** features:

- **Connect to GitLab**: add your GitLab instance (GitLab.com or self-managed) via `Settings > Version Control > GitLab` using a personal access token
- **Clone from GitLab**: searchable list of your projects and group projects
- **Merge request creation**: `Git > GitLab > Create Merge Request` with configurable target branch, description, reviewers and labels
- **MR review**: browse open MRs, view their diffs and leave comments from inside the IDE
- **Pipeline status**: a pipeline indicator in the Status Bar showing pass/fail/running for the current branch; click it for the full pipeline log
- **GitLab Duo Code Suggestions**: in-editor AI completions powered by GitLab Duo (requires a GitLab subscription with Duo features)

**JetLab** (third-party) is still in use by teams who need advanced CI/CD YAML tooling or custom features not in the official plugin. Check the JetBrains Marketplace for the latest release status before choosing between them.

---

## 16. The integrated terminal for Git

Open the integrated terminal with `Alt+F12` on Windows/Linux or `Opt+F12` on Mac. JetBrains' terminal supports multiple tabs, split panes and shell integration.

Like VS Code, JetBrains sets `GIT_EDITOR` to open files in the IDE when Git needs an editor. Running `git commit` without `-m` in the terminal opens the commit message in a new editor tab inside the IDE. Running `git rebase -i HEAD~5` opens the rebase todo file with syntax highlighting and reorder support.

JetBrains also adds a **natural-language-to-shell** mode in the terminal when AI Assistant is enabled. Press `Ctrl+\` / `Cmd+\` to open an AI prompt bar above the terminal input. Type a description like "fetch all remotes and show branches that are ahead of main" and AI Assistant generates the corresponding shell command. You can review it before running.

**Checking the Git console**: while the terminal shows interactive output, the **Console tab** in the Version Control tool window (`Alt+9`) shows every Git command the IDE has run and its output. This is invaluable for debugging unexpected IDE behaviour.

---

## 17. JetBrains AI Assistant

JetBrains AI Assistant integrates directly into the Git workflow across several touchpoints:

**In the Commit tool window**:

- **Generate Commit Message** (sparkle icon) - analyses the staged diff and produces a Conventional Commits-style message. Regenerate as many times as needed.
- **Self-Review with AI** - reviews your changes before committing and reports potential issues in the Problems tool window tab. Catches things like missing null checks, unused variables introduced in the diff, or inconsistencies with the rest of the codebase.

**In the merge tool**:

- **Resolve with AI** - proposes a resolution for conflicting hunks based on both sides and the common ancestor

**In the diff editor**:

- **Explain with AI** - asks the assistant to explain what a specific change does in plain English. Useful when reviewing unfamiliar code before merging.

**In the Log tab**:

- **Explain Commit** - generates a plain-English explanation of what a selected commit changed and why, based on the diff and the commit message

**AI Assistant pricing**: Free (3 credits/month, limited to local models via Ollama), Pro (~$10/month, cloud models including JetBrains' Mellum), Ultimate (~$30/month, frontier models including Claude integration, Junie agentic coding). AI Pro is included in the All Products Pack subscription.

---

## 18. Android Studio

Android Studio is Google's Android development IDE, built on IntelliJ IDEA. It inherits **every Git feature covered in this file**: changelists, shelving, the Log tab, interactive rebase, the three-pane merge tool, blame annotations and the Commit tool window - all with identical keyboard shortcuts.

The main difference is the AI assistant. Android Studio ships **Gemini in Android Studio** rather than JetBrains AI Assistant. Gemini generates commit messages, explains code, answers questions about the Android SDK, and can run agentic tasks scoped to Android development.

GitHub and GitLab integration work identically to the base IntelliJ IDEA setup. Google's own **Firebase** and **Google Cloud** integrations are Android Studio-specific, but they do not affect the Git workflow.

Android Studio is free to download and use. It runs on Windows, macOS and Linux.

---

## 19. Key keyboard shortcuts

| Action | Windows/Linux | Mac |
|---|---|---|
| Open Commit tool window | `Ctrl+K` | `Cmd+K` |
| Commit | `Ctrl+Enter` (in commit box) | `Cmd+Enter` |
| Push | `Ctrl+Shift+K` | `Cmd+Shift+K` |
| Update project (pull) | `Ctrl+T` | `Cmd+T` |
| Branches popup | `` Ctrl+Shift+` `` | `Ctrl+G` |
| VCS Operations popup | `` Alt+` `` | `Ctrl+V` |
| Open Version Control window | `Alt+9` | `Cmd+9` |
| Open terminal | `Alt+F12` | `Opt+F12` |
| Annotate with Git Blame | right-click gutter > Annotate | right-click gutter > Annotate |
| Fetch | `Git > Fetch` | `Git > Fetch` |
| Stash | `Git > Stash Changes` | `Git > Stash Changes` |
| Next difference (in diff) | `F7` | `F7` |
| Previous difference (in diff) | `Shift+F7` | `Shift+F7` |
| Accept left side (merge) | `Ctrl+Left` | `Cmd+Left` |
| Accept right side (merge) | `Ctrl+Right` | `Cmd+Right` |
| Cherry-pick (from Log) | right-click commit > Cherry-Pick | right-click commit > Cherry-Pick |

---

## 20. Try It Yourself

**Exercise 1 - use changelists for two features at once**

Open a repository and make changes to files for two unrelated purposes - for example, fix a bug in one file and add a new feature in another. In the Commit tool window, right-click the feature file and choose **New Changelist**. Name it "Add feature". Move the feature file into that changelist. Now commit only the bug fix from the Default Changelist. Then switch to the "Add feature" changelist and commit that separately. Notice how changelists let you work on both without ever explicitly staging or unstaging.

**Exercise 2 - interactive rebase from the Log tab**

In the Log tab, find a sequence of several recent commits. Right-click the oldest commit in the sequence and choose **Interactively Rebase from Here**. In the rebase editor, squash two adjacent commits by setting the second one to **Fixup**. Drag two commits to reorder them. Click **Start Rebasing** and watch JetBrains handle the rebase. Check the Log tab afterwards to see the new commit graph.

**Exercise 3 - explore blame annotations**

Open any file with a long history. Right-click the gutter and choose **Annotate with Git Blame**. The gutter fills with colour-coded blocks. Click on several different blocks to see the Commit Details for each one. Find the commit that introduced a particular function or block. From the Commit Details panel, right-click and choose **Open on GitHub** to verify the commit exists on the remote.

**Exercise 4 - shelve and restore changes**

Make several changes to a file that you are not ready to commit. Go to `Git > Shelve Changes`. Give the shelf a descriptive name. Verify in the file that your changes are gone. Switch to a different branch, do some work, then switch back. Go to `Git > Unshelve Changes`, select your shelf and apply it. Verify that your changes are restored exactly as they were.

**Exercise 5 - create a pull request from inside the IDE**

Push a feature branch to GitHub. Go to `Git > GitHub > Create Pull Request`. Fill in the title and description. Add a reviewer. Click **Create**. Open the Pull Requests list from the same menu and find your new PR. Click it to see the diff and the review status.

---

## 21. Common Mistakes

Confusing changelists with the Git index is a common mistake for developers coming from VS Code. Changelists are IDE-only groupings; they do not map to staged files in the Git sense. If you run `git status` in the terminal while using changelists, you will see the working tree state as if nothing is staged. JetBrains stages and unstages silently at commit time. If you enable **Git Staging mode** in settings, you switch to the VS Code mental model where staged and unstaged map directly to the Git index.

Shelving changes and forgetting about them is easy to do. Shelves are stored in `.idea/shelf/` which is typically gitignored. If you reinstall the IDE or delete the `.idea/` folder, your shelves are gone. Keep track of what you have shelved and apply or discard shelves when they are no longer needed.

Using force push without `--force-with-lease` is unnecessary in JetBrains because the IDE always uses `--force-with-lease` by default. If you override this from the terminal with raw `git push --force`, you lose the safety net. Prefer `git push --force-with-lease` in the terminal as well.

Ignoring the Console tab in the Version Control tool window means missing the most useful debugging resource. When an IDE Git operation behaves unexpectedly - a push fails, a merge produces unexpected results, a rebase stops partway - the Console tab shows the exact commands and their output. Read it before assuming a bug.

Dismissing the commit checks without reading them causes avoidable problems. JetBrains' pre-commit checks (reformat, optimise imports, analyse code) exist for a reason. If you habitually click past them, you accumulate formatting debt and missed issues. Take 30 seconds to read what each check found before deciding to suppress it.

---

## 22. Summary

JetBrains IDEs share a single, mature Git implementation that covers the full workflow - staging, committing, changelists, shelving, branching, merging, rebasing, blame and PR management - built into the core product with no extensions required. Changelists let you organise work across multiple features without touching the Git index explicitly. Shelving gives you finer-grained temporary storage than git stash. The Log tab provides a graphical commit history with filtering and all common history-modifying operations accessible via right-click. The interactive rebase editor makes squashing, reordering and rewording commits visual and low-risk. The three-pane merge tool with Magic Resolve and AI-assisted resolution handles most merge conflicts cleanly. GitHub and GitLab integrations support PR and MR creation and review from inside the IDE. JetBrains AI Assistant adds commit message generation, self-review and AI-assisted conflict resolution. Every feature described here applies equally to IntelliJ IDEA, PyCharm, WebStorm, Rider, GoLand, CLion and Android Studio.

---

## 23. Sources

- [JetBrains Git integration documentation](https://www.jetbrains.com/help/idea/using-git-integration.html)
- [JetBrains changelists and Git staging documentation](https://foojay.io/today/intellij-idea-changelists-and-git-staging/)
- [JetBrains shelving documentation](https://www.jetbrains.com/help/idea/shelving-and-unshelving-changes.html)
- [JetBrains interactive rebase documentation](https://www.jetbrains.com/help/idea/edit-project-history.html)
- [JetBrains AI Assistant documentation](https://www.jetbrains.com/help/ai-assistant)
- [JetBrains pricing](https://www.jetbrains.com/all/)
- [IntelliJ IDEA 2025.3 unified release announcement](https://blog.jetbrains.com/idea/2025/12/intellij-idea-unified-release/)
- [GitLab plugin for JetBrains](https://plugins.jetbrains.com/plugin/22857-gitlab)
- [Android Studio documentation](https://developer.android.com/studio)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
