# Helix

**Difficulty:** 🔴 Advanced | **Time:** 25 minutes

Helix is a terminal-based modal text editor written in Rust, inspired by Kakoune and Neovim. It ships built-in Tree-sitter syntax highlighting, an LSP client and multiple selections - all without plugins. Its design philosophy is deliberately minimal: no plugin system, no package manager, no extension marketplace. Everything the editor does is built into the binary. For Git, this minimalism means there is no built-in staging panel, commit UI or log view. What Helix provides is gutter diff indicators and a branch name in the statusline. Everything else - staging, committing, branching, viewing history - is handled by external tools launched from a shell. The most common approach is to bind a key to open `lazygit` in a tmux popup or a WezTerm pane. This file explains how to set that up and why Helix users choose this approach over a fully integrated Git UI.

---

## Table of Contents

1. [History and background](#1-history-and-background)
2. [Installing Helix](#2-installing-helix)
3. [Configuration](#3-configuration)
4. [Built-in Git features](#4-built-in-git-features)
5. [Why Helix has no Git UI](#5-why-helix-has-no-git-ui)
6. [Installing lazygit](#6-installing-lazygit)
7. [Integrating lazygit with Helix via tmux](#7-integrating-lazygit-with-helix-via-tmux)
8. [Integrating lazygit with Helix via WezTerm](#8-integrating-lazygit-with-helix-via-wezterm)
9. [Using :sh for one-off Git commands](#9-using-sh-for-one-off-git-commands)
10. [Setting Helix as your Git editor](#10-setting-helix-as-your-git-editor)
11. [Alternative Git tools for Helix users](#11-alternative-git-tools-for-helix-users)
12. [Keyboard shortcuts](#12-keyboard-shortcuts)
13. [Try It Yourself](#13-try-it-yourself)
14. [Common Mistakes](#14-common-mistakes)
15. [Summary](#15-summary)
16. [Sources](#16-sources)

---

## 1. History and background

Helix was created by Blaž Hrastnik starting in 2021. It takes Vim's modal editing model and Kakoune's selection-first design and combines them with modern built-in features: Tree-sitter for semantic syntax highlighting and text objects, a built-in LSP client with diagnostics in the statusline, multiple selections as a first-class editing primitive, and a which-key style menu for discoverable key bindings.

The editor is written entirely in Rust. It has no scripting language, no plugin API and no extension system. Every feature in Helix is part of the core binary, which means it is fast, portable and predictable. It also means that features not built into the core cannot be added without contributing to the repository or running a fork.

As of April 2026, Helix stable is at **25.07.1** (the numbering follows year-month format). A plugin system based on Steel/Scheme (a Scheme-based scripting language for Helix) has been in development for over two years and remains an open PR; it has not merged into upstream. Users who need plugins use the mattwparas fork, which includes the Steel integration.

Helix runs on Windows, macOS and Linux. It is free and open source under the Mozilla Public Licence 2.0.

---

## 2. Installing Helix

🍎 **macOS**

```bash
brew install helix
```

🐧 **Linux**

```bash
# Fedora
sudo dnf install helix

# Ubuntu/Debian (may be older version)
sudo apt install helix

# Arch Linux (always current)
sudo pacman -S helix

# AppImage (universal, current version)
curl -Lo helix.AppImage \
  https://github.com/helix-editor/helix/releases/latest/download/helix-*.AppImage
chmod +x helix.AppImage
./helix.AppImage

# Build from source (Rust required)
git clone https://github.com/helix-editor/helix
cd helix
cargo install --path helix-term --locked
```

🪟 **Windows**

```powershell
winget install Helix.Helix
```

Or download the installer from [github.com/helix-editor/helix/releases](https://github.com/helix-editor/helix/releases).

Verify the installation with `hx --version`. Run `hx --health` to check Tree-sitter and LSP status.

---

## 3. Configuration

Helix's configuration file is `config.toml`. Its location:

| Platform | Path |
|---|---|
| 🐧 Linux | `~/.config/helix/config.toml` |
| 🍎 macOS | `~/.config/helix/config.toml` |
| 🪟 Windows | `%AppData%\helix\config.toml` |

A minimal `config.toml`:

```toml
[editor]
line-number = "relative"
mouse = false
rulers = [72, 80]

[editor.statusline]
left = ["mode", "spinner", "file-name", "diagnostics"]
center = []
right = ["version-control", "position", "file-encoding", "file-type"]

[editor.cursor-shape]
insert = "bar"
normal = "block"
select = "underline"

[keys.normal]
# Git integration keybindings go here
```

Per-project configuration goes in `.helix/config.toml` at the repository root. This is committed to Git and allows project-specific settings (for example, enabling specific formatters or LSP servers for this project).

---

## 4. Built-in Git features

Helix's built-in Git integration is intentionally minimal:

**Gutter diff indicators**: coloured symbols in the signcolumn next to line numbers showing changes since the last commit:

- `+` green: line added
- `~` yellow: line modified
- `-` red: line deleted at this position

These update in real time as you edit. They are driven by `gitoxide` (Helix's Rust Git library) rather than shelling out to `git diff`.

**Branch name in statusline**: add `"version-control"` to your statusline configuration (shown in the example above) to display the current branch name. This reads from `.git/HEAD` directly.

**Hunk text object**: Helix has a built-in hunk text object for navigating between diff hunks:

- `]g` - jump to the next changed hunk
- `[g` - jump to the previous changed hunk

These work inside a file to navigate between your uncommitted changes.

That is the entirety of Helix's built-in Git integration. There is no staging panel, no commit dialog, no log view, no blame view and no conflict resolution UI.

---

## 5. Why Helix has no Git UI

The absence of a Git UI is a deliberate design decision, not an oversight. Helix's philosophy is that the editor should be a fast, precise text manipulation tool and that adjacent concerns like Git management, file browsing and terminal multiplexing are better handled by dedicated tools built for exactly those purposes.

This is the same philosophical position as the Unix principle of doing one thing well. `lazygit` is better at Git management than any in-editor Git panel because its entire interface is designed around Git. `tmux` or `WezTerm` is better at multiplexing terminals than any IDE's built-in terminal manager because they are purpose-built for it. A file tree explorer like `yazi` or `broot` is better at file navigation than any IDE's file panel.

The Helix workflow is: edit in Helix, manage Git in lazygit (or any terminal Git UI), multiplex with tmux or WezTerm. Each tool is best-of-class for its job. The integration between them is via keybindings that switch focus or open tools in splits.

---

## 6. Installing lazygit

lazygit is a terminal UI for Git written in Go. It provides staging, committing, branching, stashing, interactive rebase, cherry-pick and more in a keyboard-driven TUI.

🍎 **macOS**

```bash
brew install lazygit
```

🐧 **Linux**

```bash
# Ubuntu/Debian
LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep -Po '"tag_name": "v\K[^"]*')
curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/latest/download/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz"
tar xf lazygit.tar.gz lazygit
sudo install lazygit /usr/local/bin

# Arch Linux
sudo pacman -S lazygit

# Fedora
sudo dnf install lazygit
```

🪟 **Windows**

```powershell
winget install JesseDuffield.lazygit
```

Verify: `lazygit --version`

Configure lazygit at `~/.config/lazygit/config.yml` (Linux/macOS) or `%AppData%\lazygit\config.yml` (Windows). Set Helix as the editor lazygit opens for commit messages:

```yaml
os:
  editCommand: hx
  editCommandTemplate: "{{editor}} {{filename}}"
  openCommand: hx
```

---

## 7. Integrating lazygit with Helix via tmux

The cleanest Helix + lazygit integration uses **tmux popups** - floating windows that overlay the terminal temporarily.

**Prerequisites**: tmux installed (`brew install tmux` / `sudo apt install tmux` / `winget install tmux`).

**Configuration in `~/.config/helix/config.toml`**:

```toml
[keys.normal]
# Open lazygit in a tmux popup with Ctrl+G
C-g = [":sh tmux popup -d '#{pane_current_path}' -xC -yC -w 90% -h 90% -E lazygit"]
```

This binds `Ctrl+G` in normal mode to open a floating tmux popup running lazygit, sized at 90% of the terminal. The `-d '#{pane_current_path}'` flag opens lazygit in the current file's directory, not the shell's working directory - important when editing files in subdirectories.

**How it works**: when you press `Ctrl+G`, tmux creates a floating window over Helix, lazygit launches inside it, you do your Git work (stage, commit, push, branch), then press `q` to close lazygit. The popup disappears and you are back in Helix, which automatically refreshes its gutter indicators to reflect the new Git state.

**If you are not running Helix inside tmux**, the popup approach does not work. In that case, use a shell command to open lazygit inline:

```toml
[keys.normal]
C-g = [":sh lazygit", ":redraw", ":reload-all"]
```

The `:redraw` command redraws the terminal after lazygit exits (lazygit can leave drawing artefacts in some terminals), and `:reload-all` refreshes all buffers from disk to reflect any file changes made during the Git session.

> [!NOTE]
> There is a known issue where key handling (particularly the Escape key) can be disrupted after using the inline `:sh lazygit` approach in some terminals. The tmux popup approach avoids this entirely. If you experience key binding issues after closing lazygit, restart Helix.

---

## 8. Integrating lazygit with Helix via WezTerm

WezTerm is a GPU-accelerated terminal emulator that supports splitting panes and running commands in specific panes via its CLI. This enables a more persistent lazygit integration than the tmux popup approach.

**Configuration in `~/.config/helix/config.toml`**:

```toml
[keys.normal]
C-g = [":sh hx-wezterm.sh lazygit"]
```

**The `hx-wezterm.sh` script** (place in `~/.local/bin/` and make executable):

```bash
#!/bin/bash
# hx-wezterm.sh - open a tool in a WezTerm bottom pane
TOOL="$1"
PANE_ID=$(wezterm cli list | awk 'NR>1 && $NF == "'$TOOL'" {print $3; exit}')

if [ -z "$PANE_ID" ]; then
  # No pane running this tool - create one
  PANE_ID=$(wezterm cli split-pane --bottom --percent 40)
  wezterm cli send-text --pane-id "$PANE_ID" "$TOOL\n"
else
  # Pane already exists - activate it
  wezterm cli activate-pane --pane-id "$PANE_ID"
fi
```

This approach opens lazygit in a persistent bottom pane. Press `Ctrl+G` to focus the lazygit pane; pressing `Ctrl+G` again returns focus to Helix. The pane stays open between invocations so lazygit maintains its state (scroll position, selected branch etc.).

---

## 9. Using :sh for one-off Git commands

For quick Git operations that do not need a full TUI, Helix's `:sh` command runs a shell command inline. The output appears in a temporary buffer or the statusline.

```vim
" Check status
:sh git status

" View recent log
:sh git log --oneline -10

" Stage a specific file (current file's path)
:sh git add %

" Push
:sh git push

" Create and switch to a new branch
:sh git checkout -b feature/new-thing

" Stash
:sh git stash
```

The `%` variable in `:sh` expands to the current buffer's file path. This is useful for operations on the file you are editing: `:sh git blame %` prints blame output for the current file to a temporary buffer (not the inline UI that lazygit provides, but functional for quick checks).

---

## 10. Setting Helix as your Git editor

Set Helix as your default Git editor so that `git commit`, `git rebase -i` and `git merge --edit` open their files in Helix:

```bash
git config --global core.editor "hx"
```

Or set it per-project:

```bash
git config core.editor "hx"
```

With this set, running `git commit` in your terminal opens the commit message file in Helix. You write the message, save with `:w` and quit with `:q`. Git detects the save and creates the commit.

For `git rebase -i`, the rebase todo file opens in Helix. Edit it with normal Helix text editing: change `pick` to `squash` by overwriting the word, move lines with `Alt+Up` / `Alt+Down`, save and quit. Helix's `word_diff = true` equivalent is not available for the rebase todo file specifically, but Tree-sitter syntax highlighting applies to diff files.

> [!TIP]
> Add `%{buffer_name}` and `%{cursor_line}` to your statusline to see the filename and line number while editing a commit message. This is useful for staying within the 72-character subject line limit: set `rulers = [72]` in your `config.toml` to show a visual guide at column 72.

---

## 11. Alternative Git tools for Helix users

lazygit is the most popular choice but not the only option. Other terminal Git tools that integrate well with Helix:

**gitui** (Rust, by extrawurst): a lazygit alternative written in Rust. Faster than lazygit for large repositories. Less feature-complete but more reliable on unusual terminals. Install via `cargo install gitui` or package managers.

**tig**: a text-mode Git repository browser. Excellent for log browsing, blame and diff viewing. Less capable for staging and committing than lazygit. Install via `brew install tig` or `sudo apt install tig`.

**git-cola**: a Qt-based GUI Git client. Runs in a separate window rather than the terminal. Useful if you prefer a graphical staging interface alongside Helix in the terminal.

**The Git CLI itself**: many Helix users who come from a terminal background are comfortable with `git add -p`, `git commit` and `git log` directly. The `:sh` integration makes this natural in Helix.

---

## 12. Keyboard shortcuts

**Built-in Helix shortcuts relevant to Git**:

| Action | Key | Notes |
|---|---|---|
| Next changed hunk | `]g` | Navigate between diff hunks |
| Previous changed hunk | `[g` | Navigate between diff hunks |
| Run shell command | `:sh <command>` | One-off Git commands |
| Redraw terminal | `:redraw` | After lazygit exits inline |
| Reload all buffers | `:reload-all` | After external Git changes |

**Recommended custom keybindings** (add to `config.toml`):

```toml
[keys.normal]
# Open lazygit (tmux popup)
C-g = [":sh tmux popup -d '#{pane_current_path}' -xC -yC -w 90% -h 90% -E lazygit"]

# Quick status check
"space g s" = [":sh git status"]

# Quick log
"space g l" = [":sh git log --oneline -15"]

# Stage current file
"space g a" = [":sh git add %", ":echo 'Staged current file'"]

# Quick commit (prompts for message in shell)
"space g c" = [":sh git commit"]
```

---

## 13. Try It Yourself

**Exercise 1 - configure lazygit with tmux**

Install tmux and lazygit. Open a terminal with tmux running. Add the `C-g` keybinding from section 7 to your Helix config. Open a repository in Helix. Make a change to a file and press `Ctrl+G`. Verify that a lazygit popup opens. Stage the file in lazygit, write a commit message, commit, and press `q`. Verify that Helix's gutter indicators have updated.

**Exercise 2 - navigate hunks**

Open a file with several uncommitted changes in different parts of the file. Press `]g` to jump to the first changed hunk. Press `]g` again to jump to the next one. Press `[g` to go back. Observe how the gutter indicators highlight the hunk you are currently in.

**Exercise 3 - use :sh for a quick status check**

Without leaving Helix, run `:sh git status`. Read the output. Run `:sh git log --oneline -5`. Run `:sh git diff HEAD~1`. These give you basic situational awareness without opening lazygit.

**Exercise 4 - set Helix as Git editor**

Run `git config --global core.editor "hx"`. In a terminal (not inside Helix), run `git commit` on a repository with staged changes. Verify that Helix opens with the commit message template. Write a message. Save with `:w` and quit with `:q`. Verify the commit was created with `git log -1`.

**Exercise 5 - interactive rebase in Helix**

With `core.editor = hx` configured, run `git rebase -i HEAD~3` in the terminal. Helix opens the rebase todo file. Change one `pick` to `squash` by positioning your cursor and using `cw` (change word) to overwrite it. Save and quit. Watch the rebase proceed. Use `:sh git log --oneline -5` in Helix to verify the history.

---

## 14. Common Mistakes

Running Helix without tmux and then trying the tmux popup integration. The tmux popup requires an active tmux session. If you launch Helix directly from a terminal without tmux, the popup command fails. Either start tmux first (`tmux new`) and then launch Helix inside it, or use the inline `:sh lazygit` approach with `:redraw` and `:reload-all` afterwards.

Forgetting `:reload-all` after external Git operations. When lazygit stages files, creates commits or performs checkouts, the files on disk change but Helix's buffers reflect the old state. Run `:reload-all` (or the custom keybinding equivalent) to refresh all open buffers from disk. Without this, Helix shows stale content and gutter indicators.

Using `:sh git commit` without setting `GIT_EDITOR` or `core.editor` first. If neither is set, `git commit` opens nano or vi inside the `:sh` invocation, which may behave unexpectedly inside Helix. Set `core.editor = hx` globally before using `:sh git commit`.

Expecting a plugin system that does not exist yet. As of April 2026, the Steel/Scheme plugin system is not merged into upstream Helix. There are no plugins in the official Helix. If you see references to Helix plugins online, they are either for the mattwparas fork or from documentation that predates the plugin system's indefinite delay. The way to extend Helix's Git capabilities is via `:sh` commands and external tool integration, not plugins.

Trying to use Helix as a daily driver without learning a terminal multiplexer first. The Helix + lazygit workflow requires either tmux or WezTerm for smooth integration. Invest time in learning your multiplexer - it pays dividends beyond just the Git integration.

---

## 15. Summary

Helix is a terminal modal editor that ships built-in gutter diff indicators, a branch name in the statusline and hunk navigation (`]g` / `[g`), and nothing else for Git. The absence of a staging panel, commit UI, log view and blame view is a deliberate philosophical choice: Helix does text editing, and dedicated tools do Git management better than any in-editor panel can. The standard workflow is to bind a key to open lazygit in a tmux popup or WezTerm pane: press the key, do Git work in lazygit, close lazygit, return to Helix. For one-off commands, `:sh <command>` runs any shell command inline. Setting `core.editor = hx` routes `git commit` and `git rebase -i` through Helix's editor. Helix has no plugin system yet - the Steel/Scheme system remains unmerged as of April 2026. It is the right choice for developers who want a minimal, fast terminal editor and are comfortable managing Git in a dedicated TUI rather than inside the editor itself.

---

## 16. Sources

- [Helix documentation](https://docs.helix-editor.com)
- [Helix editor source repository](https://github.com/helix-editor/helix)
- [Helix, tmux and lazygit - DEV Community](https://dev.to/rajasegar/helix-tmux-and-lazygit-7nj)
- [Turning Helix into an IDE with WezTerm](https://quantonganh.com/2023/08/19/turn-helix-into-ide.md)
- [Helix plugin system discussion](https://github.com/helix-editor/helix/discussions/14457)
- [lazygit repository](https://github.com/jesseduffield/lazygit)
- [Helix future directions discussion](https://github.com/helix-editor/helix/discussions/14457)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
