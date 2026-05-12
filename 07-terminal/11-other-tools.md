# Other Terminal Tools

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

This file covers the remaining tools in the modern terminal Git ecosystem: **gitui** as a Rust-written lazygit alternative for large repositories, **starship** for a Git-aware prompt that works in every shell, **zoxide** for instant navigation between repositories, and **diff-so-fancy** as a simpler delta alternative. Each tool installs in under two minutes and provides immediate value.

---

## Table of Contents

1. [gitui - a Rust TUI for Git](#1-gitui---a-rust-tui-for-git)
2. [Starship prompt](#2-starship-prompt)
3. [zoxide - smarter directory navigation](#3-zoxide---smarter-directory-navigation)
4. [diff-so-fancy](#4-diff-so-fancy)
5. [Shell init ordering](#5-shell-init-ordering)
6. [Try It Yourself](#6-try-it-yourself)
7. [Common Mistakes](#7-common-mistakes)
8. [Summary](#8-summary)
9. [Sources](#9-sources)

---

## 1. gitui - a Rust TUI for Git

gitui is an alternative terminal UI for Git, written in Rust by Stephan Dilly. Where lazygit is written in Go and uses the system `git` binary, gitui uses **libgit2** (a C library with Rust bindings) and **asyncgit** (gitui's own async Git layer). This gives gitui a significant performance advantage on repositories with large histories or many files.

Current version: **v0.28.1** (March 2026).

### gitui vs lazygit

| Dimension                    | lazygit                            | gitui                                        |
| ---------------------------- | ---------------------------------- | -------------------------------------------- |
| Language                     | Go                                 | Rust                                         |
| Git backend                  | system `git` binary                | libgit2 (async)                              |
| Performance on large repos   | Good                               | Better - async pipeline                      |
| Interactive rebase           | Full UI: drag, squash, fixup, drop | Basic: reword, fixup, squash                 |
| GitHub PR integration        | Yes (v0.61+)                       | No                                           |
| Cherry-pick                  | Multi-select, paste                | Basic                                        |
| Bisect support               | Yes                                | No                                           |
| Worktree support             | Yes                                | No                                           |
| Syntax highlighting in diffs | Via delta pager                    | Built-in via syntect                         |
| Config format                | YAML                               | RON (Rusty Object Notation)                  |
| Platform support             | All (delta caveat on Windows)      | All (built-in highlighting works everywhere) |

**Choose gitui when**: your repository has hundreds of thousands of commits (the Linux kernel, large monorepos) and lazygit feels noticeably slow. The async architecture keeps the UI responsive while Git operations complete in the background.

**Choose lazygit when**: you want interactive rebase, GitHub PR integration, cherry-pick, bisect, worktrees, or the richest overall feature set.

### Installing gitui

🍎 **macOS**:

```bash
brew install gitui
```

🐧 **Linux**:

```bash
sudo pacman -S gitui   # Arch
sudo dnf install gitui # Fedora
sudo apt install gitui # Ubuntu 24.04+

# Any Linux - GitHub release
cargo install gitui --locked
# or download from github.com/gitui-org/gitui/releases
```

🪟 **Windows**:

```powershell
winget install StephanDilly.gitui
# or
scoop install gitui
```

### Using gitui

Open gitui in any Git repository:

```bash
gitui
```

The interface has **five tabs** accessed by number keys `1`-`5`:

| Tab          | Content                              |
| ------------ | ------------------------------------ |
| `1` Status   | Files with staged/unstaged breakdown |
| `2` Log      | Commit history                       |
| `3` Files    | File browser at current state        |
| `4` Stashing | Stash list                           |
| `5` Stashes  | (same as 4 - navigate between)       |

**Key operations**:

| Key        | Action                                      |
| ---------- | ------------------------------------------- |
| `space`    | Stage / unstage file or hunk                |
| `D`        | Discard changes in selected file            |
| `c`        | Commit (opens message popup)                |
| `a`        | Amend last commit                           |
| `s`        | Stash changes                               |
| `Shift+S`  | Pop most recent stash                       |
| `p`        | Push                                        |
| `f`        | Fetch                                       |
| `Shift+P`  | Pull (note: opposite of lazygit convention) |
| `b`        | Open branch popup                           |
| `l`        | Open log view                               |
| `?` / `F1` | Help                                        |
| `q`        | Quit                                        |

**In the log view (Tab 2)**:

| Key | Action                                  |
| --- | --------------------------------------- |
| `E` | Reword commit message                   |
| `F` | Fixup into parent commit                |
| `S` | Squash into parent commit               |
| `t` | Create tag at commit                    |
| `r` | Revert commit                           |
| `y` | Copy commit hash                        |
| `B` | Open blame view for file in this commit |

### gitui configuration

Config lives at `~/.config/gitui/` on all platforms (gitui follows XDG correctly on macOS, unlike lazygit).

**`~/.config/gitui/theme.ron`** - colour theme:

```ron
(
    selected_text: (
        fg: Some("Reset"),
        bg: Some("DarkGray"),
        underline: false,
        italic: false,
    ),
    command_fg: Some("White"),
    selection_bg: Some("Blue"),
    selection_fg: Some("White"),
    cmdbar_bg: Some("Blue"),
    cmdbar_extra_lines_bg: Some("Blue"),
)
```

**`~/.config/gitui/key_bindings.ron`** - for vim-style keys (the project ships a `vim_style_key_config.ron` you can copy):

```ron
(
    move_left:  Some(( code: Char('h'), modifiers: "" )),
    move_right: Some(( code: Char('l'), modifiers: "" )),
    move_up:    Some(( code: Char('k'), modifiers: "" )),
    move_down:  Some(( code: Char('j'), modifiers: "" )),
    open_help:  Some(( code: F(1),     modifiers: "" )),
)
```

> [!NOTE]
> gitui uses **RON (Rusty Object Notation)** for config files, not YAML or JSON. The syntax is strict - trailing commas are required after the last field in a struct, and string colours must be quoted (`Some("White")` not `Some(White)`). Version 0.28 changed the theme colour syntax; if you are upgrading from an older config, update all colour values to the quoted string format.

> [!WARNING]
> gitui uses libgit2 rather than the system `git` binary for network operations. SSH authentication sometimes needs explicit setup: ensure your key is added to `ssh-agent` with `ssh-add ~/.ssh/id_ed25519` before using push/fetch. Some SSH config features that the `git` binary handles via OpenSSH may not translate directly through libgit2.

---

## 2. Starship prompt

Starship is a cross-shell prompt written in Rust. It shows your current branch, ahead/behind counts, staged/unstaged file indicators, stash count and conflict markers - automatically updating as you work. It also shows language versions, cloud contexts, tool versions and much more, but only when they are relevant to the current directory.

Current version: **v1.25.0** (April 2026).

### Installing starship

🍎 **macOS**:

```bash
brew install starship
```

🐧🍎 **Any platform** (recommended for getting the latest version):

```bash
curl -sS https://starship.rs/install.sh | sh
```

🪟 **Windows**:

```powershell
winget install --id Starship.Starship
# or
scoop install starship
```

### Shell integration

Add to the end of your shell config:

```bash
# bash - add to end of ~/.bashrc
eval "$(starship init bash)"

# zsh - add to end of ~/.zshrc
eval "$(starship init zsh)"

# fish - add to end of ~/.config/fish/config.fish
starship init fish | source

# PowerShell - add to $PROFILE
Invoke-Expression (&starship init powershell)
```

Reload your config and the prompt changes immediately.

### Nerd Font for icons

Starship uses special characters from Nerd Fonts for branch icons and file-type indicators. Without a Nerd Font, you see boxes or question marks instead of icons.

```bash
# macOS - install a Nerd Font via Homebrew
brew install --cask font-jetbrains-mono-nerd-font

# Windows
winget install -e --id DEVCOM.JetBrainsMonoNerdFont

# Linux - download and install manually from nerdfonts.com
```

Then set the font in your terminal emulator: Windows Terminal → Profile → Appearance → Font face → `JetBrainsMono Nerd Font`.

### Starship configuration

Config lives at `~/.config/starship.toml` on all platforms. Override location with `$STARSHIP_CONFIG`.

**Quickstart with a preset** - these configure everything for you:

```bash
starship preset nerd-font-symbols -o ~/.config/starship.toml
# Other presets: no-nerd-font, gruvbox-rainbow, tokyo-night, pastel-powerline
```

**Manual configuration** for Git-specific display:

```toml
# ~/.config/starship.toml

# Show the branch icon and name
[git_branch]
symbol = " "
truncation_length = 25
truncation_symbol = "…"

# Show ahead/behind counts and file status
[git_status]
conflicted = "="
ahead = "⇡${count}"
behind = "⇣${count}"
diverged = "⇕⇡${ahead_count}⇣${behind_count}"
untracked = "?${count}"
stashed = "$"
modified = "!${count}"
staged = "+${count}"
deleted = "✘${count}"
renamed = "»${count}"
style = "bold red"

# Show the current state (REBASING, MERGING etc.)
[git_state]
format = '\([$state( $progress_current/$progress_total)]($style)\) '
style = "bold yellow"

# Show add/delete counts (disabled by default - enable if you want it)
[git_metrics]
disabled = false
added_style = "bold blue"
deleted_style = "bold red"

# Reduce prompt lag on large repos
[git_status]
disabled = false

# Tune the timeout for slow repos
[git_branch]
# Set to false if git_status makes your prompt lag on huge repos
disabled = false
```

**For large repositories** where git status makes the prompt slow, disable the `git_status` module:

```toml
[git_status]
disabled = true
```

Or set a timeout:

```bash
STARSHIP_CACHE=~/.starship/cache
```

---

## 3. zoxide - smarter directory navigation

zoxide is a smarter `cd` command. It tracks which directories you visit and how frequently, building a "frecency" score (frequency + recency). Instead of typing the full path to a repository you use daily, you type `z project` and zoxide jumps to the highest-scored match.

Current version: **v0.9.9** (January 2025).

### Installing zoxide

🍎 **macOS**:

```bash
brew install zoxide
```

🐧 **Linux**:

```bash
sudo apt install zoxide     # Ubuntu 22.04+ / Debian 12+
sudo pacman -S zoxide       # Arch
sudo dnf install zoxide     # Fedora
cargo install zoxide --locked  # any platform with Rust
```

🪟 **Windows**:

```powershell
winget install ajeetdsouza.zoxide
# or
scoop install zoxide
```

### Shell integration

Add **after your shell framework and compinit** in your config:

```bash
# bash - add to end of ~/.bashrc
eval "$(zoxide init bash)"

# zsh - add to end of ~/.zshrc (after compinit)
eval "$(zoxide init zsh)"

# fish
zoxide init fish | source

# PowerShell
Invoke-Expression (& { (zoxide init powershell | Out-String) })
```

### Using zoxide

After visiting some directories, zoxide starts tracking them:

```bash
cd ~/projects/myapp          # regular cd - zoxide records this
cd ~/work/client-project     # another visit recorded
cd ~/projects/another-app    # another

# Now use z to jump
z myapp          # jumps to ~/projects/myapp (highest frecency match)
z client         # jumps to ~/work/client-project
z another        # jumps to ~/projects/another-app

# Multiple terms - all must appear in the path
z projects app   # matches ~/projects/myapp or ~/projects/another-app

# Previous directory
z -              # like cd -

# Interactive selection with fzf (requires fzf installed)
zi myapp         # shows all matches in fzf, select one
```

**For Git workflows**, zoxide's biggest value is instant navigation between repositories:

```bash
# Without zoxide - you have to remember and type the full path
cd ~/projects/client-alpha/backend/api-service

# With zoxide - type a few characters of anything in the path
z api-service

# Jump between repos instantly throughout the day
z myapp && git status
z client-backend && git log --oneline -5
z dotfiles && git pull
```

### Managing the zoxide database

```bash
zoxide query -l           # list all tracked directories with scores
zoxide query myapp        # show the best match without cd-ing
zoxide add /path          # manually add a path
zoxide remove /path       # manually remove a path
zoxide edit               # open the database in $EDITOR
```

**Replace cd entirely** if you prefer:

```bash
# In your shell config - makes `cd` use zoxide
eval "$(zoxide init --cmd cd zsh)"
```

---

## 4. diff-so-fancy

diff-so-fancy is a simpler alternative to delta for improving `git diff` output. Written in Perl, it is older (2015) and less feature-rich than delta, but has no dependencies beyond Perl (which is pre-installed on macOS and most Linux systems). If delta feels like too much configuration, diff-so-fancy is a two-command install.

What it adds: staged change highlighting, commit/author headers, cleaned-up diff headers, coloured `+`/`-` markers. What it does not have compared to delta: syntax highlighting, side-by-side view, line numbers, word-level diff, navigation.

🍎 **macOS**:

```bash
brew install diff-so-fancy
```

🐧 **Linux**:

```bash
sudo npm install -g diff-so-fancy    # if you have npm
# or download the single Perl script:
curl -Lo /usr/local/bin/diff-so-fancy \
    https://raw.githubusercontent.com/so-fancy/diff-so-fancy/master/third_party/build_fatpack/diff-so-fancy
chmod +x /usr/local/bin/diff-so-fancy
```

Configure in `~/.gitconfig`:

```ini
[core]
    pager = diff-so-fancy | less --tabs=4 -RFX

[interactive]
    diffFilter = diff-so-fancy --patch

[color "diff-highlight"]
    oldNormal = red bold
    oldHighlight = red bold 52
    newNormal = green bold
    newHighlight = green bold 22

[color "diff"]
    meta = 11
    frag = magenta bold
    func = 130 bold
    commit = yellow bold
    old = red bold
    new = green bold
    whitespace = red reverse
```

> [!NOTE]
> If you have already configured delta, do not also configure diff-so-fancy. They both set `core.pager` and only one will take effect (whichever is last in the config). Choose one. Delta is recommended for new setups; diff-so-fancy is an option for machines where you cannot or do not want to install Rust binaries.

---

## 5. Shell init ordering

When you have multiple tools adding `eval` hooks to your shell config, the order matters. Here is the recommended order for a `~/.zshrc` with all the tools from this section:

```zsh
# ~/.zshrc - correct ordering

# 1. Shell framework first (if using Oh My Zsh)
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME=""   # disable theme if using starship
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
source $ZSH/oh-my-zsh.sh

# 2. Completion system (must be before zoxide)
autoload -U compinit && compinit

# 3. Tools that hook into the shell
eval "$(fzf --zsh)"           # fzf history/file/dir key bindings
eval "$(zoxide init zsh)"     # zoxide (after compinit)
eval "$(starship init zsh)"   # starship prompt (at the end)

# 4. Tool configuration (environment variables, not init scripts)
export BAT_THEME="TwoDark"
export MANPAGER="sh -c 'col -bx | bat -l man -p'"
export MANROFFOPT="-c"

# 5. Plugin sources (forgit etc.)
source ~/.forgit/forgit.plugin.zsh   # if using forgit manually

# 6. User config (aliases, PATH additions)
# ... your other config ...

# Note: starship must be the LAST eval - it defines the prompt
# and anything after it could replace the prompt function
```

For bash, the same ordering applies in `~/.bashrc` with `eval "$(fzf --bash)"` and `eval "$(starship init bash)"`.

---

## 6. Try It Yourself

**Exercise 1 - explore gitui on a large repository**

If you have access to a repository with a long history (thousands of commits), open it in both lazygit and gitui. Navigate to the log view in each. Compare the responsiveness when scrolling through history. On most repositories you will not notice a difference; on very large ones the async architecture of gitui may feel noticeably smoother.

**Exercise 2 - set up starship**

Install starship and add the init line to your shell config. Reload and observe the new prompt. Navigate to a Git repository - your branch name should appear. Make a change to a file without committing - you should see a `!1` or similar indicator. Stage it and see the indicator change to `+1`. Commit it and see both indicators disappear.

**Exercise 3 - build zoxide muscle memory**

Install zoxide and add it to your shell config. Over the next hour, navigate to five or six different repositories using regular `cd`. Then try navigating using `z` with just a few characters from each path. Notice how quickly zoxide suggests the right directory after just one or two visits.

**Exercise 4 - compare delta and diff-so-fancy**

If you do not yet have delta configured, try diff-so-fancy first (easier setup, just Perl). Make a change to a file and run `git diff`. Compare the output to plain `git diff` without a pager. Then try delta if you want syntax highlighting and side-by-side view.

**Exercise 5 - configure starship for a large repo**

If you have a very large repository where `git status` in the prompt causes a delay, open `~/.config/starship.toml` and add:

```toml
[git_status]
disabled = true
```

Notice the prompt becomes instant. Then re-enable it and set `scan_timeout` lower:

```toml
[git_status]
disabled = false
scan_timeout = 10  # milliseconds
```

---

## 7. Common Mistakes

Installing gitui and expecting it to use delta for diff highlighting. gitui uses libgit2 internally and renders diffs with its own built-in syntect highlighting. The delta pager config in `.gitconfig` does not apply inside gitui (it does apply when you run `git diff` directly in the terminal). This is one reason gitui works well on Windows - no custom pager dependency.

Running zoxide without rebuilding the muscle memory. The value of zoxide compounds over time as it learns your directories. If you install it and use regular `cd` for everything, zoxide never builds its database and `z` finds nothing. Use `cd` for the first visit to new directories, then switch to `z` for subsequent visits.

Adding starship init before compinit in zsh. Starship modifies the prompt via the `precmd` hook. If compinit runs after starship, it can reset the prompt. Always: `compinit` first, `starship init` last.

Setting `ZSH_THEME` in Oh My Zsh config when also using starship. Oh My Zsh themes replace the prompt function - if `ZSH_THEME` is set to anything other than empty string, it will override starship's prompt. Set `ZSH_THEME=""` when using starship with Oh My Zsh.

Configuring both delta and diff-so-fancy in `.gitconfig`. They both set `core.pager`. Whichever entry appears last in the file wins. Remove one entirely to avoid confusion.

---

## 8. Summary

**gitui** is a Rust terminal UI for Git using libgit2's async architecture, giving it a performance edge on very large repositories. Choose it over lazygit when the codebase is large enough that lazygit's status updates feel slow. Config uses RON format; built-in syntax highlighting works on all platforms including Windows without a custom pager.

**Starship** is a Rust cross-shell prompt (bash, zsh, fish, PowerShell) that shows Git branch, ahead/behind counts, staged/unstaged indicators, conflict state and more. Install once with `curl -sS https://starship.rs/install.sh | sh`, add `eval "$(starship init zsh)"` to your shell config, and configure with `~/.config/starship.toml`. Requires a Nerd Font for icons.

**zoxide** is a smarter `cd` that tracks directory visits by frecency. `z reponame` jumps to the highest-scored matching directory without typing the full path. Invaluable for developers who switch between many repositories throughout the day. Add `eval "$(zoxide init zsh)"` to your shell config after compinit.

**diff-so-fancy** is a simpler delta alternative written in Perl with no compilation required. Set `core.pager = diff-so-fancy | less --tabs=4 -RFX` and `interactive.diffFilter = diff-so-fancy --patch` in `.gitconfig`. Less powerful than delta but requires no Rust.

Shell init order matters: framework → compinit → fzf → zoxide → forgit → starship (starship must be last).

---

## 9. Sources

- [gitui repository](https://github.com/gitui-org/gitui)
- [gitui configuration docs](https://github.com/gitui-org/gitui/blob/main/KEY_CONFIG.md)
- [Starship documentation](https://starship.rs/config/)
- [Starship Git modules reference](https://starship.rs/config/#git-branch)
- [zoxide repository](https://github.com/ajeetdsouza/zoxide)
- [diff-so-fancy repository](https://github.com/so-fancy/diff-so-fancy)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
