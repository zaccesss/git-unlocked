# Other Editors and IDEs

**Difficulty:** 🟢 Beginner | **Time:** 30 minutes

The editors covered in the previous files represent the most widely used tools for Git-integrated development in 2026. But the landscape is broader than that. This file covers the significant editors and IDEs that did not get their own dedicated file: Emacs with Magit (widely considered the gold standard Git porcelain), Visual Studio (Microsoft's full Windows IDE, distinct from VS Code), Xcode (Apple's macOS IDE for iOS and macOS development), Android Studio (Google's Android IDE built on IntelliJ), Eclipse with EGit (the long-established Java IDE), Nova (Panic's macOS editor), Vim classic and nano and Notepad++ at the minimal end. Each section covers the Git integration, the platform, the key features and when you would choose it.

---

## Table of Contents

1. [Emacs and Magit](#1-emacs-and-magit)
2. [Visual Studio](#2-visual-studio)
3. [Xcode](#3-xcode)
4. [Android Studio](#4-android-studio)
5. [Eclipse and EGit](#5-eclipse-and-egit)
6. [Nova](#6-nova)
7. [Vim classic](#7-vim-classic)
8. [nano and Notepad++](#8-nano-and-notepad)
9. [Choosing from the long tail](#9-choosing-from-the-long-tail)
10. [Try It Yourself](#10-try-it-yourself)
11. [Common Mistakes](#11-common-mistakes)
12. [Summary](#12-summary)
13. [Sources](#13-sources)

---

## 1. Emacs and Magit

🪟🍎🐧 | Free (GNU GPL) | Generation 1

**Background**

Emacs is the oldest editor in active daily use by a significant number of developers. Created by Richard Stallman at MIT in 1976 (with the modern GNU Emacs version arriving in 1984), it is a programmable environment in which the editor itself is written in Emacs Lisp and every behaviour can be customised or replaced. The learning curve is steep and the keybindings are unusual by modern standards, but developers who invest in learning Emacs tend to stay for decades.

**Magit** is a Git interface built as an Emacs package, maintained by Jonas Bernoulli and Kyle Meyer. It is genuinely in a different category from other editor Git integrations - Tim Pope's description of vim-fugitive ("so awesome it should be illegal") is widely considered a reference to Magit, which set the benchmark that fugitive aspired to.

**Installing Magit**

```bash
# Emacs 29+ with use-package built in
# Add to your init.el:
(use-package magit
  :bind ("C-x g" . magit-status))
```

Or via MELPA (the Emacs package registry):

```
M-x package-install RET magit RET
```

**The Magit status buffer**

`C-x g` or `M-x magit-status` opens the status buffer. This is Magit's hub, showing:

- Untracked files
- Unstaged changes (with expandable inline diffs)
- Staged changes (with expandable inline diffs)
- Recent commits
- Unpulled and unpushed commits with counts

**Key bindings in the status buffer**:

| Key   | Action                            |
| ----- | --------------------------------- |
| `s`   | Stage file or hunk under cursor   |
| `u`   | Unstage file or hunk under cursor |
| `k`   | Discard change under cursor       |
| `Tab` | Toggle section expand/collapse    |
| `c c` | Commit                            |
| `c a` | Amend last commit                 |
| `c w` | Reword last commit message        |
| `c f` | Create fixup commit               |
| `c s` | Create squash commit              |
| `P p` | Push to upstream                  |
| `F p` | Pull from upstream                |
| `b b` | Switch branch                     |
| `b c` | Create new branch                 |
| `r i` | Interactive rebase                |
| `z z` | Stash                             |
| `z p` | Pop stash                         |
| `l l` | Open log                          |
| `?`   | Show all available commands       |

**What makes Magit exceptional**

Magit's power comes from three things. First, **transient menus** (the library that powers Magit's popup system is now a core part of GNU Emacs): every major operation opens a transient menu showing all available flags and options for that command, discoverable by pressing `?`. You can toggle `--force-with-lease`, `--rebase`, `--no-ff` and dozens of other flags directly from the menu without memorising them.

Second, **region-aware staging**: Magit can stage not just files and hunks but arbitrary regions of text. Select three lines in the middle of a hunk using Emacs region selection, press `s` and only those three lines are staged. This level of granularity is unmatched by any other editor Git integration.

Third, **the log and commit graph**: Magit's log view (`l l`) renders the full commit graph, allows filtering by author, date range, file path and commit message and makes every commit interactively actionable (cherry-pick, revert, reset, rebase, tag, copy hash) with a single keypress.

**Forge**: the `forge` Emacs package extends Magit with GitHub, GitLab and Gitea/Forgejo support. Forge maintains a local SQLite database of issues, pull requests and review comments, updated via the platform's API. From inside Emacs you can list open PRs, read their diffs, leave inline comments, approve reviews and merge - all without opening a browser. Bootstrap Forge with `M-x forge-add-repository` in a project directory.

**Who should use it**: developers already invested in Emacs or developers who work extensively with Git history (rebasing, archaeology, complex branching) and want the most keyboard-efficient interface available. The learning investment is real but the payoff for heavy Git users is significant.

---

## 2. Visual Studio

🪟 | Paid (Community free for individuals) | Generation 1 + 2

**Background**

Visual Studio is Microsoft's full IDE for Windows, distinct from VS Code. Where VS Code is a lightweight cross-platform editor, Visual Studio is a heavyweight Windows-only environment designed for .NET, C++, Unity, game development and enterprise applications. Visual Studio 2026 (v18.0, released November 12, 2025) is the current version and is marketed as "the first Intelligent Developer Environment". Visual Studio 2022 (v17.x) remains the LTSC line.

Visual Studio does not run on macOS (the macOS version was discontinued in August 2024) or Linux.

**Git integration**

Visual Studio replaced the older Team Explorer Git UI with the **Git Changes window** and **Git Repository window** in Visual Studio 2019 (v16.8, November 2020). The current UI is significantly more capable than the original Team Explorer.

**Git Changes window** (`Ctrl+0, Ctrl+G`): staging, committing, amending, stashing and branch management. The commit message box integrates with Copilot for AI-generated messages. A **Self-Review with AI** button (Visual Studio 2026) runs Copilot over your staged changes and reports potential issues before you commit.

**Git Repository window** (`Ctrl+0, Ctrl+R`): the commit graph with filtering, interactive rebase UI (added in VS 2022), branch management with create/delete/rename/merge, remote management, cherry-pick and reset (soft, mixed, hard). Right-click any commit to access the full action menu.

**Pull request review** (added as preview in VS 2026): list open PRs, view their diffs with inline commenting and submit reviews without leaving Visual Studio. This bridges the gap with VS Code's GitHub Pull Requests extension.

**GitHub Actions integration**: the GitHub Actions extension (bundled in VS 2026) shows workflow run status and logs in a dedicated panel. Failures link directly to the failing step's log output.

**Azure DevOps integration**: Visual Studio has deep Azure DevOps support via the Team Explorer pane - work item linking, Azure Repos browsing, pipeline status and board management. This is not replicated in VS Code's Azure DevOps extensions to the same depth.

**TFVC**: Team Foundation Version Control (Microsoft's centralised VCS predating Git) remains supported alongside Git but is in maintenance mode. New projects should use Git.

**Copilot in Visual Studio**

GitHub Copilot is deeply integrated in Visual Studio 2026:

- AI commit message generation (steerable via `.github/copilot-instructions.md`)
- Copilot code review before commit with inline suggestions
- Agent mode for multi-file tasks
- "Add to Copilot Chat as context" on selected commits in the history view

**Pricing**

- Community: free for individuals, open-source projects and small organisations (up to 5 users)
- Professional: approximately $45/month or $1,199 standalone
- Enterprise: approximately $250/month or $5,999 year 1

**Who should use it**: .NET, C++, Unity and Windows application developers who need the deepest language tooling and Azure DevOps integration available. For web or Python development, VS Code is a lighter and more widely supported choice.

---

## 3. Xcode

🍎 | Free (App Store) | Generation 1 + 2

**Background**

Xcode is Apple's IDE for iOS, iPadOS, macOS, watchOS and tvOS development. It is free, available only on macOS and required for building and distributing apps in the Apple ecosystem. Apple moved to year-based versioning at WWDC 2025 - the current release is **Xcode 26** (not Xcode 17), with Xcode 26.3 adding agentic coding via MCP.

**Source Control Navigator**

Open with `Cmd+2`. The Source Control Navigator has two tabs:

- **Changes** - staged and unstaged changes per repository, with the diff on the right
- **Repositories** - all configured repositories with branches, tags, remote branches and stashed changes

**Source Control menu**

The **Integrate** menu (renamed from Source Control in Xcode 26) contains the full Git command surface:

| Menu item            | Action                                           |
| -------------------- | ------------------------------------------------ |
| Commit (`Alt+Cmd+C`) | Stage and commit                                 |
| Push                 | Push current branch                              |
| Pull                 | Pull from upstream                               |
| Fetch                | Fetch all remotes                                |
| Rebase               | Rebase current branch onto another               |
| Merge                | Merge a branch into the current branch           |
| Cherry-Pick          | Cherry-pick a commit                             |
| Stash Changes        | Create a stash                                   |
| Discard All Changes  | Revert working tree to HEAD                      |
| New Branch           | Create and switch to a new branch                |
| Create Pull Request  | Open the GitHub/Bitbucket PR form in the browser |

**Commit workflow**

`Alt+Cmd+C` opens the commit dialog. This is a split view: the left panel lists changed files with checkboxes for inclusion; the right panel shows the diff. Write the commit message in the box at the top. Tick or untick files to control what is staged. Click **Commit** to create the commit.

**Blame**

Right-click any line in the editor and choose **Show Blame for Line**. Xcode opens a blame tooltip showing the commit hash, author, date and commit message. Right-click the blame annotation to jump to the commit in the Version Editor.

The **Version Editor** (toggle with `Alt+Cmd+Return`) has three modes: Comparison (side-by-side diff), Blame and Log.

**Platform integrations**

Xcode authenticates with GitHub and Bitbucket via personal access tokens. Go to **Xcode > Settings > Accounts**, click `+` and add a GitHub account. With authentication set up, you can clone from GitHub, push to GitHub and create pull requests directly from the **Integrate > Create Pull Request** menu item.

**Xcode 26 AI features**

- **Coding Assistant** - built-in ChatGPT (free tier, OpenAI account for more) and native Claude OAuth integration
- **BYOK** - configure any OpenAI Chat Completions-compatible API endpoint, including local MLX models
- **Agentic coding via MCP** - launched in Xcode 26.3, connects the Coding Assistant to MCP servers for tool-augmented coding tasks
- **Predictive code completion** - on-device model, requires Apple Silicon

**Limitations**

Xcode's Git UI is functional but not as deep as VS Code's or JetBrains' implementations. There is no interactive rebase UI (use the terminal: `git rebase -i`), the 3-way merge editor is basic compared to JetBrains' and the commit graph is less navigable than Magit or IntelliJ's Log tab. For complex Git operations, most iOS developers use the terminal alongside Xcode.

**Who should use it**: iOS, macOS, watchOS and tvOS developers who need Xcode for building and signing. There is no alternative for Apple platform development.

---

## 4. Android Studio

🪟🍎🐧 | Free | Generation 1 + 2

**Background**

Android Studio is Google's official Android development IDE, built on IntelliJ IDEA by JetBrains. Google uses IntelliJ as the base, adds Android-specific tooling (the Android SDK manager, emulator integration, Layout Editor, Profiler, APK analyser) and ships the result as Android Studio.

Because it is an IntelliJ fork, Android Studio has **the same complete Git integration as IntelliJ IDEA**. Every feature in [02-jetbrains.md](02-jetbrains.md) applies identically: changelists, shelving, the Log tab, interactive rebase editor, three-pane merge tool, Git Blame annotations, the Commit tool window with pre-commit checks and GitHub/GitLab plugins. The keyboard shortcuts are the same (`Ctrl+K` commit, `Ctrl+Shift+K` push, `Alt+9` Version Control window).

**Gemini in Android Studio**

The AI assistant in Android Studio is **Gemini in Android Studio** rather than JetBrains AI Assistant. It provides:

- AI commit message generation in the Commit tool window
- Code suggestions and completions with Gemini models
- Chat assistance for Android-specific questions (SDK APIs, Jetpack components, Compose)
- Studio Bot for longer-form development assistance

Gemini in Android Studio is available free within usage limits.

**Who should use it**: Android developers. There is no equivalent tool for Android development on other IDEs - Android Studio's emulator integration, Layout Editor and SDK manager are essential for Android work.

---

## 5. Eclipse and EGit

🪟🍎🐧 | Free (EPL 2.0) | Generation 1

**Background**

Eclipse is an open-source IDE that dominated Java development from roughly 2003 to 2014. It is built on a plugin architecture where every feature - language support, build tools, version control - is a plugin. Eclipse's Git support is provided by **EGit**, the Eclipse Git Team Provider, built on **JGit** (a pure-Java Git implementation also used by JetBrains for some operations).

Eclipse IDE 2026-03 R is the current release, following Eclipse's quarterly release cadence. Usage has declined significantly since IntelliJ IDEA became dominant in Java development, but Eclipse retains a significant userbase in legacy Java/OSGi enterprise applications, AUTOSAR embedded development and academic settings.

**EGit features**

EGit is bundled with Eclipse IDE for Java Developers and most other Eclipse IDE packages. It provides:

**Git Staging view**: drag files from the **Unstaged Changes** pane to the **Staged Changes** pane to stage them. Hunk-level staging requires right-clicking a specific change. The commit message box supports spell checking and `Amend` / `Sign-off` / `Sign commit` checkboxes.

**History view**: a graphical commit log with branch lanes, filtering by author, date and path and commit-level actions (cherry-pick, revert, create branch, create tag, reset, checkout). Click any commit to see its full diff in the Compare Editor.

**Reflog view**: shows the reflog for recovery after rebases, resets and other history-modifying operations.

**Synchronize view**: shows incoming and outgoing changes relative to the remote, with merge and rebase actions.

**Compare Editor**: a three-pane merge conflict resolution tool with accept-left/accept-right/accept-both buttons per hunk.

**Branching**: right-click any branch in the Git Repositories view to checkout, merge, rebase onto, delete or rename.

**Interactive rebase**: EGit has had interactive rebase support since version 4.x. Right-click a commit in the History view and choose **Rebase Interactive**. The rebase editor shows the standard pick/squash/fixup/drop/edit/reword options.

**GPG-signed commits**: EGit supports commit signing with GPG, configured in `Window > Preferences > Team > Git > Configuration`.

**Limitations**: EGit does not support stash management via the GUI (use the terminal), blame view is limited compared to JetBrains or GitLens and the UI feels dated compared to modern alternatives. There are no AI features in EGit.

**Who should use it**: teams maintaining existing Eclipse-based Java projects, AUTOSAR developers using Eclipse-based toolchains and developers in organisations that have standardised on Eclipse and cannot change tooling. For new Java projects, IntelliJ IDEA Community (now free for commercial use) is a significantly better experience.

---

## 6. Nova

🍎 | Paid ($99) | Generation 1

**Background**

Nova is a macOS-only code editor built by Panic, the Portland-based company also known for Transmit (FTP/SFTP client) and Prompt (iOS SSH client). It was released in September 2020 as the successor to Panic's earlier Coda editor. Nova is a native macOS application using AppKit - not Electron - which gives it good performance and a Mac-native feel.

Nova is available only on macOS. It has no Windows or Linux version.

**Git integration**

Nova has a built-in **Source Control** sidebar. Click the branching icon in the sidebar to access it.

The sidebar shows:

- The current branch name with ahead/behind counts
- Changed files grouped by status (modified, added, deleted, untracked)
- Staged changes

**Key Git operations in Nova**:

- Stage/unstage files and individual hunks from the sidebar
- Write commit messages and commit from the sidebar commit box
- View a diff for any changed file in a slide-out panel
- **Show Last Change for Line**: right-click any line in the editor and choose this option to see who last changed it and why - a blame popover with the commit hash, author and message
- Push and pull via the **Source Control** menu
- Branch switching and creation via the branch name in the toolbar
- View commit history per file via the **History** button in the source editor toolbar

**Extensions**

Nova's extension marketplace has a GitHub extension that adds:

- GitHub Actions status in a dedicated panel
- Repository cloning from GitHub with your authenticated account
- Creating pull requests (opens GitHub in the browser)

**Pricing**: $99 for the first year of updates, then $49/year optional renewal. Once you stop renewing, the last version you downloaded continues to work indefinitely. Nova does not have a free tier.

**Who should use it**: macOS developers who want a native, non-Electron editor with a polished Mac feel and do not need Windows or Linux support. Commonly used by web developers and designers who work alongside Transmit and Prompt in a Mac-native workflow.

---

## 7. Vim classic

🪟🍎🐧 | Free (charityware) | Generation 1

**Background**

Vim (Vi IMproved) was created by Bram Moolenaar in 1991 as an improvement on the original Unix `vi` editor. Bram Moolenaar died in August 2023. Vim 9.1 is the current stable release, maintained by the community. Development pace has slowed significantly compared to Neovim's trajectory.

Classic Vim is still widely used and relevant, particularly on servers where Neovim may not be installed, in SSH sessions and among developers who learned Vim before Neovim became the dominant choice. Every Linux server has `vi` or `vim` available.

**Git integration: vim-fugitive**

The primary Git plugin for Vim is **vim-fugitive** by Tim Pope, which works identically in classic Vim and Neovim. All `:Git` / `:G` commands, the status buffer, diff splits, blame and interactive rebase from [03-neovim.md](03-neovim.md) apply directly.

**Installing vim-fugitive in classic Vim** (without a plugin manager):

```bash
mkdir -p ~/.vim/pack/tpope/start
cd ~/.vim/pack/tpope/start
git clone https://tpope.io/vim/fugitive.git
vim -u NONE -c "helptags fugitive/doc" -c q
```

With **vim-plug** (the most popular Vim plugin manager):

```vim
" In ~/.vimrc
call plug#begin()
Plug 'tpope/vim-fugitive'
Plug 'airblade/vim-gitgutter'  " Gutter signs (vim-only alternative to gitsigns.nvim)
call plug#end()
```

**vim-gitgutter** provides the signcolumn diff indicators that `gitsigns.nvim` provides in Neovim. It is less feature-complete (no hunk staging from the gutter, no virtual-text blame) but covers the visual indicator use case.

**What classic Vim cannot do that Neovim can**:

- `gitsigns.nvim` - Neovim only (use `vim-gitgutter` instead)
- `neogit` - Neovim only
- `diffview.nvim` - Neovim only
- `lazy.nvim` - Neovim only (use `vim-plug` or native packages instead)

For classic Vim users, vim-fugitive covers the full daily Git workflow. The experience is slightly less polished than the Neovim stack but fully functional.

**Setting Vim as Git editor**:

```bash
git config --global core.editor vim
```

**Who should use it**: developers on systems where only Vim is available (most servers), developers who prefer Vim's compatibility guarantees and Vim users who do not want to migrate to Neovim's Lua-based configuration.

---

## 8. nano and Notepad++

**nano** 🐧🍎 | Free (GNU GPL) | No Git integration

nano is a simple terminal text editor included by default on most Linux distributions and macOS. It has no Git integration whatsoever beyond being usable as `core.editor` for writing commit messages.

**Setting nano as your Git editor**:

```bash
git config --global core.editor nano
```

When `git commit` opens nano, write the commit message, press `Ctrl+O` to save and `Ctrl+X` to exit. This is the minimum viable Git editor workflow - functional but no staging panel, no diff view, no history, no blame.

nano is useful for: writing commit messages on servers where nothing else is installed, quick edits in SSH sessions and as the default editor for users who are not yet comfortable with Vim or Helix.

---

**Notepad++** 🪟 | Free (GPL) | Minimal Git integration

Notepad++ is a Windows-only text editor known for its speed, syntax highlighting for dozens of languages and the ability to handle very large files without slowing down. It has no built-in Git integration.

Third-party plugins exist:

- **nppGitSCM** (GitHub Plugin Manager) - adds a Git status indicator in the titlebar and basic commit/push/pull via dialogs
- **NPPGit** - wraps TortoiseGit operations from within Notepad++

Neither plugin offers the staging, blame, history or conflict resolution capabilities of GitSavvy, GitLens or vim-fugitive. Notepad++ users who need Git integration typically use a standalone Git GUI client (GitHub Desktop, Sourcetree, GitKraken) alongside the editor.

**Who should use Notepad++**: Windows developers who need to edit large files (log files, CSV dumps, XML) where other editors slow down or crash; users who need to edit files with unusual encodings; developers who want a fast Windows notepad replacement for quick edits alongside their main IDE.

---

## 9. Choosing from the long tail

With so many editors covered across this entire section, here is a quick decision guide for the less common tools:

**Use Magit (Emacs) if**: you already use Emacs or are willing to invest in learning it, you work extensively with complex Git history (rebasing, archaeology, multi-branch work) and want the most keyboard-efficient Git interface available.

**Use Visual Studio if**: you build .NET, C++, Unity or Windows desktop applications and need the deepest language tooling Microsoft offers, particularly Azure DevOps integration.

**Use Xcode if**: you build iOS, macOS, watchOS or tvOS apps. There is no choice - Xcode is the only way to build and sign Apple platform apps.

**Use Android Studio if**: you build Android apps. Like Xcode for Apple, Android Studio is the standard for Android development.

**Use Eclipse if**: you maintain legacy Java/OSGi projects already on Eclipse, work in an AUTOSAR embedded toolchain or are in an organisation that has standardised on Eclipse and cannot change.

**Use Nova if**: you are a macOS developer who wants a native Mac editor with a polished feel and good web development support and you do not need cross-platform compatibility.

**Use classic Vim if**: you are on servers or systems where Neovim is not available or you prefer Vim's stability and simplicity.

**Use nano if**: you need to write a commit message on a server where nothing else is installed.

**Use Notepad++**: for opening large files on Windows, not as a primary development editor.

---

## 10. Try It Yourself

**Exercise 1 - Magit status buffer (Emacs users)**

Install Magit in Emacs. Open a Git repository and press `C-x g` to open the status buffer. Press `Tab` to expand a section. Move to an unstaged change and press `s` to stage it. Press `c c` to open the commit view. Write a commit message and press `C-c C-c` to commit. Press `l l` to open the log and verify the commit appears.

**Exercise 2 - Xcode commit and blame**

Open an Xcode project under Git. Press `Alt+Cmd+C` to open the commit dialog. Review which files are included. Untick a file to exclude it. Write a commit message and commit. Open any Swift file and right-click a line to access **Show Blame for Line**. Read the blame popover for that line.

**Exercise 3 - EGit staging view**

Open an Eclipse project under Git. Go to `Window > Show View > Other > Git > Git Staging`. The Git Staging view opens. Drag a file from Unstaged to Staged. Write a commit message and click Commit. Switch to the History view (`Window > Show View > Other > Git > History`) and verify the commit appears.

**Exercise 4 - vim-fugitive in classic Vim**

If you use classic Vim, install vim-fugitive using native packages or vim-plug. Open a Git repository. Run `:Git` (or `:G`) to open the status buffer. Press `s` to stage a file. Press `cc` to open the commit message buffer. Write a message, save with `:wq`. Run `:Git log` to verify the commit.

**Exercise 5 - Nova blame popover**

If you use Nova on macOS, open a file in a repository with commit history. Right-click a line and choose **Show Last Change for Line**. Read the blame popover. Click the commit hash in the popover to jump to that commit in the Source Control history.

---

## 11. Common Mistakes

Underestimating Magit because Emacs has a reputation for difficulty. Magit is accessible without mastering all of Emacs - you can install Emacs, install Magit, bind `C-x g` to `magit-status` and use it for Git without knowing anything else about Emacs. Many developers run Emacs only to use Magit and use a different editor for everything else.

Confusing Visual Studio with VS Code. Visual Studio is a heavyweight Windows-only IDE for .NET, C++ and enterprise development. VS Code is a lightweight cross-platform editor. They share the name "Visual Studio" but are entirely different products with different user bases, different extension ecosystems and different Git integrations.

Using Xcode for Git operations that require the terminal. Xcode's Git UI does not have interactive rebase, multi-stash management or advanced merge conflict resolution. For any Git operation not available in the Integrate menu, open the terminal (Xcode has a built-in terminal via `View > Show Debug Area`) and use Git directly.

Staying on Eclipse for Java development out of inertia when IntelliJ IDEA Community is now free for commercial use. Since the IntelliJ IDEA 2025.3 unification, the Community edition includes Java, Kotlin, SQL, JavaScript and TypeScript for free, including commercial use. The Git integration in IntelliJ is significantly better than EGit. If your team is still on Eclipse purely because IntelliJ required a paid licence, that barrier no longer exists.

Treating nano as a permanent Git editor setup. nano is a viable fallback for writing commit messages on servers. It should not be your primary Git editor on a development machine. Configure a real editor on your development machine and reserve nano for server-only use.

---

## 12. Summary

Emacs with Magit is the gold standard for keyboard-driven Git management, offering region-aware staging, transient menus for all Git flags and Forge for GitHub/GitLab integration - all from within Emacs. Visual Studio is the Windows IDE for .NET, C++, Unity and enterprise development, with a Git Changes window, interactive rebase UI, Azure DevOps integration and deep Copilot integration in the 2026 release. Xcode is macOS-only and required for Apple platform development; its Source Control Navigator covers the standard workflow with blame, diff and PR creation and Xcode 26 adds agentic coding via MCP. Android Studio is an IntelliJ IDEA fork with the full JetBrains Git implementation plus Gemini for Android-specific AI assistance. Eclipse with EGit provides a complete Git workflow for the Java/OSGi developer community still on Eclipse, with staging, history, interactive rebase and a three-pane merge tool. Nova is a native macOS editor with built-in Git staging, diff and blame. Classic Vim with vim-fugitive and vim-gitgutter covers the same core workflow as the Neovim stack, with slight feature gaps due to missing Neovim-specific plugins. nano and Notepad++ are minimal tools with no meaningful Git integration beyond `core.editor` support.

---

## 13. Sources

- [Magit documentation](https://magit.vc/manual/)
- [Magit repository](https://github.com/magit/magit)
- [Forge - Magit GitHub/GitLab integration](https://github.com/magit/forge)
- [Visual Studio Git documentation](https://learn.microsoft.com/en-us/visualstudio/version-control/git-with-visual-studio)
- [Visual Studio 2026 Insiders](https://visualstudio.microsoft.com/insiders/)
- [Xcode source control documentation](https://developer.apple.com/documentation/xcode/configuring-your-xcode-project-to-use-source-control)
- [Android Studio documentation](https://developer.android.com/studio)
- [EGit documentation](https://wiki.eclipse.org/EGit/User_Guide)
- [Nova editor](https://nova.app)
- [vim-fugitive repository](https://github.com/tpope/vim-fugitive)
- [vim-gitgutter repository](https://github.com/airblade/vim-gitgutter)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
