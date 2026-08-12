# bat and tig

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

Two terminal tools that complement a Git workflow without replacing any part of it: **bat** is a syntax-highlighted replacement for `cat` that integrates with Git to show file changes inline and **tig** is a keyboard-driven Git log browser that has been around since 2006 and remains one of the fastest ways to navigate commit history, view blame and browse the state of a repository entirely from the terminal.

---

## Table of Contents

1. [bat - syntax-highlighted cat](#1-bat---syntax-highlighted-cat)
2. [Installing bat](#2-installing-bat)
3. [bat in daily use](#3-bat-in-daily-use)
4. [bat and Git integration](#4-bat-and-git-integration)
5. [bat configuration](#5-bat-configuration)
6. [tig - a terminal Git browser](#6-tig---a-terminal-git-browser)
7. [Installing tig](#7-installing-tig)
8. [tig views and navigation](#8-tig-views-and-navigation)
9. [tig for staging](#9-tig-for-staging)
10. [tig configuration](#10-tig-configuration)
11. [Try It Yourself](#11-try-it-yourself)
12. [Common Mistakes](#12-common-mistakes)
13. [Summary](#13-summary)
14. [Sources](#14-sources)

---

## 1. bat - syntax-highlighted cat

`cat` prints the contents of a file. bat does the same thing with syntax highlighting, line numbers, Git change indicators in the gutter and automatic paging when the output is longer than the terminal. For reading files in the terminal, bat is significantly easier to read than `cat`.

bat is written in Rust and uses the `syntect` library (the same one delta uses) for syntax highlighting, with the same Sublime Text `.sublime-syntax` format files. It supports over 200 languages out of the box.

**Comparison**:

```bash
# cat - plain text, no highlighting
cat src/auth.py

# bat - syntax highlighted, line numbers, git markers, paged
bat src/auth.py
```

bat shows:

- **Syntax highlighting** with the language detected from the file extension
- **Line numbers** in the gutter
- **Git change markers** (`+` for added lines, `~` for modified, `-` for deleted) in the gutter next to line numbers
- **File header** showing the filename
- **Automatic paging** via `less` when the file is longer than the terminal

---

## 2. Installing bat

The package is called `bat` on most platforms. On Ubuntu and Debian, it is `batcat` due to a historical name conflict.

🍎 **macOS**:

```bash
brew install bat
```

🐧 **Linux**:

```bash
# Arch Linux
sudo pacman -S bat

# Fedora
sudo dnf install bat

# Ubuntu/Debian - note the package name
sudo apt install bat

# Ubuntu/Debian: the binary is installed as batcat
# Create a symlink so you can use 'bat'
mkdir -p ~/.local/bin
ln -s /usr/bin/batcat ~/.local/bin/bat
# Make sure ~/.local/bin is on your PATH
```

🪟 **Windows**:

```powershell
winget install sharkdp.bat
# or
scoop install bat
# or
choco install bat
```

Verify:

```bash
bat --version
```

---

## 3. bat in daily use

bat is a drop-in replacement for `cat` in most situations. You can alias `cat` to `bat` if you want it everywhere, though some scripts rely on exact `cat` behaviour.

```bash
# View a file with syntax highlighting
bat README.md
bat src/main.py

# View multiple files
bat src/*.py

# View specific line range
bat -r 10:30 src/auth.py    # lines 10 to 30
bat -r 50: src/auth.py      # line 50 to end
bat -r :-20 src/auth.py     # last 20 lines

# Plain output (no headers, line numbers or decorations)
bat -p src/auth.py           # --plain
bat --style=plain src/auth.py

# Show non-printable characters (useful for debugging whitespace issues)
bat -A src/auth.py           # --show-all

# Force a specific language for highlighting
bat -l json config.txt      # highlight config.txt as JSON

# Use bat as a pager for man pages
export MANPAGER="sh -c 'col -bx | bat -l man -p'"
man git-diff
```

**Shell alias** for convenience:

```bash
# In ~/.zshrc or ~/.bashrc
alias cat='bat --paging=never'  # no paging - just print
# or: alias cat='bat' to keep paging
```

---

## 4. bat and Git integration

bat shows Git change indicators automatically when viewing files inside a Git repository. The gutter to the left of line numbers shows:

- `+` green: line added since last commit
- `~` yellow: line modified since last commit
- (empty space): line deleted at this position is not shown directly; you see the gap

This gives you a quick visual overview of what changed in a file without running `git diff`.

**bat as a Git pager**: use bat instead of `less` for `git diff`, `git log -p` and `git show`:

```bash
git diff | bat -l diff
git show HEAD | bat -l diff
git log -p | bat -l diff
```

Or set it as the default Git pager (though delta is generally a better choice for diffs specifically):

```bash
git config --global core.pager "bat --plain --paging=always"
```

**bat for man pages** (one of the most popular uses):

```bash
export MANPAGER="sh -c 'col -bx | bat -l man -p'"
export MANROFFOPT="-c"
```

This gives you syntax-highlighted man pages with bat's colour scheme. Add both lines to your `~/.zshrc` or `~/.bashrc`.

---

## 5. bat configuration

bat reads a config file at:

| Platform   | Path                   |
| ---------- | ---------------------- |
| 🐧 Linux   | `~/.config/bat/config` |
| 🍎 macOS   | `~/.config/bat/config` |
| 🪟 Windows | `%APPDATA%\bat\config` |

Find the path: `bat --config-file`
Generate a default config: `bat --generate-config-file`

```bash
# ~/.config/bat/config

# Set a default theme
--theme="TwoDark"

# Default style (show line numbers, changes, file header, grid)
--style="numbers,changes,header,grid"

# Use italic text where available
--italic-text=always

# Map file extensions to languages
--map-syntax "*.jenkinsfile:Groovy"
--map-syntax ".ignore:Git Ignore"
--map-syntax "*.env:DotENV"
--map-syntax "Makefile.*:Makefile"

# Default pager
--pager="less -RF"
```

**Themes**: `bat --list-themes` shows all available themes. Try them interactively:

```bash
# Preview a theme on a file
bat --theme="GitHub" src/main.py
bat --theme="Nord" src/main.py
bat --theme="Dracula" src/main.py
bat --theme="Monokai Extended" src/main.py
bat --theme="OneHalfDark" src/main.py
```

Set the default via config or the environment variable:

```bash
export BAT_THEME="TwoDark"
```

**Custom themes**: place `.tmTheme` files in `$(bat --config-dir)/themes/` and rebuild the cache:

```bash
bat cache --build
```

---

## 6. tig - a terminal Git browser

tig is an ncurses-based Git repository browser. It has been maintained since 2006, is written in C and has a stable, keyboard-driven interface focused on navigating history, viewing diffs, blame, references and stashes. It is available on every platform Git supports.

tig is not trying to be lazygit. It does not do staging or commit creation as smoothly. What it is excellent at is **reading history** - quickly navigating a commit graph, viewing the diff for any commit, blaming any file, browsing all refs and searching through commits. For code archaeology and understanding what happened in a repository, tig is fast and precise.

---

## 7. Installing tig

🍎 **macOS**:

```bash
brew install tig
```

🐧 **Linux**:

```bash
sudo apt install tig       # Ubuntu/Debian
sudo dnf install tig       # Fedora
sudo pacman -S tig         # Arch
sudo apk add tig           # Alpine
```

🪟 **Windows**:

tig is bundled with **Git for Windows 2.14.2+** - check with `tig --version` in Git Bash. It is also available via MSYS2:

```bash
# In MSYS2 terminal
pacman -S tig
```

Or run inside WSL2 after `sudo apt install tig`.

---

## 8. tig views and navigation

Launch tig from inside a repository:

```bash
tig                    # main view - commit history with graph
tig log                # same as tig
tig blame file.py      # blame a specific file
tig status             # status view (staging interface)
tig stash              # stash list
tig refs               # all refs (branches, tags, remotes)
tig show HEAD          # diff of the last commit
tig file.py            # history of a specific file
tig HEAD~5..HEAD       # commits in a range
git log -p | tig       # tig as a pager for log output
```

### Views

tig has several named views, each accessed by a key:

| Key | View                                    |
| --- | --------------------------------------- |
| `m` | Main view (commit log with graph)       |
| `d` | Diff view (diff of the selected commit) |
| `l` | Log view                                |
| `t` | Tree view (file browser at a commit)    |
| `f` | Blame view (who wrote each line)        |
| `r` | Refs view (all branches, tags, remotes) |
| `s` | Status view (staging interface)         |
| `y` | Stash view                              |
| `p` | Pager (view output piped into tig)      |
| `h` | Help view                               |

### Navigation

Most tig navigation uses vi-style keys:

| Key           | Action                                                  |
| ------------- | ------------------------------------------------------- |
| `j` / `k`     | Move down / up one line                                 |
| `J` / `K`     | Move to next / previous item (context-sensitive)        |
| `Space` / `-` | Page down / up                                          |
| `g` / `G`     | Jump to first / last entry                              |
| `Enter`       | Open the selected item (show diff, expand commit, etc.) |
| `Tab`         | Switch between split pane and full view                 |
| `q`           | Close current view                                      |
| `Q`           | Quit tig                                                |
| `R` / `F5`    | Refresh the current view                                |
| `/`           | Search forward (regular expression)                     |
| `?`           | Search backward                                         |
| `n` / `N`     | Next / previous search match                            |

### Main view actions

From the main view (commit log):

| Key     | Action                                                 |
| ------- | ------------------------------------------------------ |
| `Enter` | Open the diff for the selected commit                  |
| `C`     | Cherry-pick this commit to the current branch          |
| `r`     | Revert this commit                                     |
| `t`     | Create a tag at this commit                            |
| `!`     | Reset HEAD to this commit (asks type: soft/mixed/hard) |
| `b`     | Create a new branch at this commit                     |
| `y`     | Copy commit hash                                       |
| `E`     | Open in `$EDITOR`                                      |

---

## 9. tig for staging

tig's status view (`tig status` or press `s` from anywhere) provides a basic staging interface:

```
On branch main
Changes to be committed:
  modified src/auth.py

Changes not staged for commit:
  modified src/api.py
  modified tests/test_auth.py

Untracked files:
  docs/new-doc.md
```

Navigate the file list with `j`/`k`. In the status view:

| Key     | Action                                     |
| ------- | ------------------------------------------ |
| `u`     | Stage / unstage the selected file          |
| `!`     | Revert the selected file (discard changes) |
| `C`     | Commit (opens `$EDITOR` for the message)   |
| `Enter` | Open the diff for the selected file        |

For granular hunk-level staging, tig's stage view is less capable than lazygit or `git add -p`. Use tig for fast full-file staging and history browsing; use lazygit or `git add -p` for interactive hunk-level staging.

---

## 10. tig configuration

tig reads `~/.tigrc` or `$XDG_CONFIG_HOME/tig/config` (typically `~/.config/tig/config`).

A useful `~/.tigrc`:

```
# Better main view layout
set main-view = \
    line-number:no,interval=5 \
    id:yes \
    date:default \
    author:full,width=15 \
    commit-title:yes,graph,refs,overflow=no

# Split views vertically (useful on wide terminals)
set vertical-split = auto
set split-view-height = 70%

# Show full diffs in split pane
set diff-options = -m --first-parent

# Better blame
set blame-options = -C -C -C

# Auto-refresh
set refresh-mode = auto
set refresh-interval = 5

# Mouse support (optional)
set mouse = true

# Enable line numbers in diff view
set line-graphics = utf-8

# Tab width
set tab-size = 4

# Colour customisation
color cursor white blue bold
color title-focus black green bold
color title-blur  black green

# Custom keybindings
bind main C !git cherry-pick %(commit)
bind main <Ctrl-r> !?git reset --hard %(commit)
bind diff <Ctrl-d> !?git checkout %(commit) -- %(file)
```

---

## 11. Try It Yourself

**Exercise 1 - explore bat on a file**

Install bat. Navigate to any code file in a project (`src/*.py`, `*.js`, `*.go` etc.). Run `bat filename`. Notice the syntax highlighting, line numbers and any Git change indicators if the file has uncommitted changes. Run `bat -r 1:20 filename` to see just the first 20 lines.

**Exercise 2 - bat and git diff**

Make a change to a file in a repository but do not commit. Run `bat filename` - you should see change indicators (yellow `~` for modified lines) in the gutter next to the line numbers. Now run `git diff | bat -l diff` to see the diff itself syntax-highlighted by bat.

**Exercise 3 - bat for man pages**

Add to your shell config:

```bash
export MANPAGER="sh -c 'col -bx | bat -l man -p'"
export MANROFFOPT="-c"
```

Reload your config and run `man git-log`. The man page should now display with syntax highlighting. Navigate with the usual `less` keys (`Space`, `b`, `q`).

**Exercise 4 - explore tig's main view**

Install tig. Navigate to a repository and run `tig`. Read through the commit graph. Press `Enter` on any commit to see its diff in a split pane. Press `q` to close the diff. Press `f` to open the blame view for the file the commit modified. Press `q` to return.

**Exercise 5 - use tig for file history**

Find a file in a repository with several commits that modified it. Run `tig path/to/file.py`. tig shows only the commits that touched that file. Navigate through the list and press Enter on each to see exactly what changed in that commit. This is faster than `git log --follow -p path/to/file.py` for interactive browsing.

---

## 12. Common Mistakes

On Ubuntu/Debian, running `bat` after installing and getting "command not found". The binary is installed as `batcat` - create the symlink described in section 2 and ensure `~/.local/bin` is on your PATH.

Setting `core.pager = bat` in `.gitconfig` and then finding `git add -p` broken. bat as a Git pager works for display commands but breaks interactive ones like `git add --patch`. Either use delta (which handles this correctly with `--color-only`) or only set bat as the pager for specific commands rather than globally.

Expecting tig to replace lazygit for staging. tig's status view is functional for full-file staging but does not offer the same hunk-level interactivity as lazygit or `git add -p`. Use tig for log browsing and blame; use lazygit for staging and committing.

Running `tig blame file.py` from a directory outside the repository root. tig resolves paths relative to your current directory. If you are in a subdirectory, use the path from the repo root: `tig blame src/auth/middleware.py` from the repo root, not `tig blame middleware.py` from inside `src/auth/`.

Forgetting tig is bundled with Git for Windows. Windows users often install extra tools when tig is already there in Git Bash. Run `tig --version` in Git Bash before downloading anything.

---

## 13. Summary

bat is a syntax-highlighted `cat` replacement with Git change gutter indicators, line numbers and automatic paging. Install via your package manager (package is `bat`; on Ubuntu/Debian it installs as `batcat` - create a symlink). Use it for viewing files, as a pager for man pages (`export MANPAGER="sh -c 'col -bx | bat -l man -p'"`) or piping diff output for casual viewing. Configure via `~/.config/bat/config`. Themes are set with `--theme=` or `BAT_THEME`.

tig is an ncurses Git browser written in C, stable since 2006. Launch with `tig` for the main log view, `tig blame file` for blame, `tig status` for the staging interface, `tig stash` for stash browsing, `tig refs` for all references. Navigate with vi-style `j`/`k`, open items with Enter, quit with `q`. It is bundled with Git for Windows 2.14.2+. Configure via `~/.tigrc`. Both tools complement rather than replace lazygit - bat for reading files, tig for navigating history, lazygit for staging and commits.

---

## 14. Sources

- [bat repository](https://github.com/sharkdp/bat)
- [bat documentation](https://github.com/sharkdp/bat#readme)
- [tig repository](https://github.com/jonas/tig)
- [tig manual](https://jonas.github.io/tig/doc/tig.1.html)
- [tig user guide](https://jonas.github.io/tig/doc/manual.html)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
