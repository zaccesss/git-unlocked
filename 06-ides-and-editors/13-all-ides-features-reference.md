# All IDEs and Editors: Features Reference

**Difficulty:** 🟢 Beginner | **Time:** 15 minutes

This is the quick-reference file for the entire `ides-and-editors/` section. Use it when you need to compare tools at a glance, find which editors support a specific Git feature or identify the right tool for a specific use case. Every editor and IDE covered in this section appears here.

---

## Table of Contents

1. [Platform support at a glance](#1-platform-support-at-a-glance)
2. [Git feature comparison](#2-git-feature-comparison)
3. [AI Git features comparison](#3-ai-git-features-comparison)
4. [Generation and philosophy](#4-generation-and-philosophy)
5. [Pricing at a glance](#5-pricing-at-a-glance)
6. [Extension ecosystems](#6-extension-ecosystems)
7. [Integrated terminal and Git editor support](#7-integrated-terminal-and-git-editor-support)
8. [Quick decision guide](#8-quick-decision-guide)
9. [Key keyboard shortcuts across editors](#9-key-keyboard-shortcuts-across-editors)
10. [Sources](#10-sources)

---

## 1. Platform support at a glance

| Editor | 🪟 Windows | 🍎 macOS | 🐧 Linux | Notes |
|---|---|---|---|---|
| VS Code | Yes | Yes | Yes | |
| JetBrains IDEs | Yes | Yes | Yes | IntelliJ, PyCharm, WebStorm, Rider, GoLand, CLion |
| Neovim | Yes | Yes | Yes | |
| GitHub Desktop | Yes | Yes | Community | [shiftkey/desktop](https://github.com/shiftkey/desktop) fork for Linux |
| Cursor | Yes | Yes | Yes | |
| Windsurf | Yes | Yes | Yes | |
| Google Antigravity | Yes | Yes | Yes | Free public preview |
| AWS Kiro | Yes | Yes | Yes | |
| Zed | Yes | Yes | Yes | Windows support added 2026 |
| Helix | Yes | Yes | Yes | |
| Sublime Text | Yes | Yes | Yes | |
| Emacs + Magit | Yes | Yes | Yes | |
| Visual Studio | Yes | No | No | macOS version discontinued Aug 2024 |
| Xcode | No | Yes | No | macOS only, required for Apple dev |
| Android Studio | Yes | Yes | Yes | |
| Eclipse + EGit | Yes | Yes | Yes | |
| Nova | No | Yes | No | macOS only |
| Vim classic | Yes | Yes | Yes | Via WSL on Windows |
| nano | Via WSL | Yes | Yes | |
| Notepad++ | Yes | No | No | Windows only |

---

## 2. Git feature comparison

Key: ✅ Built-in or first-party | 🔌 Via extension/plugin | ❌ Not available | 🖥️ Terminal only

| Feature | VS Code | JetBrains | Neovim | GitHub Desktop | Cursor | Windsurf | Antigravity | Kiro |
|---|---|---|---|---|---|---|---|---|
| Staging panel | ✅ | ✅ | 🔌 fugitive | ✅ | ✅ | ✅ | ✅ | ✅ |
| Hunk staging | ✅ | ✅ | 🔌 gitsigns | ✅ | ✅ | ✅ | ✅ | ✅ |
| Line staging | ✅ | ✅ | 🔌 gitsigns | ✅ | ✅ | ✅ | ✅ | ✅ |
| Diff editor | ✅ | ✅ | 🔌 diffview | ✅ | ✅ | ✅ | ✅ | ✅ |
| 3-way merge editor | ✅ | ✅ | 🔌 diffview | Via editor | ✅ | ✅ | ✅ | ✅ |
| Inline blame | ✅ | ✅ | 🔌 gitsigns | ❌ | ✅ | ✅ | ✅ | ✅ |
| Gutter indicators | ✅ | ✅ | 🔌 gitsigns | ❌ | ✅ | ✅ | ✅ | ✅ |
| Commit graph / log | ✅ | ✅ | 🔌 neogit | ✅ | ✅ | ✅ | ✅ | ✅ |
| Interactive rebase UI | ❌ | ✅ | 🔌 fugitive | ❌ | ❌ | ❌ | ❌ | ❌ |
| Branch management | ✅ | ✅ | 🔌 neogit | ✅ | ✅ | ✅ | ✅ | ✅ |
| Stash management | 🖥️ | ✅ | 🔌 fugitive | ✅ (1 slot) | 🖥️ | 🖥️ | 🖥️ | 🖥️ |
| Cherry-pick UI | ✅ | ✅ | 🔌 fugitive | ✅ (drag) | 🖥️ | 🖥️ | 🖥️ | 🖥️ |
| Conflict resolution | ✅ | ✅ | 🔌 diffview | ✅ | ✅ | ✅ | ✅ | ✅ |
| PR creation | 🔌 | 🔌 | ❌ | ✅ | 🔌 | 🔌 | 🔌 | Via GitHub App |
| PR review | 🔌 | 🔌 | ❌ | ✅ (checkout) | 🔌 | 🔌 | ❌ | ❌ |
| File history | ✅ Timeline | ✅ | 🔌 diffview | ✅ | ✅ | ✅ | ✅ | ✅ |
| Git hooks support | 🖥️ | 🖥️ | 🖥️ | ✅ (v3.5.5) | 🖥️ | 🖥️ | 🖥️ | ✅ Native |

| Feature | Zed | Helix | Sublime Text | Emacs | Visual Studio | Xcode | Android Studio | Eclipse | Nova | Vim |
|---|---|---|---|---|---|---|---|---|---|---|
| Staging panel | ✅ | ❌ | 🔌 GitSavvy | ✅ Magit | ✅ | ✅ | ✅ | ✅ EGit | ✅ | 🔌 fugitive |
| Hunk staging | ✅ | ❌ | 🔌 GitSavvy | ✅ Magit | ✅ | ✅ | ✅ | ✅ | ✅ | 🔌 fugitive |
| Line staging | ✅ | ❌ | 🔌 GitSavvy | ✅ Magit | ✅ | ❌ | ✅ | ❌ | ❌ | 🔌 fugitive |
| Diff editor | ✅ | ❌ | 🔌 GitSavvy | ✅ Magit | ✅ | ✅ | ✅ | ✅ | ✅ | 🔌 fugitive |
| 3-way merge editor | ✅ | ❌ | Via editor | ✅ Magit | ✅ | ✅ (basic) | ✅ | ✅ | ❌ | 🖥️ |
| Inline blame | ✅ | ❌ | 🔌 GitSavvy | ✅ Magit | ✅ | ✅ | ✅ | ✅ | ✅ | 🔌 fugitive |
| Gutter indicators | ✅ | ✅ | ❌ | 🔌 diff-hl | ✅ | ✅ | ✅ | ✅ | ❌ | 🔌 gitgutter |
| Commit graph / log | ❌ | ❌ | 🔌 GitSavvy | ✅ Magit | ✅ | ✅ | ✅ | ✅ | ✅ | 🔌 fugitive |
| Interactive rebase UI | ❌ | ❌ | 🔌 GitSavvy | ✅ Magit | ✅ | ❌ | ✅ | ✅ | ❌ | 🔌 fugitive |
| Stash management | 🖥️ | 🖥️ | 🔌 GitSavvy | ✅ Magit | ✅ | ✅ | ✅ | ❌ | ❌ | 🔌 fugitive |
| Cherry-pick UI | 🖥️ | 🖥️ | 🔌 GitSavvy | ✅ Magit | ✅ | ✅ | ✅ | ✅ | ❌ | 🔌 fugitive |
| PR creation | 🔌 | ❌ | 🔌 GitSavvy | 🔌 Forge | ✅ (preview) | ✅ | 🔌 | ❌ | 🔌 | ❌ |
| PR review | ❌ | ❌ | ❌ | 🔌 Forge | ✅ (preview) | ❌ | ❌ | ❌ | ❌ | ❌ |
| File history | ❌ | ❌ | 🔌 GitSavvy | ✅ Magit | ✅ | ✅ | ✅ | ✅ | ✅ | 🔌 fugitive |

---

## 3. AI Git features comparison

| Feature | VS Code | JetBrains | GitHub Desktop | Cursor | Windsurf | Antigravity | Kiro | Zed |
|---|---|---|---|---|---|---|---|---|
| AI commit messages | ✅ Copilot | ✅ AI Assistant | ✅ Copilot | ✅ Built-in | ✅ Cascade | ✅ Gemini | ✅ Claude | ✅ Configurable |
| AI merge resolution | ✅ Copilot | ✅ AI Assistant | ❌ | ✅ Agent | ✅ Cascade | ✅ Agent | ✅ Agent | ✅ Configurable |
| Autonomous branch creation | 🔌 Agent mode | ❌ | ❌ | ✅ Agent | ✅ Cascade | ✅ Agent | ✅ Agent | ❌ |
| Autonomous commits | 🔌 Agent mode | ❌ | ❌ | ✅ Agent | ✅ Cascade | ✅ Agent | ✅ Agent | ❌ |
| Autonomous PR creation | 🔌 Agent mode | ❌ | ❌ | ✅ Cloud Agent | ✅ Devin | ✅ Agent | ✅ GitHub App | ❌ |
| AI blame attribution | ❌ | ❌ | ❌ | ✅ Cursor Blame | ❌ | ❌ | ❌ | ❌ |
| Spec-driven development | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ❌ |
| Git event hooks (IDE-native) | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ❌ |
| Parallel agent Git worktrees | ❌ | ✅ (2026.1) | ❌ | ✅ (Cursor 2.5+) | ✅ (Wave 13) | ✅ (Manager view) | ❌ | ❌ |
| AI PR review response | ❌ | ❌ | ❌ | ✅ Bugbot | ❌ | ❌ | ✅ /kiro fix | ❌ |

---

## 4. Generation and philosophy

| Editor | Generation | Primary philosophy | Suited to |
|---|---|---|---|
| VS Code | 1 + 2 | Extensible, universal | Everyone; most popular editor worldwide |
| JetBrains IDEs | 1 + 2 | Deep language semantics | Language-specific professional development |
| Neovim | 1 | Modal efficiency, composability | Terminal-native developers who customise everything |
| GitHub Desktop | 1 | Simplicity, visual clarity | Beginners and developers who want pure GUI |
| Cursor | 3 | AI-first, autonomous agents | Developers who want AI in full control of the workflow |
| Windsurf | 3 | Ambient AI context, flow state | Developers who want AI to observe and assist continuously |
| Google Antigravity | 3 | Agent orchestration, verifiable artifacts | Parallel multi-agent experimental workflows |
| AWS Kiro | 3 | Spec-driven, documented AI | AWS teams who want structured, auditable AI development |
| Zed | 1 + 2 | Performance, multiplayer collaboration | Speed-focused developers and pair programmers |
| Helix | 1 | Minimal, modal, no plugins | Developers who want a curated, stable terminal editor |
| Sublime Text | 1 | Speed, Command Palette, precision | Web developers and writers who value editor responsiveness |
| Emacs + Magit | 1 | Maximum programmability | Power users willing to invest in learning |
| Visual Studio | 1 + 2 | .NET/C++ language depth, Azure DevOps | Enterprise Windows/.NET/C++ developers |
| Xcode | 1 + 2 | Apple platform integration | iOS/macOS/watchOS/tvOS developers |
| Android Studio | 1 + 2 | Android platform integration | Android developers |
| Eclipse + EGit | 1 | Plugin architecture, OSGi | Legacy Java, AUTOSAR, enterprise Eclipse users |
| Nova | 1 | Native macOS, beautiful UI | Mac-first web developers |
| Vim classic | 1 | Stability, universality | Server-side editing, Vim veterans |
| nano | 1 | Minimal, universally available | Quick server-side edits, beginners |
| Notepad++ | 1 | Fast, large file handling | Windows users editing large files |

---

## 5. Pricing at a glance

| Editor | Free tier | Paid tier | Notes |
|---|---|---|---|
| VS Code | Yes (full editor) | GitHub Copilot from $10/mo | Editor is always free; AI features require Copilot |
| JetBrains IDEs | Community editions free | From $299/year (individual) | IntelliJ Community now free for commercial Java/Kotlin |
| Neovim | Yes (full) | - | Free, open source |
| GitHub Desktop | Yes (full) | Copilot from $10/mo for commit messages | Editor is free; Copilot subscription for AI messages |
| Cursor | Yes (50 premium requests/mo) | Pro $20/mo | Credit-based pricing since June 2025 |
| Windsurf | Yes (25 credits/day) | Pro $15/mo | Cheaper than Cursor Pro |
| Google Antigravity | Yes (public preview) | AI Pro $20/mo expected | Free during preview; paid tiers coming |
| AWS Kiro | Yes (50 credits/mo) | Pro $20/mo | No AWS account required |
| Zed | Yes (BYOK AI) | $5/mo credit | Pay-per-use AI at API cost + 10% |
| Helix | Yes (full) | - | Free, open source |
| Sublime Text | Evaluation (nag screen) | $99 one-time (3 yr updates) | Perpetual licence after update period |
| Emacs + Magit | Yes (both free) | - | Free, open source |
| Visual Studio | Community (individuals/OSS) | Professional ~$45/mo | Heavy Windows IDE |
| Xcode | Yes (free on macOS) | - | Free via Mac App Store |
| Android Studio | Yes (free) | Gemini within limits | Free; Gemini has usage limits |
| Eclipse + EGit | Yes (both free) | - | Free, open source |
| Nova | No free tier | $99 first year, $49/yr renewal | Perpetual after last purchased version |
| Vim classic | Yes (full) | - | Free, charityware |
| nano | Yes (full) | - | Free, open source |
| Notepad++ | Yes (full) | - | Free, open source |

---

## 6. Extension ecosystems

| Editor | Extension registry | Approximate count | Key Git extensions |
|---|---|---|---|
| VS Code | VS Code Marketplace | 50,000+ | GitLens, GitHub Pull Requests, GitLab Workflow |
| JetBrains | JetBrains Marketplace | 10,000+ | GitHub (bundled), GitLab (bundled), AI Assistant |
| Neovim | GitHub / lazy.nvim | Thousands | vim-fugitive, gitsigns.nvim, neogit, diffview.nvim |
| Cursor | VS Code Marketplace | 50,000+ | All VS Code extensions work |
| Windsurf | VS Code Marketplace | 50,000+ | All VS Code extensions work |
| Google Antigravity | VS Code Marketplace | 50,000+ | All VS Code extensions work |
| AWS Kiro | OpenVSX | 3,000+ | GitLens, GitLab Workflow (GitHub PRs may vary) |
| Zed | zed.dev/extensions | ~700 | Limited; no GitLens equivalent yet |
| Helix | None (no plugin system) | 0 | Integration via `:sh` and external tools |
| Sublime Text | Package Control | 5,000+ | GitSavvy, Terminus |
| Emacs | MELPA / GNU ELPA | Thousands | Magit, Forge, diff-hl |
| Visual Studio | VS Marketplace | Thousands | GitHub (bundled), Azure DevOps (bundled) |
| Xcode | Limited | Small | GitHub extension (limited) |
| Android Studio | JetBrains Marketplace | 10,000+ | Same as IntelliJ |
| Eclipse | Eclipse Marketplace | Thousands | EGit (bundled) |
| Nova | Nova Extension Library | Hundreds | GitHub extension |
| Vim classic | GitHub / vim-plug | Thousands | vim-fugitive, vim-gitgutter |

---

## 7. Integrated terminal and Git editor support

| Editor | Has integrated terminal | `GIT_EDITOR` wired to IDE | `git rebase -i` opens in IDE |
|---|---|---|---|
| VS Code | Yes (`` Ctrl+` ``) | Yes (`code --wait`) | Yes |
| JetBrains | Yes (`Alt+F12`) | Yes (IDE editor) | Yes |
| Neovim | Yes (`:terminal`) | Manual (`export GIT_EDITOR=nvim`) | Yes (if configured) |
| GitHub Desktop | No | No | Use system default |
| Cursor | Yes (`` Ctrl+` ``) | Yes (`cursor --wait`) | Yes |
| Windsurf | Yes + Cascade dedicated terminal | Yes (`windsurf --wait`) | Yes |
| Google Antigravity | Yes (`` Ctrl+` ``) | Yes | Yes |
| AWS Kiro | Yes (`` Ctrl+` ``) | Yes | Yes |
| Zed | Yes (`` Ctrl+` ``) | Yes (`zed --wait`) | Yes |
| Helix | Via `:sh` only | Manual (`export GIT_EDITOR=hx`) | Yes (if configured) |
| Sublime Text | Via Terminus extension | Manual (`git config core.editor "subl -w"`) | Yes (with Terminus) |
| Emacs | Yes (M-x term / vterm) | Yes (Magit handles it) | Yes |
| Visual Studio | Yes | Yes | Yes |
| Xcode | Debug area terminal | Manual | Yes (if configured) |
| Android Studio | Yes (`Alt+F12`) | Yes | Yes |
| Eclipse | Yes (Terminal view) | Manual | Limited |
| Nova | Yes (built-in) | Manual (`nova -w`) | Yes (if configured) |
| Vim classic | Yes (`:terminal`) | Manual (`export GIT_EDITOR=vim`) | Yes (if configured) |

---

## 8. Quick decision guide

**I am a complete beginner learning Git for the first time**
- Use **GitHub Desktop** for the simplest possible visual interface
- Or **VS Code** if you want a full editor that grows with you

**I write Python, Java, Kotlin, C# or Go professionally**
- **JetBrains** (PyCharm, IntelliJ, Rider, GoLand) for the deepest language tooling and the best built-in Git integration

**I want the most keyboard-efficient Git workflow in a terminal**
- **Neovim** with vim-fugitive, gitsigns, neogit and diffview
- Or **Emacs with Magit** for the gold-standard porcelain if you are willing to learn Emacs

**I want AI to help me but I stay in control**
- **VS Code with GitHub Copilot** - mature, stable, AI assists on request
- Or **JetBrains with AI Assistant** for the same level of control with better built-in Git

**I want AI to handle much of my Git workflow autonomously**
- **Cursor** - most production-ready, largest community, Cloud Agents for PRs
- **Windsurf** - better free tier, Cascade Flow awareness, $15/month Pro

**I am building for AWS and want structured AI-assisted development**
- **AWS Kiro** - spec-driven development, Git event hooks, GitHub issue integration

**I want a fast native editor that is not Electron**
- **Zed** - GPU-accelerated, now on all three platforms, native Git since March 2025

**I build iOS, macOS, watchOS or tvOS apps**
- **Xcode** - no choice, it is required for Apple platform development

**I build Android apps**
- **Android Studio** - the standard for Android development

**I want an experiment with frontier agent orchestration (and accept instability)**
- **Google Antigravity** - most ambitious architecture, free, but public preview with stability issues

**I work on Windows with .NET, C++ or Unity**
- **Visual Studio** - deepest language tooling, Azure DevOps integration, Copilot built in

**I want a polished macOS-native editor**
- **Nova** - native Mac feel, good web development support, $99

**I value speed and a curated terminal experience with no plugin complexity**
- **Helix** - built-in Tree-sitter and LSP, no plugins by design, lazygit for Git

**I need to edit very large files on Windows**
- **Notepad++** - handles gigabyte files without slowing down

---

## 9. Key keyboard shortcuts across editors

The most common Git operations and how to trigger them in each editor:

### Open source control / Git panel

| Editor | Shortcut |
|---|---|
| VS Code / Cursor / Windsurf / Antigravity / Kiro | `Ctrl+Shift+G` / `Cmd+Shift+G` |
| JetBrains / Android Studio | `Alt+9` / `Cmd+9` |
| Neovim (fugitive) | `:Git` or `<leader>gs` (custom) |
| GitHub Desktop | `Ctrl+1` / `Cmd+1` (Changes tab) |
| Zed | `Ctrl+Shift+G` / `Cmd+Shift+G` |
| Sublime Text + GitSavvy | `Ctrl+Shift+S` (custom, recommended) |
| Emacs + Magit | `C-x g` |
| Visual Studio | `Ctrl+0, Ctrl+G` |
| Xcode | `Cmd+2` (Source Control Navigator) |

### Commit

| Editor | Shortcut |
|---|---|
| VS Code / Cursor / Windsurf / Antigravity / Kiro / Zed | `Ctrl+Enter` / `Cmd+Enter` (in commit box) |
| JetBrains / Android Studio | `Ctrl+K` / `Cmd+K` (open commit window), then `Ctrl+Enter` |
| Neovim (fugitive) | `cc` in status buffer |
| GitHub Desktop | `Ctrl+Enter` / `Cmd+Enter` |
| Sublime Text + GitSavvy | `Ctrl+Shift+C` (custom, recommended) |
| Emacs + Magit | `c c` |
| Visual Studio | Commit button in Git Changes window |
| Xcode | `Alt+Cmd+C` |

### Push

| Editor | Shortcut |
|---|---|
| VS Code / Cursor / Windsurf / Antigravity / Kiro | Status bar sync button or Command Palette `Git: Push` |
| JetBrains / Android Studio | `Ctrl+Shift+K` / `Cmd+Shift+K` |
| Neovim (fugitive) | `:Git push` or `<leader>gp` (custom) |
| GitHub Desktop | `Ctrl+P` / `Cmd+P` |
| Zed | Command Palette: `git: push` |
| Helix | `:sh git push` |
| Sublime Text + GitSavvy | `P` in status view or `Ctrl+Shift+P` (custom) |
| Emacs + Magit | `P p` |
| Visual Studio | Push button in Git Changes window |

### Open integrated terminal

| Editor | Shortcut |
|---|---|
| VS Code / Cursor / Windsurf / Antigravity / Kiro / Zed | `` Ctrl+` `` / `` Cmd+` `` |
| JetBrains / Android Studio | `Alt+F12` / `Opt+F12` |
| Neovim | `:terminal` |
| Sublime Text | `` Ctrl+` `` (via Terminus extension) |
| Visual Studio | View > Terminal |
| Xcode | View > Debug Area > Activate Console |

---

## 10. Sources

- [VS Code Source Control documentation](https://code.visualstudio.com/docs/sourcecontrol/overview)
- [JetBrains Git documentation](https://www.jetbrains.com/help/idea/using-git-integration.html)
- [vim-fugitive repository](https://github.com/tpope/vim-fugitive)
- [gitsigns.nvim repository](https://github.com/lewis6991/gitsigns.nvim)
- [GitHub Desktop documentation](https://docs.github.com/en/desktop)
- [Cursor documentation](https://docs.cursor.com)
- [Windsurf documentation](https://docs.windsurf.com)
- [Google Antigravity - Wikipedia](https://en.wikipedia.org/wiki/Google_Antigravity)
- [AWS Kiro documentation](https://kiro.dev/docs)
- [Zed documentation](https://zed.dev/docs)
- [Helix documentation](https://docs.helix-editor.com)
- [GitSavvy repository](https://github.com/timbrel/GitSavvy)
- [Magit documentation](https://magit.vc/manual/)
- [Forge repository](https://github.com/magit/forge)
- [Visual Studio Git documentation](https://learn.microsoft.com/en-us/visualstudio/version-control/git-with-visual-studio)
- [Xcode source control documentation](https://developer.apple.com/documentation/xcode/configuring-your-xcode-project-to-use-source-control)
- [Android Studio documentation](https://developer.android.com/studio)
- [EGit documentation](https://wiki.eclipse.org/EGit/User_Guide)
- [Nova editor](https://nova.app)
- [vim-gitgutter repository](https://github.com/airblade/vim-gitgutter)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
