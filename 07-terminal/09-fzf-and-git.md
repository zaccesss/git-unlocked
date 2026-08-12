# fzf and Git

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

fzf is a command-line fuzzy finder. You pipe a list of anything into it, type a few characters and it instantly filters the list to matching items. You select one and press Enter and fzf returns it for use in another command. For Git, this means fuzzy-searching through branches, commits, stashes, files and command history - turning long-typed names into two-character selections. Combined with git aliases and shell functions, fzf transforms the command-line Git experience.

---

## Table of Contents

1. [What fzf is and how it works](#1-what-fzf-is-and-how-it-works)
2. [Installing fzf](#2-installing-fzf)
3. [Shell integration - the three key bindings](#3-shell-integration---the-three-key-bindings)
4. [fzf and Git - branches](#4-fzf-and-git---branches)
5. [fzf and Git - commits](#5-fzf-and-git---commits)
6. [fzf and Git - files and stashes](#6-fzf-and-git---files-and-stashes)
7. [Git aliases powered by fzf](#7-git-aliases-powered-by-fzf)
8. [forgit - a ready-made fzf-git plugin](#8-forgit---a-ready-made-fzf-git-plugin)
9. [fzf-git.sh - the author's own integration](#9-fzf-gitsh---the-authors-own-integration)
10. [Try It Yourself](#10-try-it-yourself)
11. [Common Mistakes](#11-common-mistakes)
12. [Summary](#12-summary)
13. [Sources](#13-sources)

---

## 1. What fzf is and how it works

fzf reads lines from stdin, opens a fuzzy search interface and writes the selected line to stdout. That is its entire interface. Because it uses stdin and stdout, it composes with any command that produces or consumes text.

A minimal example:

```bash
# Pick a file from the current directory tree
find . -type f | fzf
```

fzf opens an interactive list of all files. Type characters to filter, press arrow keys or `j`/`k` to navigate, press Enter to select. The selected path is printed to stdout, where it can be passed to another command.

For Git, this means any list Git can produce - branches, commits, file paths, stash entries, remote names - becomes interactively searchable.

fzf's fuzzy matching is what makes it fast. You do not need to type the exact start of a name. `flg` matches `feature/login`, `flgbug` matches `feature/login-bugfix`. Characters are matched in order but do not need to be adjacent.

---

## 2. Installing fzf

🍎 **macOS**:

```bash
brew install fzf
# Run the post-install script to set up shell integration
$(brew --prefix)/opt/fzf/install
```

🐧 **Linux**:

```bash
# Debian/Ubuntu
sudo apt install fzf

# Fedora
sudo dnf install fzf

# Arch
sudo pacman -S fzf

# Any Linux - direct from GitHub (always current version)
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```

🪟 **Windows**:

```powershell
winget install junegunn.fzf
# or
scoop install fzf
# or
choco install fzf
```

> [!NOTE]
> The distro package for fzf may be significantly older than the current release. For the freshest version, use the GitHub install script (`~/.fzf/install`) or Homebrew. The GitHub script both installs the binary and sets up shell integration.

---

## 3. Shell integration - the three key bindings

The `fzf --bash`, `fzf --zsh` and `fzf --fish` shell integration scripts add three key bindings and a fuzzy completion trigger.

Add to your shell config:

```bash
# bash - add to ~/.bashrc
eval "$(fzf --bash)"

# zsh - add to ~/.zshrc (after compinit)
eval "$(fzf --zsh)"

# fish - add to ~/.config/fish/config.fish
fzf --fish | source
```

The three key bindings this enables:

**`Ctrl+R`** - fuzzy search your shell history. This replaces the default reverse-i-search in bash/zsh with an fzf-powered list of all your history. Type to filter, press Enter to put the command on the prompt (not run it immediately).

**`Ctrl+T`** - paste a file or directory path onto the command line. An fzf window opens showing all files under the current directory. Select one and its path is pasted where your cursor was.

**`Alt+C`** - fuzzy `cd`. An fzf window shows all directories; select one to cd into it. On macOS, `Alt+C` may need to be configured as `Option+C` depending on your terminal settings.

**Fuzzy completion with `**`**: in bash and zsh, type a command prefix and `\*\*` then press Tab to invoke fzf completion:

```bash
vim **<Tab>         # fzf to choose a file for vim
cd **<Tab>          # fzf to choose a directory
git checkout **<Tab>  # fzf to choose a branch
ssh **<Tab>         # fzf to choose a hostname from ~/.ssh/config
kill -9 **<Tab>     # fzf to choose a running process
```

---

## 4. fzf and Git - branches

Fuzzy branch checkout is one of the highest-value fzf-git integrations. Instead of typing `git checkout feature/auth-service-oauth-integration`, you type `gcb<Enter>` and type a few characters.

**Shell function for fuzzy branch checkout**:

```bash
# Add to ~/.zshrc or ~/.bashrc
gcb() {
    local branch
    branch=$(git branch --all --sort=-committerdate \
        | grep -v HEAD \
        | sed 's/remotes\/origin\///' \
        | sort -u \
        | fzf \
            --height 40% \
            --preview 'git log --oneline --graph --date=short --color=always \
                --pretty="format:%C(auto)%cd %h%d %s" {1} | head -20' \
            --preview-window right:60%)
    [ -n "$branch" ] && git switch "$(echo "$branch" | sed 's/^ *//')"
}
```

This shows all local and remote branches sorted by most recently committed, with a live preview of the branch's recent commits on the right side as you filter. Select and press Enter to switch.

**Simpler version without preview**:

```bash
gcb() {
    local branch
    branch=$(git branch --sort=-committerdate | fzf --height 40%)
    [ -n "$branch" ] && git switch "$(echo "$branch" | sed 's/^[* ]*//')"
}
```

---

## 5. fzf and Git - commits

**Fuzzy log search**:

```bash
# Browse commits with a diff preview
fgl() {
    git log --oneline --all --color=always \
        | fzf \
            --ansi \
            --no-sort \
            --preview 'echo {} | grep -o "[a-f0-9]\{7,\}" | head -1 | xargs git show --color=always | head -100' \
            --preview-window right:60% \
        | grep -o '[a-f0-9]\{7,\}'
}
```

Run `fgl` and type to filter commits by message. The preview shows the diff for the highlighted commit. Press Enter to output the commit hash (useful for piping to `git show`, `git cherry-pick` or `git rebase -i`).

**Interactive rebase base selector**:

```bash
# Choose the base commit for an interactive rebase
gri() {
    local commit
    commit=$(git log --oneline --color=always \
        | fzf --ansi --preview 'git show --color=always {1}' \
        | awk '{print $1}')
    [ -n "$commit" ] && git rebase -i "$commit"
}
```

Run `gri`, select the commit just before where you want to start rebasing and the interactive rebase opens with that as the base.

---

## 6. fzf and Git - files and stashes

**Fuzzy diff view** - pick an unstaged file to view its diff:

```bash
gdf() {
    git diff --name-only \
        | fzf \
            --preview 'git diff --color=always {}' \
            --preview-window right:70%
}
```

**Fuzzy file add** - interactively choose which files to stage:

```bash
gfa() {
    local files
    files=$(git ls-files --modified --deleted --other --exclude-standard \
        | fzf -m \
            --preview 'git diff --color=always {}' \
            --preview-window right:70%)
    [ -n "$files" ] && echo "$files" | xargs git add
}
```

The `-m` flag enables **multi-select** in fzf. Press `Tab` to select multiple items, Enter to confirm.

**Fuzzy stash browser**:

```bash
fstash() {
    local out k sha
    while out=$(
        git stash list --pretty="%C(yellow)%h %>(14)%Cgreen%cr %C(blue)%gs" \
            | fzf --ansi --no-sort \
                --expect=ctrl-d,ctrl-b \
                --query="$1"
    ); do
        k=$(head -1 <<< "$out")
        sha=$(tail -1 <<< "$out" | awk '{print $1}')
        [ -z "$sha" ] && break
        if [ "$k" = 'ctrl-d' ]; then
            git diff "$sha"
        elif [ "$k" = 'ctrl-b' ]; then
            git stash branch "stash-$sha" "$sha"
            break
        else
            git stash show -p "$sha"
        fi
    done
}
```

Run `fstash`. Press Enter to view a stash's diff, `Ctrl+D` to see the diff against HEAD or `Ctrl+B` to create a branch from the stash.

---

## 7. Git aliases powered by fzf

These git aliases in `~/.gitconfig` use fzf for interactive selection:

```ini
[alias]
    # Fuzzy branch checkout
    fcb = "!f() { git switch $(git branch --all --sort=-committerdate | grep -v HEAD | sed 's/remotes\\/origin\\///' | sort -u | fzf --height 40%); }; f"

    # Fuzzy add - choose files to stage
    fa = "!f() { git ls-files --modified --deleted --other --exclude-standard | fzf -m --preview 'git diff --color=always {}' | xargs -r git add; }; f"

    # Fuzzy log - browse and output a commit hash
    fl = "!git log --oneline --all --color=always | fzf --ansi --no-sort --preview 'echo {} | grep -o \"[a-f0-9]\\{7,\\}\" | head -1 | xargs git show --color=always | head -80' | grep -o '[a-f0-9]\\{7,\\}'"
```

Use `git fa` instead of `git add` to get a fuzzy file picker with diff preview. Use `git fcb` instead of `git checkout` to get fuzzy branch selection.

---

## 8. forgit - a ready-made fzf-git plugin

forgit is a lazy-loaded shell plugin that wraps git commands with fzf, saving you from writing the functions yourself. Current version: **26.04.2** (April 2026). Requires fzf v0.49+.

### Installing forgit

🍎 **macOS**:

```bash
brew install forgit
# Add to ~/.zshrc:
[ -f $(brew --prefix)/share/forgit/forgit.plugin.zsh ] && \
    source $(brew --prefix)/share/forgit/forgit.plugin.zsh
```

**Oh My Zsh**:

```bash
git clone https://github.com/wfxr/forgit ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/forgit
# Add forgit to plugins=() in ~/.zshrc
```

**Manual**:

```bash
git clone https://github.com/wfxr/forgit ~/.forgit
# Add to ~/.zshrc or ~/.bashrc:
source ~/.forgit/forgit.plugin.zsh
# fish:
source ~/.forgit/forgit.plugin.fish
```

🪟 **Windows**: use inside WSL2 or Git Bash. No native cmd/PowerShell support.

### forgit commands

| Command  | Action                                                   |
| -------- | -------------------------------------------------------- |
| `ga`     | Interactive `git add` - pick files with diff preview     |
| `glo`    | Interactive `git log` - browse commits with diff preview |
| `gd`     | Interactive `git diff` - pick a file to diff             |
| `gcf`    | Interactive checkout file - pick a file to restore       |
| `gcb`    | Interactive checkout branch                              |
| `gsw`    | Interactive `git switch`                                 |
| `gco`    | Interactive checkout commit                              |
| `gbd`    | Interactive branch delete - multi-select                 |
| `gst`    | Interactive stash show                                   |
| `gsp`    | Interactive stash pop                                    |
| `gss`    | Interactive stash push                                   |
| `gclean` | Interactive `git clean` - preview before deleting        |
| `grh`    | Interactive `git reset HEAD` - unstage files             |
| `gcp`    | Interactive cherry-pick - multi-select commits           |
| `grb`    | Interactive rebase                                       |
| `gfu`    | Interactive fixup + autosquash                           |
| `grc`    | Interactive revert                                       |
| `gbl`    | Interactive blame                                        |

**Customisation**:

```bash
# Disable default aliases if they conflict with your own
export FORGIT_NO_ALIASES=1
# Then define your own:
export forgit_log=fglo
export forgit_add=fga

# Custom fzf options for forgit
export FORGIT_FZF_DEFAULT_OPTS="--height 60% --border"

# Use delta for diff previews
export FORGIT_DIFF_PAGER="delta --paging=never"
```

---

## 9. fzf-git.sh - the author's own integration

**fzf-git.sh** is a script by junegunn (the creator of fzf) providing polished Git-fzf integrations as key bindings. It adds `Ctrl+G` followed by a letter to fuzzy-search different Git objects:

| Key             | Action                               |
| --------------- | ------------------------------------ |
| `Ctrl+G Ctrl+F` | Fuzzy browse modified **files**      |
| `Ctrl+G Ctrl+B` | Fuzzy browse **branches**            |
| `Ctrl+G Ctrl+T` | Fuzzy browse **tags**                |
| `Ctrl+G Ctrl+R` | Fuzzy browse **remotes**             |
| `Ctrl+G Ctrl+H` | Fuzzy browse commit **hashes** (log) |
| `Ctrl+G Ctrl+S` | Fuzzy browse **stashes**             |
| `Ctrl+G Ctrl+E` | Fuzzy browse **each ref**            |
| `Ctrl+G Ctrl+W` | Fuzzy browse **worktrees**           |

Install:

```bash
# Clone anywhere
git clone https://github.com/junegunn/fzf-git.sh ~/.fzf-git

# Add to ~/.zshrc or ~/.bashrc
source ~/.fzf-git/fzf-git.sh

# fish
source ~/.fzf-git/fzf-git.fish
```

Unlike forgit which wraps full git commands, fzf-git.sh primarily works as shell key bindings that paste the selected value onto the command line. This makes it more composable - you can use the selected branch hash or filename as part of any command you are constructing.

---

## 10. Try It Yourself

**Exercise 1 - set up shell integration**

Add `eval "$(fzf --bash)"` (or the zsh/fish equivalent) to your shell config. Reload the config. Press `Ctrl+R` - you should see a fuzzy history search instead of the default reverse-i-search. Type a few characters to filter your history. Press Enter to paste a command onto the prompt.

**Exercise 2 - fuzzy checkout**

Add the `gcb` shell function from section 4 to your `~/.zshrc` or `~/.bashrc`. Reload. Navigate to a repository with several branches. Type `gcb` and press Enter. An fzf list of branches appears. Type a few characters to filter. Press Enter to switch to that branch.

**Exercise 3 - fuzzy interactive add**

Add the `gfa` function from section 6 to your shell config. Make changes to three files in a repository. Run `gfa`. An fzf list of modified files appears with a diff preview on the right. Press Tab to select multiple files. Press Enter to stage only the selected files. Verify with `git status`.

**Exercise 4 - install and use forgit**

Install forgit using one of the methods in section 8. Run `glo` in a repository with commit history. Browse commits with the preview on the right. Find a commit you are interested in and press Enter to inspect it.

**Exercise 5 - fuzzy log with hash output**

Add the `fgl` function from section 5 to your shell config. Run it. Select a commit. The hash is printed to stdout. Now use it in a command: `git show $(fgl)`. This runs `fgl`, waits for you to select a commit and passes its hash to `git show`.

---

## 11. Common Mistakes

Running `gcb` and finding it only shows local branches, missing remote branches you want to checkout. The function needs `--all` in the `git branch` call to include remote branches. The version in section 4 includes `--all`. Adjust if your function is missing it.

The `**<Tab>` fuzzy completion not working in zsh. This requires the fzf shell integration to be sourced and requires `_fzf_comprun` to be available. Make sure you have run the `fzf --zsh` eval in your `~/.zshrc` **after** `compinit`. Order matters.

forgit `ga` conflicting with the Oh My Zsh `git` plugin's `ga` alias (which maps to `git add`). Set `FORGIT_NO_ALIASES=1` before sourcing forgit if you want to keep the Oh My Zsh aliases and use custom names for forgit commands.

Using fzf inside a script or CI environment where there is no interactive terminal. fzf requires a TTY for its interactive interface. In scripts, use `--filter` mode instead: `echo "list" | fzf --filter="query"` which filters without the interactive UI and exits immediately with matching lines.

`Alt+C` not working on macOS. macOS Terminal.app and iTerm2 may intercept `Option+C` before it reaches the shell. In iTerm2, go to Profiles → Keys and set "Left Option key" to "Esc+". In Terminal.app, check Preferences → Profiles → Keyboard → "Use Option as Meta key".

---

## 12. Summary

fzf is a command-line fuzzy finder that accepts any list on stdin and returns the selected item on stdout, composing cleanly with Git and shell commands. Shell integration adds `Ctrl+R` (history search), `Ctrl+T` (file path paste) and `Alt+C` (fuzzy cd). For Git, fzf powers fuzzy branch checkout, fuzzy commit browsing with diff previews, fuzzy file staging with `-m` multi-select and fuzzy stash browsing. Write your own functions using `git branch | fzf` and `git log --oneline | fzf` as the pattern or use ready-made solutions: **forgit** wraps full git commands as shell aliases (`ga`, `glo`, `gd`, `gcb`, `gbd` and more) and **fzf-git.sh** by fzf's author adds `Ctrl+G` key bindings for interactive Git object selection. On Windows, fzf works natively; forgit requires WSL2 or Git Bash.

---

## 13. Sources

- [fzf repository](https://github.com/junegunn/fzf)
- [fzf wiki - Git examples](https://github.com/junegunn/fzf/wiki/examples#git)
- [forgit repository](https://github.com/wfxr/forgit)
- [fzf-git.sh repository](https://github.com/junegunn/fzf-git.sh)
- [Building Git helpers from scratch with fzf - Olivia Coumans](https://oliviac.dev/blog/build-git-helpers-bash-fzf/)
- [A Practical Guide to fzf: Building a Git Explorer](https://thevaluable.dev/fzf-git-integration/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>
