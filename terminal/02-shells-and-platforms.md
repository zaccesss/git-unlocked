# Shells and Platforms

**Difficulty:** 🟢 Beginner | **Time:** 35 minutes

This file covers every major shell you will encounter across Windows, Mac and Linux - what each one is, how to set it up, where its config files live, and how to make it work well for daily Git use. It also covers WSL2, which lets Windows users run a full Linux shell environment natively.

---

## Table of Contents

1. [bash - the universal standard](#1-bash---the-universal-standard)
2. [zsh - the modern Mac default](#2-zsh---the-modern-mac-default)
3. [fish - the friendly shell](#3-fish---the-friendly-shell)
4. [PowerShell - the Windows professional](#4-powershell---the-windows-professional)
5. [cmd.exe - the Windows legacy shell](#5-cmdexe---the-windows-legacy-shell)
6. [WSL2 - Linux on Windows](#6-wsl2---linux-on-windows)
7. [Choosing a shell](#7-choosing-a-shell)
8. [Try It Yourself](#8-try-it-yourself)
9. [Common Mistakes](#9-common-mistakes)
10. [Summary](#10-summary)
11. [Sources](#11-sources)

---

## 1. bash - the universal standard

**bash** (Bourne Again Shell) was written by Brian Fox for the GNU project and first released in 1989. It is the default shell on virtually every Linux distribution and was the default on macOS until 2019. It is named as a pun on the original Bourne shell (`sh`) from 1979.

Current version: **bash 5.3** (July 2025). macOS ships the older bash 3.2.57 at `/bin/bash` for compatibility reasons - Apple froze on 3.2 when bash switched to the GPLv3 licence. This is why Mac developers often install a newer bash via Homebrew, though most use zsh instead.

### Installing bash

🍎 **macOS** - install a current version:

```bash
brew install bash
# Add to allowed shells
echo "$(brew --prefix)/bin/bash" | sudo tee -a /etc/shells
# Change to it (optional - most Mac developers use zsh)
chsh -s "$(brew --prefix)/bin/bash"
```

🐧 **Linux** - already installed; update via package manager if needed:

```bash
# Ubuntu/Debian
sudo apt update && sudo apt install --only-upgrade bash

# Fedora
sudo dnf upgrade bash
```

🪟 **Windows** - bash is available via WSL2 (the recommended approach, covered in section 6) or via Git for Windows, which bundles Git Bash - a minimal bash environment from MSYS2.

### Config files

bash config is split across multiple files and which one bash reads depends on how it was started. This is one of the most confusing parts of bash for beginners.

**Login shell** (started at TTY login, via SSH, or with `bash -l`):
1. `/etc/profile` (system-wide)
2. First existing of: `~/.bash_profile`, `~/.bash_login`, `~/.profile`

**Interactive non-login shell** (each new terminal tab on Linux):
1. `/etc/bash.bashrc` (Debian) or `/etc/bashrc` (RHEL/Fedora)
2. `~/.bashrc`

**Non-interactive shell** (scripts):
- Only reads `$BASH_ENV` if set

The practical consequence: if you add an alias to `~/.bashrc` but your terminal opens login shells, the alias will not be available. The standard fix is to add this to `~/.bash_profile`:

```bash
# Load .bashrc for interactive settings in login shells too
[ -f ~/.bashrc ] && . ~/.bashrc
```

> [!NOTE]
> macOS Terminal.app (pre-Catalina) opened a login shell per tab, reading `~/.bash_profile` but not `~/.bashrc` by default. If you are on an older Mac and wonder why aliases from `~/.bashrc` are missing, this is why.

### A minimal bash setup for Git

```bash
# ~/.bashrc

# PATH - add your tools
export PATH="$HOME/.local/bin:$PATH"

# History settings
export HISTSIZE=100000
export HISTFILESIZE=200000
export HISTCONTROL=ignoreboth:erasedups  # skip space-prefixed commands and duplicates
export HISTTIMEFORMAT="%F %T "
shopt -s histappend    # append to history file, don't overwrite
PROMPT_COMMAND='history -a'  # save every command immediately

# Safety aliases
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

# Git shortcuts (full aliases covered in 06-git-aliases.md)
alias gs='git status --short --branch'
alias gl='git log --oneline --graph --all -20'
```

---

## 2. zsh - the modern Mac default

**zsh** (Z shell) was created by Paul Falstad at Princeton in 1990 and is MIT-licensed. Apple switched to zsh as the macOS default at macOS Catalina (October 2019) - primarily to avoid bash's GPLv3 licence. Current version in most distros is **zsh 5.9** (May 2022).

zsh is bash-compatible for interactive use while adding many improvements: better tab completion, spelling correction, shared command history across sessions, powerful prompt customisation via `PROMPT` variables, and a built-in plugin system via `autoload`.

### Config files

zsh has more config files than bash. The load order:

1. `/etc/zshenv` then `~/.zshenv` - always read, every zsh invocation (interactive or not)
2. `/etc/zprofile` then `~/.zprofile` - login shells only, read before `.zshrc`
3. `/etc/zshrc` then `~/.zshrc` - interactive shells
4. `/etc/zlogin` then `~/.zlogin` - login shells, after `.zshrc`

**In practice**: put environment variables and PATH in `~/.zprofile`, and everything else (aliases, functions, prompt, plugin loading) in `~/.zshrc`.

> [!CAUTION]
> macOS-specific PATH trap: macOS Terminal.app opens a login shell per tab, which runs `/etc/zprofile`, which calls `path_helper`. If you put PATH exports in `~/.zshenv`, `path_helper` in `/etc/zprofile` will run after them and can reorder or override your PATH. **Always put macOS PATH exports in `~/.zprofile`**, not `~/.zshenv`.

### Oh My Zsh

Oh My Zsh is the most popular framework for managing zsh configuration. It provides hundreds of plugins and themes and makes zsh much more approachable. Created by Robby Russell in 2009.

Install:

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

After installation, your `~/.zshrc` is managed by Oh My Zsh. Key settings to add:

```zsh
# ~/.zshrc (Oh My Zsh managed)

# Theme (try: robbyrussell, agnoster, af-magic, or powerlevel10k)
ZSH_THEME="robbyrussell"

# Plugins - add to this list
plugins=(git docker kubectl zsh-autosuggestions zsh-syntax-highlighting)

# History settings
HISTSIZE=100000
SAVEHIST=100000
setopt HIST_IGNORE_SPACE     # skip commands starting with space
setopt HIST_IGNORE_ALL_DUPS  # remove duplicate entries
setopt SHARE_HISTORY         # share history across sessions
setopt INC_APPEND_HISTORY    # write immediately, not on exit
```

The `git` plugin (bundled with Oh My Zsh) provides 150+ git aliases. Run `alias | grep "^g"` to see them all. Most useful: `gst` (status), `gco` (checkout), `gcmsg` (commit -m), `gp` (push), `gl` (log).

**Two plugins to install separately** - these are not bundled:

```bash
# zsh-autosuggestions: shows completions from history as you type (press → to accept)
git clone https://github.com/zsh-users/zsh-autosuggestions \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# zsh-syntax-highlighting: colours commands as you type (green = valid, red = not found)
git clone https://github.com/zsh-users/zsh-syntax-highlighting \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

Then add both to your `plugins=(...)` list in `~/.zshrc`.

### Setting zsh as your default shell

🍎 **macOS** - already the default since Catalina. If you need to reset:

```bash
chsh -s /bin/zsh
```

🐧 **Linux** - install then set as default:

```bash
sudo apt install zsh        # Ubuntu/Debian
sudo dnf install zsh        # Fedora
chsh -s "$(which zsh)"      # set as default (takes effect on next login)
```

🪟 **Windows** - available inside WSL2 (see section 6). Not natively available in Windows without WSL.

---

## 3. fish - the friendly shell

**fish** (Friendly Interactive SHell) takes a different approach: instead of requiring you to configure everything yourself, it ships with autosuggestions, syntax highlighting and smart completions built in, with zero setup. Created in 2005, current version: **fish 4.0.6** (April 2026). Fish 4.0 (February 2025) rewrote the core from C++ to Rust.

fish is excellent for interactive use. The important trade-off to understand: **fish is not POSIX-compliant**. Its syntax differs from bash and zsh:

| bash / zsh | fish |
| ---------- | ---- |
| `export VAR=value` | `set -gx VAR value` |
| `if [ condition ]; then` | `if condition` |
| `function name() { }` | `function name; end` |
| `FOO=bar command` | Not supported natively |
| `source ~/.bashrc` | `source ~/.config/fish/config.fish` |

This means most shell scripts found online (install scripts, environment setup scripts, `.profile` fragments) will not run directly in fish. For those, you temporarily switch to bash: `bash script.sh`.

### Installing fish

🍎 **macOS**:

```bash
brew install fish
echo "$(brew --prefix)/bin/fish" | sudo tee -a /etc/shells
chsh -s "$(brew --prefix)/bin/fish"
```

🐧 **Linux**:

```bash
# Ubuntu/Debian
sudo apt install fish

# Fedora
sudo dnf install fish

# Arch
sudo pacman -S fish
```

🪟 **Windows** - inside WSL2:

```bash
sudo apt install fish
chsh -s "$(which fish)"
```

### Config files

fish config lives at:

| Platform | Path |
| -------- | ---- |
| 🐧 Linux | `~/.config/fish/config.fish` |
| 🍎 macOS | `~/.config/fish/config.fish` |
| 🪟 Windows (WSL) | `~/.config/fish/config.fish` |

Functions live in `~/.config/fish/functions/` (one file per function, named after the function). Completions in `~/.config/fish/completions/`. Fish also has a web-based config interface - run `fish_config` in the terminal to open it in your browser.

```fish
# ~/.config/fish/config.fish

# PATH
fish_add_path $HOME/.local/bin

# Suppress greeting
set -g fish_greeting ""

# History - fish automatically ignores commands starting with a space
# No extra config needed for HISTIGNORE

# Git abbreviations (fish uses abbreviations, not aliases)
abbr -a gs 'git status --short --branch'
abbr -a gl 'git log --oneline --graph --all -20'
abbr -a gp 'git push'
abbr -a gpl 'git pull'
```

**fish's secret history feature**: any command starting with a space is automatically excluded from history - no configuration needed. This is useful for one-off commands involving secrets.

---

## 4. PowerShell - the Windows professional

Windows has two separate PowerShell products that coexist on the same machine:

**Windows PowerShell 5.1** - built into Windows, `.NET Framework` based, Windows-only. Command is `powershell.exe`. Still supported with security fixes but no new features. The default in Windows 11.

**PowerShell 7+** - cross-platform (Windows, macOS, Linux), open source (MIT), `.NET` based. Command is `pwsh.exe` (Windows) or `pwsh` (macOS/Linux). Current stable: **PowerShell 7.6.0 LTS** (March 2026), supported until November 2028. Install alongside Windows PowerShell - they do not replace each other.

Install PowerShell 7 on Windows:

```powershell
winget install Microsoft.PowerShell
```

Install on macOS or Linux:

```bash
# macOS
brew install --cask powershell

# Ubuntu
sudo apt install powershell

# Or via the Microsoft package repo for other distros
```

### Profile files

PowerShell has **four profile files** per installation - scoped by user and host:

| Scope | Windows path | macOS/Linux path |
| ----- | ------------ | ---------------- |
| Current user, current host | `$env:USERPROFILE\Documents\PowerShell\Microsoft.PowerShell_profile.ps1` | `~/.config/powershell/Microsoft.PowerShell_profile.ps1` |
| Current user, all hosts | `$env:USERPROFILE\Documents\PowerShell\profile.ps1` | `~/.config/powershell/profile.ps1` |
| All users, current host | `$env:ProgramFiles\PowerShell\7\Microsoft.PowerShell_profile.ps1` | `/usr/local/share/powershell/Microsoft.PowerShell_profile.ps1` |
| All users, all hosts | `$env:ProgramFiles\PowerShell\7\profile.ps1` | varies |

`$PROFILE` expands to the current user, current host path - the one you edit for personal settings.

> [!CAUTION]
> **OneDrive trap on Windows**: if OneDrive's Known Folder Move is enabled, your `Documents` folder is redirected to `C:\Users\username\OneDrive\Documents`. This silently moves `$PROFILE` to a OneDrive path, which can cause issues with slow syncing and path confusion. Always verify with `$PROFILE` in PowerShell before editing.

Create and open your profile:

```powershell
# Create if it does not exist
if (!(Test-Path $PROFILE)) {
    New-Item -ItemType File -Path $PROFILE -Force
}
# Open in VS Code
code $PROFILE
```

### A minimal PowerShell profile for Git

```powershell
# $PROFILE

# PSReadLine configuration
Import-Module PSReadLine
Set-PSReadLineOption -PredictionSource History
Set-PSReadLineOption -HistoryNoDuplicates
Set-PSReadLineOption -MaximumHistoryCount 50000
Set-PSReadLineOption -EditMode Vi  # optional: vi-style editing

# Filter sensitive commands from history
Set-PSReadLineOption -AddToHistoryHandler {
    param([string]$line)
    $sensitive = 'password|asplaintext|token|apikey|secret|bearer'
    return ($line -notmatch $sensitive)
}

# Git aliases (Git must be on PATH)
function gs { git status --short --branch }
function gl { git log --oneline --graph --all -20 @args }
function gp { git push @args }
function gpl { git pull @args }

# Starship prompt (if installed)
Invoke-Expression (&starship init powershell)
```

> [!WARNING]
> `Clear-History` in PowerShell clears the in-session history list but **does not delete the PSReadLine history file**. To truly clear history: `Remove-Item (Get-PSReadLineOption).HistorySavePath`. This is a common surprise.

### PowerShell execution policy

By default, PowerShell blocks running scripts. You will likely need to change this for profile files and Git hooks:

```powershell
# See current policy
Get-ExecutionPolicy -List

# Allow user-written scripts (recommended minimum)
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```

---

## 5. cmd.exe - the Windows legacy shell

`cmd.exe` (Command Prompt) has shipped with every version of Windows since NT in 1993. It handles `.bat` and `.cmd` scripts and is still used in legacy enterprise environments, CI/CD pipelines, recovery environments and situations where PowerShell execution policy blocks scripts.

For Git work, cmd.exe is mostly adequate but lacks many quality-of-life features. Key limitations: no persistent history between sessions by default, primitive scripting, no real functions, no arrays, path handling quirks, and echo by default on every command in batch files (suppress with `@echo off`).

**When you will encounter cmd.exe**:
- Opening a terminal via `Win+R → cmd`
- Batch scripts (`.bat`, `.cmd` files)
- Some legacy CI/CD runners
- Visual Studio's Developer Command Prompt

**Git in cmd.exe**: Git for Windows adds `C:\Program Files\Git\cmd` to the system PATH, so `git` commands work in cmd.exe. However, Git Bash (the bash environment bundled with Git for Windows) is usually a better choice for Git operations on Windows, or WSL2.

---

## 6. WSL2 - Linux on Windows

WSL2 (Windows Subsystem for Linux 2) runs a real Linux kernel inside a lightweight virtual machine on Windows. Unlike WSL1 (which translated Linux syscalls to Windows syscalls), WSL2 runs a genuine Linux kernel, giving full compatibility with Linux tools including Docker, systemd, FUSE filesystems and eBPF.

This means Windows users can run bash, zsh, fish, lazygit, delta, fzf and every other Linux-native tool with no compromise.

### Installing WSL2

Open PowerShell as Administrator and run:

```powershell
wsl --install
```

This single command enables WSL, installs the Linux kernel, and installs Ubuntu. Restart when prompted.

After restart, Ubuntu opens and asks you to create a username and password. This is your Linux user account - it does not need to match your Windows username.

To install a specific distribution:

```powershell
# See available distributions
wsl --list --online

# Install a specific one
wsl --install -d Ubuntu-24.04
```

### Setting up a productive WSL2 environment

After installation, update the system and install essential tools:

```bash
sudo apt update && sudo apt full-upgrade -y
sudo apt install -y git curl wget build-essential
```

Install zsh and Oh My Zsh:

```bash
sudo apt install -y zsh
chsh -s "$(which zsh)"   # set zsh as default (takes effect on next WSL restart)

# Install Oh My Zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Install autosuggestions and syntax-highlighting plugins
git clone https://github.com/zsh-users/zsh-autosuggestions \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

git clone https://github.com/zsh-users/zsh-syntax-highlighting \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

Add both plugins to `~/.zshrc`:

```zsh
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
```

### Connecting Windows Terminal to WSL2

Windows Terminal automatically creates a profile for your WSL distributions. To make Ubuntu your default terminal:

1. Open Windows Terminal Settings (`Ctrl+,`)
2. Go to **Startup**
3. Set **Default profile** to your Ubuntu distribution
4. Click Save

In the JSON settings file (`settings.json`), this looks like:

```json
"defaultProfile": "{profile-guid-for-ubuntu}"
```

Set the starting directory to your Linux home (not the Windows filesystem):

```json
{
    "startingDirectory": "//wsl.localhost/Ubuntu-24.04/home/your_username"
}
```

### Nerd Fonts for icons in the prompt

Tools like Starship and Oh My Zsh themes use special icon characters from Nerd Fonts. The font must be installed on **the Windows side** because Windows Terminal renders fonts.

```powershell
# In Windows PowerShell - install JetBrainsMono Nerd Font
winget install -e --id DEVCOM.JetBrainsMonoNerdFont
```

Or download any Nerd Font from [nerdfonts.com](https://nerdfonts.com) and install. Then set it in Windows Terminal: Settings → your Ubuntu profile → Appearance → Font face → JetBrainsMono Nerd Font.

### Using VS Code with WSL2

Install the **WSL extension** in VS Code on Windows. Then from inside a WSL terminal, navigate to your project and type:

```bash
code .
```

VS Code installs a server component inside WSL and connects to it. The terminal inside VS Code is now a real Linux shell, file watching works without configuration, and VS Code uses the Git binary inside WSL.

> [!IMPORTANT]
> Keep your code in the Linux filesystem (`~/projects/`, not `/mnt/c/`). File operations across the WSL-Windows boundary (`/mnt/c/...`) are significantly slower because they go through a 9P network protocol. Git operations on a repo stored in the Windows filesystem from WSL can be very slow on large repositories.

### The WSL2 filesystem explained

WSL2 uses three filesystem locations:

| Location | What it is | Speed | Use for |
| -------- | ---------- | ----- | ------- |
| `~` (e.g. `/home/username`) | Native Linux ext4 filesystem | Fast | Code, Git repos, tools |
| `/mnt/c/`, `/mnt/d/` etc. | Windows drives accessed via 9P | Slow | Accessing Windows files when needed |
| `\\wsl.localhost\Ubuntu-24.04\...` | Windows UNC path to Linux filesystem | Moderate | Opening Linux files in Windows apps |

### Advanced WSL2 configuration

Create `%UserProfile%\.wslconfig` on the Windows side to control WSL2 resource limits and networking:

```ini
[wsl2]
memory=8GB
processors=4
# Mirrored networking: WSL2 and Windows share the same IP address
# This simplifies VPN compatibility and localhost access (Windows 11 + WSL2 2.0+)
networkingMode=mirrored
dnsTunneling=true
autoProxy=true

[experimental]
autoMemoryReclaim=gradual
sparseVhd=true
```

Apply changes: `wsl --shutdown` then reopen WSL.

Per-distribution settings go in `/etc/wsl.conf` inside the WSL distro:

```ini
[boot]
systemd=true    # enable systemd (WSL2 + Windows 11)

[user]
default=your_username

[interop]
appendWindowsPath=false  # prevent Windows PATH from leaking into WSL (speeds up shell startup)
```

---

## 7. Choosing a shell

| Shell | Best for | Avoid if |
| ----- | -------- | -------- |
| **bash** | Linux servers, scripts, portability, WSL | You want modern interactive features without plugins |
| **zsh + Oh My Zsh** | macOS daily driver, rich completion, extensive plugins | You want simplicity or fast startup |
| **fish** | Beginners wanting features out of the box, friendly interactive use | You run a lot of bash scripts or need POSIX compliance |
| **PowerShell 7** | Windows development, .NET, Azure, cross-platform scripting | You prefer Unix-style pipelines |
| **cmd.exe** | Running legacy `.bat` scripts, enterprise tools that require it | Everything else |
| **WSL2** | Windows users who want the full Linux toolchain | You cannot or do not want to enable virtualisation |

Most developers on Mac use zsh (it is the default). Most developers on Linux use bash or zsh. Most developers on Windows who do serious development use WSL2 with bash or zsh inside it.

---

## 8. Try It Yourself

**Exercise 1 - find your shell config file**

Identify which shell you are using (`echo $SHELL` on Mac/Linux, `$PSVersionTable.PSEdition` in PowerShell) and locate its config file. Open it in your editor and read through it. What PATH entries are set? Are there any aliases?

**Exercise 2 - add a Git alias to your shell config**

Add this line to your shell config file (`.bashrc`, `.zshrc`, `config.fish` or `$PROFILE`):

```bash
# bash/zsh
alias gs='git status --short --branch'

# fish
abbr -a gs 'git status --short --branch'

# PowerShell
function gs { git status --short --branch }
```

Reload your config (`source ~/.zshrc` or open a new terminal), navigate to a Git repo and run `gs`. Verify it works.

**Exercise 3 - WSL2 setup (Windows users)**

If you are on Windows, install WSL2 with `wsl --install`. After it sets up, run `git --version` inside WSL to confirm Git is available. Then run `echo $SHELL` to see which shell you have. Install zsh and Oh My Zsh following the steps in section 6.

**Exercise 4 - check your history settings**

In bash or zsh, run `history | tail -20` to see recent commands. Then check `echo $HISTSIZE` to see your history limit. Add the history settings from section 1 or 2 to your config file to improve them.

**Exercise 5 - explore your shell config load order**

In bash, add `echo "Loading .bash_profile"` to `~/.bash_profile` and `echo "Loading .bashrc"` to `~/.bashrc`. Open a new terminal and observe which message(s) appear. Does a new tab load the same files? This reveals which files your terminal opens as login vs non-login shells.

---

## 9. Common Mistakes

Editing the wrong config file is one of the most common issues. Adding aliases to `~/.bash_profile` when your terminal opens non-login shells (typical on Linux) means they are never loaded. Adding them to `~/.zshenv` on macOS means `path_helper` can override your PATH. When in doubt: on Linux, use `~/.bashrc` or `~/.zshrc`. On macOS with zsh, use `~/.zshrc` for aliases and `~/.zprofile` for PATH.

Forgetting to reload the config after editing it. Changes to `~/.zshrc` or `~/.bashrc` take effect only when the shell starts or when you explicitly reload: `source ~/.zshrc` or `. ~/.bashrc`. Opening a new terminal tab also reloads. Without reloading, your new aliases and settings will not appear.

On Windows, using the wrong shell for the task. PowerShell and cmd.exe have different syntax for almost everything. `$env:PATH` is PowerShell; `%PATH%` is cmd. `Get-ChildItem` is PowerShell; `dir` is cmd. If a Git tutorial gives a command that does not work, check whether it was written for bash/PowerShell/cmd.

Storing code in `/mnt/c/` in WSL2. This is the single most common WSL2 performance mistake. Keep your repositories in the Linux filesystem (`~/projects/`). A `git status` on a large repo in `/mnt/c/` can take several seconds; the same command in the Linux filesystem takes milliseconds.

Installing tools inside WSL but running them from Windows PowerShell. Tools installed in WSL (like lazygit, delta, fzf) are not available in Windows PowerShell or cmd.exe. They only run inside a WSL terminal. For Windows-native tools, install them via winget or scoop in PowerShell.

---

## 10. Summary

bash is the universal Linux shell and macOS legacy default, with config split between login (`.bash_profile`) and interactive (`.bashrc`) files. The standard fix for macOS is to source `.bashrc` from `.bash_profile`. zsh is the macOS default since 2019, with Oh My Zsh providing plugins and themes; put aliases in `~/.zshrc` and PATH in `~/.zprofile` on Mac. fish ships features like autosuggestions and syntax highlighting with zero setup but is not POSIX-compliant; any command starting with a space is automatically hidden from its history. PowerShell 7 is the modern cross-platform Windows shell; `$PROFILE` points to your config file, and `Clear-History` does not clear the PSReadLine history file. cmd.exe is the Windows legacy shell, still used for `.bat` scripts and enterprise tools.

WSL2 runs a real Linux kernel on Windows and is the recommended way for Windows developers to get the full Linux toolchain. Keep code in the Linux filesystem, not in `/mnt/c/`. Use Windows Terminal with a Nerd Font, install zsh and Oh My Zsh inside WSL, and connect VS Code via the WSL extension with `code .`.

---

## 11. Sources

- [bash manual - GNU](https://www.gnu.org/software/bash/manual/bash.html)
- [zsh documentation](https://zsh.sourceforge.io/Doc/)
- [Oh My Zsh](https://ohmyz.sh)
- [fish shell documentation](https://fishshell.com/docs/current/)
- [PowerShell documentation - Microsoft](https://learn.microsoft.com/en-us/powershell/)
- [WSL documentation - Microsoft](https://learn.microsoft.com/en-us/windows/wsl/)
- [WSL2 complete guide - KX](https://kx.cloudingenium.com/en/wsl2-windows-linux-development-environment)
- [Production-grade WSL2 setup](https://faranheit.medium.com/making-windows-disappear-a-linux-developers-guide-to-a-production-grade-wsl2-setup-ae43473e12e8)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>
