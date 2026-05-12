# IDEs and Editors: Overview

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

Every developer needs somewhere to write code, and the editor you choose shapes how you interact with Git every single day. This section maps the full landscape of editors and IDEs available in 2026 - from lightweight terminal tools to fully autonomous AI coding environments - and shows you exactly how Git fits into each one. Whether you are picking your first editor, evaluating the wave of new AI-first tools, or simply trying to understand what your current editor can do with Git, this overview will orient you before you read the individual files.

---

## Table of Contents

1. [Why your editor choice matters for Git](#1-why-your-editor-choice-matters-for-git)
2. [Three generations of Git tooling](#2-three-generations-of-git-tooling)
3. [The VS Code fork family](#3-the-vs-code-fork-family)
4. [Files in this section](#4-files-in-this-section)
5. [How to choose](#5-how-to-choose)
6. [Git still runs underneath everything](#6-git-still-runs-underneath-everything)
7. [The integrated terminal as an escape hatch](#7-the-integrated-terminal-as-an-escape-hatch)
8. [Try It Yourself](#8-try-it-yourself)
9. [Common Mistakes](#9-common-mistakes)
10. [Summary](#10-summary)
11. [Sources](#11-sources)

---

## 1. Why your editor choice matters for Git

Git is a command-line tool at its core. Every operation - staging, committing, branching, merging, rebasing - can be performed in a terminal with nothing but the `git` binary and a keyboard. However, the editor you use every day can make that workflow dramatically easier, more visual, or even partially automated, depending on how deeply it integrates with Git.

A well-integrated editor gives you:

- Visual diff views so you can see exactly what changed before committing
- Gutter indicators so you know which lines are new, modified, or deleted at a glance
- Inline blame annotations so you can see who wrote every line and when
- One-click staging, committing and pushing without leaving your editor
- Merge conflict resolution UIs that make three-way merges readable
- Branch management without switching to a terminal
- Pull request and code review workflows without switching to a browser
- AI-generated commit messages, merge conflict suggestions and automated PR creation

The degree of integration varies enormously. Some editors treat Git as a first-class feature built into the core product. Others rely entirely on extensions and plugins. A growing third category treats Git operations as tasks to delegate to an AI agent that reads your codebase and acts autonomously.

---

## 2. Three generations of Git tooling

The editor landscape in 2026 has split into three distinct generations, each with a fundamentally different relationship to Git.

**Generation 1 - classic IDEs and editors** arrived before AI changed the industry. These tools built Git support either into the core application or through a mature extension ecosystem. VS Code, JetBrains IDEs, Neovim, GitHub Desktop, Sublime Text and Helix all belong here. Git integration is stable, well-documented and reliable. These tools surface Git information and operations through a polished UI and leave the decisions entirely to you. They are the right choice when you want control, predictability and a workflow that does not change overnight.

**Generation 2 - AI-assisted editors** added an AI layer on top of a classic editing experience. GitHub Copilot in VS Code is the most widely adopted example: the editor works exactly as before, but an AI model can suggest code completions, generate commit messages and assist with code review. GitLab Duo follows the same pattern. The AI assists - you still drive every Git operation manually.

**Generation 3 - agentic IDEs** treat AI not as an assistant but as an active participant. Cursor, Windsurf, Google Antigravity and AWS Kiro all belong here. These tools can plan multi-file changes, run terminal commands, read CI output and interact with Git repositories with significant autonomy. They can stage files, write commit messages, open pull requests, create branches and iterate on code without you directing each step. The Git workflow is not just surfaced - it is partially automated. This is powerful and genuinely useful, but it requires understanding what the agent is doing and reviewing its output carefully.

All three generations are in active use in 2026. Each has a legitimate place depending on your workflow, your team and your project.

---

## 3. The VS Code fork family

One fact that matters for understanding this section: almost every Generation 3 agentic IDE is a fork of VS Code or its open-source variant Code-OSS. This means they all inherit VS Code's Source Control panel, terminal integration, extension format and keybindings as their baseline Git UI.

| Editor | Fork base | Notes |
|---|---|---|
| Cursor | VS Code | Full VS Code extension compatibility |
| Windsurf | VS Code | Full VS Code extension compatibility |
| Google Antigravity | VS Code / Code-OSS | Built by ex-Windsurf team, launched Nov 2025 |
| AWS Kiro | Code-OSS | Uses OpenVSX registry, not VS Code Marketplace |

If you already know VS Code's Git integration, you already know the baseline for all four of these tools. What each one adds on top of that baseline is what makes them distinct.

---

## 4. Files in this section

| File | Tool | Generation | Platforms |
|---|---|---|---|
| 01-vs-code.md | Visual Studio Code | 1 + 2 | 🪟🍎🐧 |
| 02-jetbrains.md | JetBrains IDEs | 1 + 2 | 🪟🍎🐧 |
| 03-neovim.md | Neovim | 1 | 🪟🍎🐧 |
| 04-github-desktop.md | GitHub Desktop | 1 | 🪟🍎 |
| 05-cursor.md | Cursor | 3 | 🪟🍎🐧 |
| 06-windsurf.md | Windsurf | 3 | 🪟🍎🐧 |
| 07-antigravity.md | Google Antigravity | 3 | 🪟🍎🐧 |
| 08-kiro.md | AWS Kiro | 3 | 🪟🍎🐧 |
| 09-zed.md | Zed | 1 + 2 | 🪟🍎🐧 |
| 10-helix.md | Helix | 1 | 🪟🍎🐧 |
| 11-sublime-text.md | Sublime Text | 1 | 🪟🍎🐧 |
| 12-other-editors.md | Emacs, Visual Studio, Xcode and more | 1 + 2 | Varies |
| 13-all-ides-features-reference.md | Full reference table | - | - |

> [!NOTE]
> GitHub Desktop does not have an official Linux build. A community-maintained fork at [shiftkey/desktop](https://github.com/shiftkey/desktop) provides `.deb`, `.rpm` and AppImage packages as well as a Flathub Flatpak. Zed added Windows support in 2026 - all three platforms are now supported. Google Antigravity is in free public preview as of April 2026.

---

## 5. How to choose

The right editor depends on what you are optimising for.

**If you are a beginner** learning Git alongside learning to code, use VS Code. It has the most tutorials, the largest community, the most extensions and a Git UI that covers most workflows without any configuration. GitHub Desktop is also worth knowing - it is even simpler and pairs naturally with GitHub.

**If you write Java, Kotlin, Python, Scala or .NET** professionally, use a JetBrains IDE. The Git integration is the most complete built-in implementation available anywhere, with changelists, shelving, an interactive rebase UI and a Log view that rivals dedicated Git GUI clients.

**If you live in the terminal** and value keystroke efficiency above all else, use Neovim with vim-fugitive and gitsigns. If you want a gentler on-ramp to that philosophy, try Helix. If you want the gold standard of terminal Git porcelains and are willing to invest in learning Emacs, Magit is genuinely in a category of its own.

**If you want AI to assist but not take over**, VS Code with GitHub Copilot or GitLens Pro is the mature, stable choice. AI handles commit messages, conflict suggestions and code review comments while you retain full manual control.

**If you want AI to handle significant portions of your Git workflow autonomously**, Cursor and Windsurf are the most production-ready options as of April 2026. Both are VS Code forks so the learning curve from VS Code is minimal.

**If you are building on AWS** or need spec-driven development with Git event hooks, AWS Kiro is purpose-built for that workflow.

**If you value a fast, native editing experience** and want modern built-in Git without Electron overhead, Zed is worth evaluating - it now supports Windows, macOS and Linux.

---

## 6. Git still runs underneath everything

Every editor and IDE in this section uses the same `git` binary you would use in a terminal. None of them implement Git themselves (with the exception of some JetBrains operations that use JGit internally for specific tasks). When VS Code stages a file, it runs `git add`. When Cursor's agent creates a branch, it runs `git checkout -b`. When GitSavvy in Sublime Text shows a diff, it runs `git diff`.

This means two important things. First, everything you learn about Git in the terminal applies directly to understanding what your editor is doing. Second, you can always drop into the terminal to do something your editor cannot do. The editor is a UI on top of Git, not a replacement for it.

> [!TIP]
> If you ever want to see exactly what Git command your editor is running, check its output panel or logs. VS Code shows Git commands in the Output panel under `View > Output > Git`. JetBrains shows them in the Console tab of the Git tool window. Knowing what command is running behind the button helps you understand Git more deeply and debug problems faster.

---

## 7. The integrated terminal as an escape hatch

Every editor covered in this section ships an integrated terminal, and that terminal is your escape hatch for anything the GUI does not support. Some complex operations - interactive rebase with specific flags, worktree management, `git filter-repo`, submodule surgery - are either not available in the GUI or are cleaner to do at the command line.

| Editor | Open terminal | Notes |
|---|---|---|
| VS Code | `` Ctrl+` `` / `` Cmd+` `` | `GIT_EDITOR` routes back to VS Code |
| JetBrains | `Alt+F12` / `Opt+F12` | `GIT_EDITOR` routes back to IDE |
| Cursor | `` Ctrl+` `` / `` Cmd+` `` | Inherited from VS Code |
| Windsurf | `` Ctrl+` `` / `` Cmd+` `` | Cascade has a dedicated zsh terminal |
| Antigravity | `` Ctrl+` `` / `` Cmd+` `` | Inherited from VS Code |
| Kiro | `` Ctrl+` `` / `` Cmd+` `` | Inherited from Code-OSS |
| Zed | `` Ctrl+` `` / `` Cmd+` `` | Native terminal, fast |
| Sublime Text | `` Ctrl+` `` via Terminus extension | Third-party extension required |
| Helix | `:sh <command>` | Runs a single shell command inline |

VS Code and JetBrains both wire `GIT_EDITOR` and `GIT_ASKPASS` back into the editor, so `git commit` and `git rebase -i` in the integrated terminal open the relevant files inside the IDE rather than in a separate window. This means you get your editor's syntax highlighting, word wrap and keybindings when writing commit messages or editing rebase todo files.

---

## 8. Try It Yourself

> [!NOTE]
> These exercises work with any editor. They are designed to help you observe how your current editor interacts with Git.

**Exercise 1 - explore your editor's Git panel**

Open a Git repository in your editor. Find the source control or version control panel. Make a small change to a file and observe how the editor shows you that the file has been modified. Stage the change and look at how the staged state is displayed differently from the unstaged state. Commit using the editor's UI rather than the terminal.

**Exercise 2 - find your editor's Git log**

Without using the terminal, find the commit history for your repository inside your editor. Most editors have a log view or history panel. Can you see branch names? Can you see who authored each commit? Can you click on a commit to see its diff?

**Exercise 3 - watch the terminal and GUI together**

Open your editor's integrated terminal and your editor's Git panel side by side. Run `git status` in the terminal and compare what it shows with what the panel is showing. Make another change to a file and watch both update. This shows you that the panel and the terminal are looking at the same underlying Git state.

**Exercise 4 - check the Git output log (VS Code only)**

Go to `View > Output` and switch the dropdown to Git. Make a change to a file and stage it using the Source Control panel. Watch the output log to see the exact `git add` command VS Code ran. This shows you what the editor is doing behind the scenes.

---

## 9. Common Mistakes

Treating your editor's Git UI as a black box is one of the most common mistakes developers make. When something goes wrong - a commit lands on the wrong branch, a merge conflict does not resolve as expected, a push is rejected - developers who understand the underlying Git commands can diagnose the problem quickly. Developers who only know the buttons often cannot.

Assuming all editors have the same Git features leads to frustration when switching tools. JetBrains changelists do not exist in VS Code. VS Code's 3-way merge editor works differently from Xcode's. Cursor's agent can create branches and open pull requests autonomously in ways that plain VS Code cannot. Read the file for your specific editor rather than assuming cross-editor parity.

Ignoring the integrated terminal entirely is a mistake even if you prefer the GUI. Some operations are either not available in the GUI or significantly cleaner at the command line. Knowing how to move fluidly between the GUI and the terminal makes you a more capable developer.

Trusting AI agent Git operations without reviewing them is a specific mistake that has become relevant in 2026. When Cursor's agent commits code or Windsurf's Cascade pushes a branch, those operations are real and permanent. Review the diff and the commit message before accepting agent-driven Git operations, exactly as you would review a colleague's pull request.

---

## 10. Summary

The editor landscape in 2026 spans three generations: classic IDEs and editors with stable Git integrations, AI-assisted editors that add intelligence on top of traditional workflows, and agentic IDEs where AI can drive significant portions of the Git workflow autonomously. VS Code is the most widely used editor and the foundation most agentic IDEs are forked from. JetBrains provides the most complete built-in Git integration. Neovim and Helix serve keyboard-native workflows. GitHub Desktop is the simplest visual Git client. Cursor and Windsurf are the most production-ready agentic options as of April 2026. Zed is a fast, native alternative now available on all three platforms. Sublime Text with GitSavvy is a capable keyboard-driven setup. Emacs with Magit remains the gold standard terminal-based Git porcelain. Every tool in this section runs the same `git` binary underneath, and every one ships an integrated terminal for operations the GUI does not cover. Understanding that the GUI is a UI on top of Git, not a replacement for it, is the foundation for using any of these tools effectively.

---

## 11. Sources

- [VS Code Source Control documentation](https://code.visualstudio.com/docs/sourcecontrol/overview)
- [JetBrains Git integration documentation](https://www.jetbrains.com/help/idea/using-git-integration.html)
- [GitLens documentation](https://help.gitkraken.com/gitlens/gitlens-home/)
- [Neovim documentation](https://neovim.io/doc/)
- [vim-fugitive repository](https://github.com/tpope/vim-fugitive)
- [GitHub Desktop documentation](https://docs.github.com/en/desktop)
- [Cursor documentation](https://docs.cursor.com)
- [Windsurf documentation](https://docs.windsurf.com)
- [Google Antigravity - Wikipedia](https://en.wikipedia.org/wiki/Google_Antigravity)
- [AWS Kiro documentation](https://kiro.dev/docs)
- [Zed documentation](https://zed.dev/docs)
- [Helix documentation](https://docs.helix-editor.com)
- [GitSavvy repository](https://github.com/timbrel/GitSavvy)
- [Magit documentation](https://magit.vc/manual/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
