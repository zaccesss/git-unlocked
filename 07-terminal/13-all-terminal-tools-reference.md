# All Terminal Tools: Reference

**Difficulty:** 🟢 Beginner | **Time:** 10 minutes

Quick reference for every tool covered in the `terminal/` section. Use this file when you need to look up an install command, a key binding, a config file path or find the right tool for a specific task.

---

## Table of Contents

1. [Tools at a glance](#1-tools-at-a-glance)
2. [Install commands by platform](#2-install-commands-by-platform)
3. [Config file locations](#3-config-file-locations)
4. [Key bindings quick reference](#4-key-bindings-quick-reference)
5. [Shell config snippets](#5-shell-config-snippets)
6. [Choosing the right tool](#6-choosing-the-right-tool)
7. [Prompt symbols reference](#7-prompt-symbols-reference)
8. [Danger levels at a glance](#8-danger-levels-at-a-glance)
9. [Sources and further reading](#9-sources-and-further-reading)

---

## 1. Tools at a glance

| Tool | What it does | Written in | File |
| ---- | ------------ | ---------- | ---- |
| **delta** | Syntax-highlighted git diff/log/blame pager | Rust | [07-delta.md](07-delta.md) |
| **lazygit** | Full terminal UI for Git | Go | [08-lazygit.md](08-lazygit.md) |
| **fzf** | Fuzzy finder - pipes any list into interactive search | Go | [09-fzf-and-git.md](09-fzf-and-git.md) |
| **forgit** | fzf-powered Git command wrappers | Shell | [09-fzf-and-git.md](09-fzf-and-git.md) |
| **bat** | Syntax-highlighted cat with Git gutter markers | Rust | [10-bat-and-tig.md](10-bat-and-tig.md) |
| **tig** | ncurses Git log browser and blame viewer | C | [10-bat-and-tig.md](10-bat-and-tig.md) |
| **gitui** | Terminal UI for Git (fast on large repos) | Rust | [11-other-tools.md](11-other-tools.md) |
| **starship** | Cross-shell Git-aware prompt | Rust | [11-other-tools.md](11-other-tools.md) |
| **zoxide** | Smarter cd with frecency ranking | Rust | [11-other-tools.md](11-other-tools.md) |
| **diff-so-fancy** | Simpler delta alternative (Perl, no compilation) | Perl | [11-other-tools.md](11-other-tools.md) |

---

## 2. Install commands by platform

### delta

```bash
# macOS
brew install git-delta

# Linux - Arch
sudo pacman -S git-delta

# Linux - Fedora
sudo dnf install git-delta

# Linux - Ubuntu/Debian
sudo apt install git-delta

# Windows
winget install dandavison.delta
```

### lazygit

```bash
# macOS
brew install lazygit

# Linux - Arch
sudo pacman -S lazygit

# Linux - Fedora
sudo dnf install lazygit

# Linux - Ubuntu/Debian 24.04+
sudo apt install lazygit

# Linux - any (current release from GitHub)
LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep -Po '"tag_name": *"v\K[^"]*')
curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/download/v${LAZYGIT_VERSION}/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz"
tar xf lazygit.tar.gz lazygit && sudo install lazygit -D -t /usr/local/bin/

# Windows
winget install -e --id JesseDuffield.lazygit
```

### fzf

```bash
# macOS
brew install fzf && $(brew --prefix)/opt/fzf/install

# Linux - any (includes shell integration)
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && ~/.fzf/install

# Linux - distro packages (may be older)
sudo apt install fzf       # Ubuntu/Debian
sudo pacman -S fzf          # Arch
sudo dnf install fzf        # Fedora

# Windows
winget install junegunn.fzf
```

### bat

```bash
# macOS
brew install bat

# Linux - Arch
sudo pacman -S bat

# Linux - Fedora
sudo dnf install bat

# Linux - Ubuntu/Debian (installs as batcat)
sudo apt install bat
ln -s /usr/bin/batcat ~/.local/bin/bat   # create symlink

# Windows
winget install sharkdp.bat
```

### tig

```bash
# macOS
brew install tig

# Linux
sudo apt install tig       # Ubuntu/Debian
sudo pacman -S tig          # Arch
sudo dnf install tig        # Fedora

# Windows - bundled with Git for Windows 2.14.2+
# Check: tig --version (in Git Bash)
# MSYS2: pacman -S tig
```

### gitui

```bash
# macOS
brew install gitui

# Linux - Arch
sudo pacman -S gitui

# Linux - Ubuntu/Debian 24.04+
sudo apt install gitui

# Linux/macOS - via Cargo
cargo install gitui --locked

# Windows
winget install StephanDilly.gitui
```

### starship

```bash
# All platforms - recommended
curl -sS https://starship.rs/install.sh | sh

# macOS
brew install starship

# Windows
winget install --id Starship.Starship
```

### zoxide

```bash
# macOS
brew install zoxide

# Linux - Ubuntu 22.04+/Debian 12+
sudo apt install zoxide

# Linux - Arch
sudo pacman -S zoxide

# Linux - Fedora
sudo dnf install zoxide

# Windows
winget install ajeetdsouza.zoxide
```

### diff-so-fancy

```bash
# macOS
brew install diff-so-fancy

# Linux - via npm
sudo npm install -g diff-so-fancy

# Linux/macOS - single Perl script (no npm needed)
curl -Lo /usr/local/bin/diff-so-fancy \
    https://raw.githubusercontent.com/so-fancy/diff-so-fancy/master/third_party/build_fatpack/diff-so-fancy
chmod +x /usr/local/bin/diff-so-fancy
```

---

## 3. Config file locations

| Tool | 🐧 Linux | 🍎 macOS | 🪟 Windows |
| ---- | -------- | -------- | ---------- |
| delta | `~/.gitconfig` (`[delta]` section) | `~/.gitconfig` | `C:\Users\<n>\.gitconfig` |
| lazygit | `~/.config/lazygit/config.yml` | `~/Library/Application Support/lazygit/config.yml` | `%LOCALAPPDATA%\lazygit\config.yml` |
| fzf | `~/.bashrc` / `~/.zshrc` (env vars) | same | PowerShell `$PROFILE` |
| bat | `~/.config/bat/config` | `~/.config/bat/config` | `%APPDATA%\bat\config` |
| tig | `~/.config/tig/config` or `~/.tigrc` | same | `%USERPROFILE%\.tigrc` |
| gitui | `~/.config/gitui/` | `~/.config/gitui/` | `%APPDATA%\gitui\` |
| starship | `~/.config/starship.toml` | `~/.config/starship.toml` | `%USERPROFILE%\.config\starship.toml` |
| zoxide | `~/.local/share/zoxide/db.zo` (database) | same | `%LOCALAPPDATA%\zoxide\db.zo` |

---

## 4. Key bindings quick reference

### lazygit - universal

| Key | Action |
| --- | ------ |
| `1`-`5` | Jump to panel (Status/Files/Branches/Commits/Stash) |
| `?` | Help for current panel |
| `q` | Quit lazygit |
| `P` | Push |
| `p` | Pull |
| `f` | Fetch all |
| `x` | Options menu |
| `:` | Custom git command |
| `Tab` | Next panel |

### lazygit - Files panel

| Key | Action |
| --- | ------ |
| `space` | Stage / unstage |
| `a` | Stage all / unstage all |
| `Enter` | View diff / hunk staging |
| `c` | Commit (inline) |
| `Shift+C` | Commit (editor) |
| `A` | Amend last commit |
| `d` | Discard changes |
| `s` | Stash |
| `e` | Open in editor |
| `i` | Add to .gitignore |

### lazygit - Branches panel

| Key | Action |
| --- | ------ |
| `space` | Checkout |
| `n` | New branch |
| `d` | Delete branch |
| `r` | Rebase onto |
| `M` | Merge into current |
| `f` | Fast-forward |
| `o` | Open PR |
| `Shift+G` | View PR in browser |
| `w` | Create worktree |

### lazygit - Commits panel

| Key | Action |
| --- | ------ |
| `Enter` | Expand commit |
| `i` | Interactive rebase from here |
| `p` | Pick (rebase action) |
| `r` | Reword |
| `s` | Squash |
| `f` | Fixup |
| `d` | Drop |
| `Ctrl+J`/`Ctrl+K` | Move commit down/up |
| `Shift+C` | Cherry-pick (copy) |
| `Shift+V` | Paste cherry-pick |
| `m` | Continue rebase/merge |

### tig - universal

| Key | Action |
| --- | ------ |
| `j`/`k` | Move down/up |
| `Enter` | Open/expand item |
| `q` | Close view |
| `Q` | Quit tig |
| `/` | Search |
| `n`/`N` | Next/previous match |
| `R` | Refresh |
| `?` | Help |

### tig - view shortcuts

| Key | View |
| --- | ---- |
| `m` | Main (commit log) |
| `d` | Diff |
| `f` | Blame |
| `s` | Status |
| `y` | Stash |
| `r` | Refs |
| `t` | Tree |
| `h` | Help |

### fzf - shell integration

| Key | Action |
| --- | ------ |
| `Ctrl+R` | Fuzzy history search |
| `Ctrl+T` | Paste file path |
| `Alt+C` | Fuzzy cd |
| `Tab` (in fzf) | Multi-select |
| `Enter` | Confirm selection |
| `Esc` | Cancel |
| `**Tab` | Fuzzy completion trigger |

---

## 5. Shell config snippets

### delta in .gitconfig

```ini
[core]
    pager = delta

[interactive]
    diffFilter = delta --color-only

[delta]
    navigate = true
    dark = true
    side-by-side = true
    line-numbers = true
    syntax-theme = TwoDark
```

### fzf shell integration

```bash
# bash - add to ~/.bashrc
eval "$(fzf --bash)"

# zsh - add to ~/.zshrc (after compinit)
eval "$(fzf --zsh)"

# fish
fzf --fish | source

# PowerShell
$env:FZF_DEFAULT_OPTS = '--height 40% --layout=reverse --border'
```

### starship shell integration

```bash
# bash
eval "$(starship init bash)"

# zsh (last line of ~/.zshrc)
eval "$(starship init zsh)"

# fish
starship init fish | source

# PowerShell
Invoke-Expression (&starship init powershell)
```

### zoxide shell integration

```bash
# bash (after PATH setup)
eval "$(zoxide init bash)"

# zsh (after compinit)
eval "$(zoxide init zsh)"

# fish
zoxide init fish | source

# PowerShell
Invoke-Expression (& { (zoxide init powershell | Out-String) })
```

### bat environment variables

```bash
export BAT_THEME="TwoDark"
export MANPAGER="sh -c 'col -bx | bat -l man -p'"
export MANROFFOPT="-c"
```

### lazygit + delta integration (in lazygit config.yml)

```yaml
git:
  paging:
    colorArg: always
    pager: delta --paging=never --dark
```

### Shell init ordering (zsh)

```zsh
# 1. Oh My Zsh (if using)
source $ZSH/oh-my-zsh.sh

# 2. compinit
autoload -U compinit && compinit

# 3. Tool inits (order matters)
eval "$(fzf --zsh)"
eval "$(zoxide init zsh)"

# 4. Environment variables
export BAT_THEME="TwoDark"

# 5. Plugin sources
source ~/.forgit/forgit.plugin.zsh

# 6. starship LAST (defines the prompt)
eval "$(starship init zsh)"
```

---

## 6. Choosing the right tool

### For a specific task

| Task | Best tool | Command |
| ---- | --------- | ------- |
| View a file with highlighting | bat | `bat file.py` |
| Stage files and commit | lazygit | `lazygit` |
| Stage specific hunks | lazygit or `git add -p` | lazygit → Enter → space |
| Interactive rebase (visual) | lazygit | Commits panel → i |
| Interactive rebase (text) | git | `git rebase -i HEAD~N` |
| Browse commit history | tig | `tig` |
| Blame a file | tig | `tig blame file.py` |
| Fuzzy checkout a branch | fzf function | `gcb` |
| Fuzzy log browse | forgit | `glo` |
| View diff with syntax highlighting | delta | `git diff` (configured) |
| Jump to a repository | zoxide | `z repo-name` |
| Check Git status in prompt | starship | automatic |
| Large repo TUI | gitui | `gitui` |
| Man pages with highlighting | bat | `man git-diff` (configured) |

### lazygit vs gitui

| Situation | Use |
| --------- | --- |
| General daily Git work | lazygit |
| Very large repository (100k+ commits) | gitui |
| Need interactive rebase UI | lazygit |
| Need GitHub PR integration | lazygit |
| Windows with delta in diffs | gitui (built-in highlighting) |
| Prefer YAML config | lazygit |
| Prefer Rust/RON config | gitui |

### delta vs diff-so-fancy

| Situation | Use |
| --------- | --- |
| New setup, want best output | delta |
| Cannot install Rust binaries | diff-so-fancy |
| Want side-by-side view | delta |
| Want syntax highlighting | delta |
| Simplest possible install | diff-so-fancy |

---

## 7. Prompt symbols reference

| Symbol | Shell / context | Meaning |
| ------ | --------------- | ------- |
| `$` | bash, zsh (normal user) | Ready for input |
| `#` | bash, zsh (root) | Ready for input as root - be careful |
| `%` | zsh (some configs) | Ready for input (zsh default) |
| `>>>` | Python REPL | Inside Python, not your shell - type `exit()` |
| `PS>` | PowerShell | Ready for input in PowerShell |
| `>` | cmd.exe | Ready for input in Command Prompt |
| `⇡3` | starship | 3 commits ahead of remote |
| `⇣2` | starship | 2 commits behind remote |
| `!2` | starship | 2 modified files |
| `+1` | starship | 1 staged file |
| `?3` | starship | 3 untracked files |
| `$` | starship | stash exists |
| `=` | starship | merge conflicts |

---

## 8. Danger levels at a glance

Commands that can cause permanent data loss - review [04-terminal-safety.md](04-terminal-safety.md) for full details.

| Command | Danger | What is lost | Recovery |
| ------- | ------ | ------------ | -------- |
| `git reset --hard` | High | Uncommitted changes | None (reflog saves commits only) |
| `git clean -fdx` | High | Untracked and ignored files | None |
| `git clean -fd` | Medium | Untracked files | None |
| `git restore .` | High | Unstaged changes | None |
| `git push --force` | High | Remote commits (others' work) | Reflog on colleagues' machines |
| `git push --force-with-lease` | Low | Remote commits (if others pushed) | Ask teammate |
| `git branch -D` | Low | Branch pointer (commits stay in reflog) | Reflog for 30 days |
| `rm -rf` | Extreme | Files on disk | None (no Trash) |
| `git stash drop` | Medium | Stash contents | Reflog may help |

**Safety habits summary**:
- Stash before `reset --hard`
- Dry run before `clean`: `git clean -n`
- Use `--force-with-lease` not `--force`
- Never force push to `main`/`develop`
- Never embed API keys in commands (shell history)

---

## 9. Sources and further reading

### Official documentation

- [Git documentation](https://git-scm.com/doc)
- [delta documentation](https://dandavison.github.io/delta/)
- [lazygit documentation](https://github.com/jesseduffield/lazygit/tree/master/docs)
- [fzf documentation](https://github.com/junegunn/fzf#readme)
- [bat documentation](https://github.com/sharkdp/bat#readme)
- [tig manual](https://jonas.github.io/tig/doc/manual.html)
- [gitui documentation](https://github.com/gitui-org/gitui)
- [Starship documentation](https://starship.rs/config/)
- [zoxide documentation](https://github.com/ajeetdsouza/zoxide#readme)
- [forgit documentation](https://github.com/wfxr/forgit#readme)

### Shell documentation

- [bash reference manual](https://www.gnu.org/software/bash/manual/bash.html)
- [zsh documentation](https://zsh.sourceforge.io/Doc/)
- [fish documentation](https://fishshell.com/docs/current/)
- [PowerShell documentation](https://learn.microsoft.com/en-us/powershell/)
- [WSL documentation](https://learn.microsoft.com/en-us/windows/wsl/)

### Related course files

- [git/21-advanced-config.md](../02-git/21-advanced-config.md) - Git configuration deep dive
- [git/18-hooks.md](../02-git/18-hooks.md) - Git hooks for automation
- [git/12-git-workflows.md](../02-git/12-git-workflows.md) - Branching strategies
- [git/13-rebase.md](../02-git/13-rebase.md) - Rebase in depth
- [terminal/08-lazygit.md](08-lazygit.md) - lazygit in depth

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
